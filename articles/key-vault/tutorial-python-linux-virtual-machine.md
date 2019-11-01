---
title: Руководство. Использование виртуальной машины Linux и приложения Python для хранения секретов в Azure Key Vault | Документация Майкрософт
description: Из этого руководства вы узнаете, как настроить приложение Python для чтения секрета из Azure Key Vault.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 15650de776b481d1635b58f2b8ecf2bf2921d12f
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2019
ms.locfileid: "73242419"
---
# <a name="tutorial-use-a-linux-vm-and-a-python-app-to-store-secrets-in-azure-key-vault"></a>Руководство по Использование виртуальной машины Linux и приложения Python для хранения секретов в Azure Key Vault

Azure Key Vault помогает защитить секреты (например, ключи API и строки подключения к базе данных), необходимые для доступа к приложениям, службам и ИТ-ресурсам.

В этом руководстве описано, как настроить веб-приложение Azure, чтобы оно считывало данные из Azure Key Vault с помощью управляемых удостоверений для ресурсов Azure. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создайте хранилище ключей.
> * сохранение секрета в хранилище ключей;
> * Создание виртуальной машины Linux
> * включение [управляемого удостоверения](../active-directory/managed-identities-azure-resources/overview.md) для виртуальной машины;
> * предоставление разрешений, необходимых консольному приложению для чтения данных из хранилища ключей;
> * извлечение секрета из хранилища ключей.

Прежде чем продолжить, хорошо изучите статью [Что такое хранилище ключей Azure?](basic-concepts.md).

## <a name="prerequisites"></a>Предварительные требования

* [Git](https://git-scm.com/downloads).
* Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.
* [Azure CLI версии 2.0.4 (и более поздней)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) или Azure Cloud Shell.

[!INCLUDE [Azure Cloud Shell](../../includes/cloud-shell-try-it.md)]

## <a name="understand-managed-service-identity"></a>Основные сведения о MSI

Azure Key Vault может надежно хранить учетные данные, исключая их передачу в код. Чтобы извлечь их, вам нужно пройти аутентификацию в Azure Key Vault, но это невозможно без учетных данных. Это классическая проблема замкнутого круга. Используя Azure и Azure Active Directory (Azure AD), MSI предоставляет удостоверение начальной загрузки, что значительно упрощает начало работы.

При активации MSI для службы Azure (Виртуальные машины, Служба приложений или Функции), Azure создает субъект-службу для экземпляра службы в Azure AD. Azure внедряет учетные данные субъекта-службы в экземпляр службы.

![MSI](media/MSI.png)

Далее код вызывает локальную службу метаданных, доступную в ресурсе Azure, чтобы получить маркер доступа. Код использует маркер доступа, который получает от локальной конечной точки MSI для проверки подлинности в службе Azure Key Vault.

## <a name="sign-in-to-azure"></a>Вход в Azure

Чтобы войти в Azure с помощью Azure CLI, введите следующую команду:

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими.

Создайте группу ресурсов с помощью команды `az group create` в регионе West US (Западная часть США), используя приведенный ниже код. Замените `YourResourceGroupName` именем по своему выбору.

```azurecli-interactive
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Работая с этим руководством, вы будете использовать созданную группу ресурсов.

## <a name="create-a-key-vault"></a>Создайте хранилище ключей.

Теперь создайте хранилище ключей в группе ресурсов, созданной на предыдущем шаге. Введите следующие сведения:

* Имя хранилища ключей: это должна быть строка, состоящая из 3–24 символов, которые содержат: 0–9, a–z, A–Z и дефисы (-).
* Имя группы ресурсов.
* Расположение. **западная часть США**.

```azurecli-interactive
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

На этом этапе любые операции в этом хранилище ключей может выполнять только учетная запись Azure.

## <a name="add-a-secret-to-the-key-vault"></a>Добавление секрета в хранилище ключей

Теперь мы добавим секрет, чтобы продемонстрировать этот процесс. Мы рекомендуем вам сохранить строку подключения SQL или другие данные, которые должны храниться в безопасном расположении и при этом быть доступными для вашего приложения.

Введите следующие команды, чтобы создать секрет с именем *AppSecret* в хранилище ключей. Этот секрет будет хранить значение **MySecret**.

```azurecli-interactive
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-linux-virtual-machine"></a>Создание виртуальной машины Linux

Создайте виртуальную машину с помощью команды `az vm create`.

В следующем примере создается виртуальная машина **myVM** и добавляется учетная запись пользователя **azureuser**. Параметр `--generate-ssh-keys` автоматически создает ключ SSH и отправляет его в расположение ключа по умолчанию ( **~/.ssh**). Чтобы создать определенный набор ключей, укажите параметр `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Создание виртуальной машины и вспомогательных ресурсов занимает несколько минут. В следующем примере выходных данных показано успешное создание виртуальной машины.

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-00-00-00-00-00",
  "powerState": "VM running",
  "privateIpAddress": "XX.XX.XX.XX",
  "publicIpAddress": "XX.XX.XXX.XXX",
  "resourceGroup": "myResourceGroup"
}
```

Запишите значение `publicIpAddress`, которое появится в выходных данных виртуальной машины. Этот адрес будет использоваться для доступа к виртуальной машине на следующих шагах.

## <a name="assign-an-identity-to-the-vm"></a>Назначение удостоверения виртуальной машине

Создайте назначаемое системой удостоверение для виртуальной машины, выполнив следующую команду:

```azurecli-interactive
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Выходные данные команды выглядят следующим образом.

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

Запишите `systemAssignedIdentity`. Это значение используется на следующем шаге.

## <a name="give-the-vm-identity-permission-to-key-vault"></a>Предоставление Key Vault разрешения на удостоверение виртуальной машины

Теперь вы можете предоставить Key Vault разрешение на созданное вами удостоверение. Выполните следующую команду:

```azurecli-interactive
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>Вход на виртуальную машину

Войдите на виртуальную машину с помощью терминала.

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-python-library-on-the-vm"></a>Установка библиотеки Python на виртуальной машине

Скачайте и установите библиотеку Python [requests](https://pypi.org/project/requests/2.7.0/), чтобы совершать вызовы HTTP GET.

## <a name="create-edit-and-run-the-sample-python-app"></a>Создание, изменение и запуск примера приложения Python

Создайте файл Python с именем **Sample.py**.

Откройте файл Sample.py и внесите в него следующий код:

```python
# importing the requests library
import requests

# Step 1: Fetch an access token from an MSI-enabled Azure resource      
# Note that the resource here is https://vault.azure.net for the public cloud, and api-version is 2018-02-01
MSI_ENDPOINT = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net"
r = requests.get(MSI_ENDPOINT, headers = {"Metadata" : "true"})

# Extracting data in JSON format 
# This request gets an access token from Azure Active Directory by using the local MSI endpoint
data = r.json()

# Step 2: Pass the access token received from the previous HTTP GET call to the key vault
KeyVaultURL = "https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01"
kvSecret = requests.get(url = KeyVaultURL, headers = {"Authorization": "Bearer " + data["access_token"]})

print(kvSecret.json()["value"])
```

Указанный выше код выполняет операцию из двух шагов:

   1. Получает токен от локальной конечной точки MSI на виртуальной машине. Затем конечная точка получает токен из Azure Active Directory.
   1. Передает токен в хранилище ключей и получает ваш секрет.

Выполните следующую команду: Отобразится значение секрета.

```console
python Sample.py
```

Из этого руководства вы узнали, как использовать Azure Key Vault с приложением Python на виртуальной машине Linux.

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите группу ресурсов, виртуальную машину и все связанные ресурсы, если они вам больше не нужны. Чтобы сделать это, выберите группу ресурсов для виртуальной машины и нажмите **Удалить**.

Удалите хранилище ключей с помощью команды `az keyvault delete`:

```azurecli-interactive
az keyvault delete --name
                   [--resource-group]
                   [--subscription]
```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Справочник по REST API для Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
