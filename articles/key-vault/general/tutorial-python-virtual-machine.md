---
title: Учебник по использованию Azure Key Vault с виртуальной машиной в Python | Документация Майкрософт
description: Благодаря этому руководству вы настроите приложение ASP.NET Core для считывания секрета из хранилища ключей.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 07/20/2020
ms.author: mbaldwin
ms.custom: mvc, tracking-python
ms.openlocfilehash: 453307b304c4cb1899b1de31117c944ac66fcddb
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87093935"
---
# <a name="tutorial-use-azure-key-vault-with-a-virtual-machine-in-python"></a>Руководство по Использование Azure Key Vault с виртуальной машиной в Python

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

Перед началом работы ознакомьтесь c [основными понятиями службы Key Vault](basic-concepts.md). 

Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Предварительные требования

Для Windows, Mac и Linux:
  * [Git](https://git-scm.com/downloads);
  * Для этого руководства требуется запустить Azure CLI локально. Необходимо установить Azure CLI версии 2.0.4 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо установить или обновить CLI, ознакомьтесь со статьей [Установка Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="log-in-to-azure"></a>Вход в Azure

Чтобы войти в Azure с помощью Azure CLI, введите следующую команду:

```azurecli
az login
```

### <a name="create-a-resource-group-and-key-vault"></a>Создание группы ресурсов и хранилища ключей

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

## <a name="add-a-secret-to-the-key-vault"></a>Добавление секрета в хранилище ключей

Теперь мы добавим секрет, чтобы продемонстрировать этот процесс. Секретом может быть строка подключения к серверу SQL или другие сведения, которые должны быть защищены и при этом доступны приложению.

Введите следующую команду, чтобы создать секрет с именем **AppSecret** в хранилище ключей.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

В этом секрете хранится значение **MySecret**.

## <a name="create-a-virtual-machine"></a>Создание виртуальной машины
Вы можете создать виртуальную машину одним из следующих способов.

* [CLI Azure.](../../virtual-machines/windows/quick-create-cli.md)
* [PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
* [Портал Azure](../../virtual-machines/windows/quick-create-portal.md)

## <a name="assign-an-identity-to-the-vm"></a>Назначение удостоверения виртуальной машине
На этом шаге вы создадите назначаемое системой удостоверение для виртуальной машины, выполнив следующую команду в Azure CLI.

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Обратите внимание на присвоенный системой идентификатор, который отображается в следующем коде. Выходные данные приведенной выше команды выглядят следующим образом. 

```output
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

Чтобы войти в виртуальную машину, следуйте инструкциям из статьи [Как подключиться к виртуальной машине Azure под управлением Windows и войти на нее](../../virtual-machines/windows/connect-logon.md).

## <a name="create-and-run-a-sample-python-app"></a>Создание и запуск примера приложения Python

В следующем разделе приведен пример файла с именем *Sample.py*. Он использует библиотеку [requests](https://2.python-requests.org/en/master/) для выполнения вызовов HTTP GET.

## <a name="edit-samplepy"></a>Редактирование файла Sample.py

После создания *Sample.py* откройте файл и скопируйте код в этом разделе. 

Код представляет собой двухэтапный процесс.
1. Получение токена из локальной конечной точки MSI на виртуальной машине.  
  При этом также извлекается токен из Azure AD.
1. Передача токена в хранилище ключей и получение секрета. 

```python
    # importing the requests library 
    import requests 

    # Step 1: Fetch an access token from a Managed Identity enabled azure resource.
    # Resources with an MSI configured recieve an AAD access token by using the Azure Instance Metadata Service (IMDS)
    # IMDS provides an endpoint accessible to all IaaS VMs using a non-routable well-known IP Address
    # To learn more about IMDS and MSI Authentication see the following link: https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service
    # Note that the resource here is https://vault.azure.net for public cloud and api-version is 2018-02-01
    MSI_ENDPOINT = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net"
    r = requests.get(MSI_ENDPOINT, headers = {"Metadata" : "true"}) 
      
    # extracting data in json format 
    # This request gets an access_token from Azure AD by using the local MSI endpoint.
    data = r.json() 
    
    # Step 2: Pass the access_token received from previous HTTP GET call to your key vault.
    KeyVaultURL = "https://{YOUR KEY VAULT NAME}.vault.azure.net/secrets/{YOUR SECRET NAME}?api-version=2016-10-01"
    kvSecret = requests.get(url = KeyVaultURL, headers = {"Authorization": "Bearer " + data["access_token"]})
    
    print(kvSecret.json()["value"])
```

Выполнив следующий код, вы можете отобразить значение секрета. 

```console
python Sample.py
```

В указанном коде показано, как выполнять операции с Azure Key Vault на виртуальной машине Windows. 

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите виртуальную машину и хранилище ключей, если они больше не нужны.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Справочник по REST API для Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
