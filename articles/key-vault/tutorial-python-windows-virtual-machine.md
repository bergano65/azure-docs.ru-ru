---
title: Руководство. Использование Azure Key Vault с виртуальной машиной Windows на языке Python | Документация Майкрософт
description: Благодаря этому руководству вы настроите приложение ASP.NET Core для считывания секрета из хранилища ключей.
services: key-vault
author: mbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: 58d1b9d293e88612853aec9303f4c4110d6a5cd2
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64701951"
---
# <a name="tutorial-use-azure-key-vault-with-a-windows-virtual-machine-in-python"></a>Руководство по Использование Azure Key Vault с виртуальной машиной Windows на языке Python

Azure Key Vault помогает защитить секреты, такие как ключи API, строки подключения к базам данных, необходимые для доступа к приложениям, службам и ИТ-ресурсам.

Из этого руководства вы узнаете, как настроить консольное приложение для считывания сведений из Azure Key Vault. Для этого используйте управляемые удостоверения для ресурсов Azure. 

В этом руководстве описаны следующие процедуры.

> [!div class="checklist"]
> * Создать хранилище ключей.
> * Добавление секрета в хранилище ключей.
> * получение секрета из хранилища ключей;
> * Создайте виртуальную машину Azure.
> * Включение управляемого удостоверения.
> * Назначение разрешений для идентификатора виртуальной машины.

Перед началом работы ознакомьтесь c [основными понятиями службы Key Vault](key-vault-whatis.md#basic-concepts). 

Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Предварительные требования

Для Windows, Mac и Linux:
  * [Git](https://git-scm.com/downloads)
  * Для этого руководства требуется запустить Azure CLI локально. Необходимо установить Azure CLI версии 2.0.4 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо установить или обновить CLI, ознакомьтесь со статьей [Установка Azure CLI 2.0](https://review.docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="about-managed-service-identity"></a>Основные сведения об MSI

Azure Key Vault надежно хранит учетные данные, исключая их отображение в коде. Но для получения ключей нужно выполнить проверку подлинности в Azure Key Vault. А для проверки подлинности в Key Vault требуются учетные данные. Это классическая дилемма. Управляемое удостоверение службы (MSI) решает эту проблему, предоставляя _соответствующее удостоверение_, которое упрощает процесс.

При включении MSI для службы Azure (например, виртуальных машин, Службы приложений Azure или Функций Azure), Azure создает [субъект-службу](key-vault-whatis.md#basic-concepts). MSI делает это для экземпляра службы в Azure Active Directory (Azure AD) и добавляет учетные данные субъекта-службы в этот экземпляр. 

![MSI](media/MSI.png)

Далее, чтобы получить маркер доступа, код вызывает локальную службу метаданных, доступную в ресурсе Azure. Код использует маркер доступа, который получает от локальной конечной точки MSI, для проверки подлинности в службе Azure Key Vault. 

## <a name="log-in-to-azure"></a>Вход в Azure

Чтобы войти в Azure с помощью Azure CLI, введите следующую команду:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими.

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#az-group-create). 

Выберите имя группы ресурсов для заполнителя. В следующем примере создается группа ресурсов в регионе "Западная часть США":

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Работая с этим руководством, вы будете использовать созданную группу ресурсов.

## <a name="create-a-key-vault"></a>Создайте хранилище ключей.

Чтобы создать хранилище ключей в группе ресурсов, созданной на предыдущем шаге, предоставьте следующую информацию.

* Имя хранилища ключей. Строка длиной от 3 до 24 символов, которая может содержать только цифры (0–9), буквы (a-z, A-Z) и дефисы (-).
* Имя группы ресурсов
* Расположение. **Западная часть США**

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
На этом этапе операции в этом хранилище ключей может выполнять только учетная запись Azure.

## <a name="add-a-secret-to-the-key-vault"></a>Добавление секрета в хранилище ключей

Теперь мы добавим секрет, чтобы продемонстрировать этот процесс. Секретом может быть строка подключения к серверу SQL или другие сведения, которые должны быть защищены и при этом доступны приложению.

Введите следующую команду, чтобы создать секрет с именем **AppSecret** в хранилище ключей.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

В этом секрете хранится значение **MySecret**.

## <a name="create-a-virtual-machine"></a>Создание виртуальной машины
Вы можете создать виртуальную машину одним из следующих способов.

* [CLI Azure.](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-cli)
* [PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell)
* [Портал Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

## <a name="assign-an-identity-to-the-vm"></a>Назначение удостоверения виртуальной машине
На этом шаге вы создадите назначаемое системой удостоверение для виртуальной машины, выполнив следующую команду в Azure CLI.

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Обратите внимание на присвоенный системой идентификатор, который отображается в следующем коде. Выходные данные приведенной выше команды выглядят следующим образом. 

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>Назначение разрешения для идентификатора виртуальной машины
Теперь вы можете предоставить созданному выше идентификатору разрешение для хранилища ключей, выполнив следующую команду.

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-on-to-the-virtual-machine"></a>Вход на виртуальную машину

Чтобы войти в виртуальную машину, следуйте инструкциям из статьи [Как подключиться к виртуальной машине Azure под управлением Windows и войти на нее](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).

## <a name="create-and-run-a-sample-python-app"></a>Создание и запуск примера приложения Python

В следующем разделе приведен пример файла с именем *Sample.py*. Он использует библиотеку [requests](http://docs.python-requests.org/en/master/) для выполнения вызовов HTTP GET.

## <a name="edit-samplepy"></a>Редактирование файла Sample.py

После создания *Sample.py* откройте файл и скопируйте код в этом разделе. 

Код представляет собой двухэтапный процесс.
1. Получение токена из локальной конечной точки MSI на виртуальной машине.  
  При этом также извлекается токен из Azure AD.
1. Передача токена в хранилище ключей и получение секрета. 

```
    # importing the requests library 
    import requests 

    # Step 1: Fetch an access token from a Managed Identity enabled azure resource.      
    # Note that the resource here is https://vault.azure.net for public cloud and api-version is 2018-02-01
    MSI_ENDPOINT = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net"
    r = requests.get(MSI_ENDPOINT, headers = {"Metadata" : "true"}) 
      
    # extracting data in json format 
    # This request gets an access_token from Azure AD by using the local MSI endpoint.
    data = r.json() 
    
    # Step 2: Pass the access_token received from previous HTTP GET call to your key vault.
    KeyVaultURL = "https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01"
    kvSecret = requests.get(url = KeyVaultURL, headers = {"Authorization": "Bearer " + data["access_token"]})
    
    print(kvSecret.json()["value"])
```

Выполнив следующий код, вы можете отобразить значение секрета. 

```
python Sample.py
```

В указанном коде показано, как выполнять операции с Azure Key Vault на виртуальной машине Windows. 

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите виртуальную машину и хранилище ключей, если они больше не нужны.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Справочник по REST API для Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
