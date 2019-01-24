---
title: Руководство. Использование Azure Key Vault с виртуальной машиной Azure на языке Python | Документация Майкрософт
description: В этом руководстве вы настроите приложение Python для чтения секрета из хранилища ключей.
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: rajvijan
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: key-vault
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: 1e364003093d5e37a75830386cafe855b0bdcad2
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54467407"
---
# <a name="tutorial-use-azure-key-vault-with-an-azure-virtual-machine-in-python"></a>Руководство. Использование Azure Key Vault с виртуальной машиной Azure на языке Python

Azure Key Vault помогает защитить секреты (например, ключи API и строки подключения к базе данных), необходимые для доступа к приложениям, службам и ИТ-ресурсам.

В этом руководстве описано, как настроить веб-приложение Azure, чтобы оно считывало данные из Azure Key Vault с помощью управляемых удостоверений для ресурсов Azure. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создать хранилище ключей.
> * сохранение секрета в хранилище ключей;
> * Создайте виртуальную машину Azure.
> * Включите [управляемое удостоверение](../active-directory/managed-identities-azure-resources/overview.md) для виртуальной машины.
> * Предоставить разрешения, необходимые консольному приложению для чтения данных из хранилища ключей.
> * получение секрета из хранилища ключей;

Прежде чем продолжить, ознакомьтесь с разделом [Основные понятия](key-vault-whatis.md#basic-concepts).

## <a name="prerequisites"></a>Предварительные требования
Для всех платформ необходимо:

* Git ([скачать](https://git-scm.com/downloads)).
* Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) версии 2.0.4 или более поздней. Доступно для Windows, Mac и Linux.

### <a name="managed-service-identity-and-how-it-works"></a>Управляемое удостоверение службы и принцип его работы
В этом руководстве используется Управляемое удостоверение службы (MSI).

Azure Key Vault может надежно хранить учетные данные, исключая их передачу в код. Для их извлечения вам необходима проверка подлинности в Key Vault. А для проверки подлинности в Key Vault требуются учетные данные. Это классическая проблема начальной загрузки. Используя Azure и Azure Active Directory, MSI предоставляет "удостоверение начальной загрузки", что значительно упрощает начало работы.

При активации MSI для службы Azure (Виртуальные машины, Служба приложений или Функции), Azure создает [субъект-службу](key-vault-whatis.md#basic-concepts) для экземпляра службы в Azure AD. Azure внедряет учетные данные субъект-службы в экземпляр службы. 

![MSI](media/MSI.png)

Далее, чтобы получить маркер доступа, код вызывает локальную службу метаданных, доступную в ресурсе Azure.
Код использует маркер доступа, который получает от локальной конечной точки MSI для проверки подлинности в службе Azure Key Vault. 

## <a name="log-in-to-azure"></a>Вход в Azure

Чтобы войти в Azure с помощью Azure CLI, введите следующую команду:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#az-group-create). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими.

Выберите имя группы ресурсов для заполнителя.
В следующем примере создается группа ресурсов в регионе "Западная часть США":

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Созданная группа ресурсов будет использоваться далее в этой статье.

## <a name="create-a-key-vault"></a>Создайте хранилище ключей.

Теперь создайте хранилище ключей в группе ресурсов, созданной на предыдущем шаге. Введите следующие сведения:

* Имя хранилища ключей: это должна быть строка, состоящая из 3–24 символов, которые содержат: 0–9, a–z, A–Z и дефисы (-).
* Имя группы ресурсов.
* Расположение. **западная часть США**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
На этом этапе любые операции в этом хранилище ключей может выполнять только учетная запись Azure.

## <a name="add-a-secret-to-the-key-vault"></a>Добавление секрета в хранилище ключей

Теперь мы добавим секрет, чтобы продемонстрировать этот процесс. Вы можете хранить здесь строки подключения SQL и прочие сведения, которые должны храниться безопасно, но быть доступны для приложения.

Введите следующие команды, чтобы создать секрет с именем *AppSecret* в хранилище ключей. Этот секрет будет хранить значение **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-virtual-machine"></a>Создание виртуальной машины

Создайте виртуальную машину с помощью команды [az vm create](/cli/azure/vm).

В следующем примере создается виртуальная машина *myVM* и добавляется учетная запись пользователя *azureuser*. Параметр `--generate-ssh-keys` автоматически создает ключ SSH и отправляет его в расположение ключа по умолчанию (*~/.ssh*). Чтобы использовать определенный набор ключей, примените параметр `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Создание виртуальной машины и вспомогательных ресурсов занимает несколько минут. В следующем примере выходных данных показано успешное создание виртуальной машины.

```
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

## <a name="assign-an-identity-to-the-virtual-machine"></a>Назначение удостоверения виртуальной машине
На этом шаге мы создадим назначаемое системой удостоверение для виртуальной машины. Выполните следующую команду в Azure CLI.

```
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Выходные данные команды выглядят следующим образом. Запишите значение **systemAssignedIdentity**. 

```
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="give-the-virtual-machine-identity-permission-to-the-key-vault"></a>Предоставление удостоверению виртуальной машины разрешения на доступ к хранилищу ключей
Сейчас мы можем предоставить разрешение удостоверения хранилищу ключей. Выполните следующую команду:

```
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-virtual-machine"></a>Войдите на виртуальную машину.

Войдите на виртуальную машину с помощью [этого руководства](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).

## <a name="create-and-run-the-sample-python-app"></a>Создание и запуск примера приложения Python

Следующий пример файла — *Sample.py*. Он использует библиотеку [requests](https://pypi.org/project/requests/2.7.0/) для выполнения вызовов HTTP GET.

## <a name="edit-samplepy"></a>Редактирование файла Sample.py
После создания Sample.py откройте файл и скопируйте следующий код. Код представляет собой двухэтапный процесс. 
1. Получение токена из локальной конечной точки MSI на виртуальной машине. Затем конечная точка получает токен из Azure Active Directory.
2. Передача токена в хранилище ключей и получение секрета. 

```
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

Выполнив следующую команду, вы должны увидеть значение секрета. 

```
python Sample.py
```

В указанном коде показано, как выполнять операции с Azure Key Vault на виртуальной машине Windows. 

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Справочник по REST API для Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
