---
title: Руководство. Использование Azure Key Vault с виртуальной машиной Windows в .NET | Документация Майкрософт
description: Благодаря этому руководству вы настроите приложение ASP.NET Core для считывания секрета из хранилища ключей.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 8db1c511ab9defb140720655588b27279a0f08be
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86085486"
---
# <a name="tutorial-use-azure-key-vault-with-a-windows-virtual-machine-in-net"></a>Руководство по Использование Azure Key Vault с виртуальной машиной Windows в .NET

Azure Key Vault помогает защитить секреты, такие как ключи API, строки подключения к базам данных, необходимые для доступа к приложениям, службам и ИТ-ресурсам.

Из этого руководства вы узнаете, как настроить консольное приложение для считывания сведений из Azure Key Vault. Приложение будет использовать управляемое удостоверение виртуальной машины для аутентификации в Key Vault. 

В этом руководстве описаны следующие процедуры.

> [!div class="checklist"]
> * Создайте группу ресурсов.
> * Создать хранилище ключей.
> * Добавление секрета в хранилище ключей.
> * получение секрета из хранилища ключей;
> * Создайте виртуальную машину Azure.
> * Включить [управляемое удостоверение](../../active-directory/managed-identities-azure-resources/overview.md) для виртуальной машины.
> * Назначение разрешений для идентификатора виртуальной машины.

Перед началом работы ознакомьтесь c [основными понятиями службы Key Vault](basic-concepts.md). 

Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Предварительные требования

Для Windows, Mac и Linux:
  * [Git](https://git-scm.com/downloads);
  * [Пакет SDK для .NET Core 3.1 или более поздней версии](https://dotnet.microsoft.com/download/dotnet-core/3.1).
  * [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-resources-and-assign-permissions"></a>Создание ресурсов и назначение разрешений

Перед началом программирования необходимо создать некоторые ресурсы, поместить секрет в хранилище ключей и назначить разрешения.

### <a name="sign-in-to-azure"></a>Вход в Azure

Чтобы войти в Azure с помощью Azure CLI, введите следующую команду:

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Создание группы ресурсов

Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими. Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#az-group-create). 

В этом примере создается группа ресурсов в расположении западной части США:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Работая с этим учебником, вы будете использовать созданную группу ресурсов.

### <a name="create-a-key-vault-and-populate-it-with-a-secret"></a>Создание хранилища ключей и его заполнение секретом

Создайте хранилище ключей в группе ресурсов, предоставив команде [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) следующую информацию:

* Имя хранилища ключей. Строка длиной от 3 до 24 символов, которая может содержать только цифры (0–9), буквы (a-z, A-Z) и дефисы (-).
* Имя группы ресурсов
* Расположение. **Западная часть США**

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
На этом этапе операции в этом хранилище ключей может выполнять только учетная запись Azure.

Теперь добавьте секрет в хранилище ключей с помощью команды [az keyvault secret set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set)


Введите следующую команду, чтобы создать секрет с именем **AppSecret** в хранилище ключей.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

В этом секрете хранится значение **MySecret**.

### <a name="create-a-virtual-machine"></a>Создание виртуальной машины
Создайте виртуальную машину одним из следующих способов.

* [CLI Azure.](../../virtual-machines/windows/quick-create-cli.md)
* [PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
* [Портал Azure](../../virtual-machines/windows/quick-create-portal.md)

### <a name="assign-an-identity-to-the-vm"></a>Назначение удостоверения виртуальной машине
Создайте назначаемое системой удостоверение для виртуальной машины с помощью команды [az vm identity assign](/cli/azure/vm/identity?view=azure-cli-latest#az-vm-identity-assign):

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

### <a name="assign-permissions-to-the-vm-identity"></a>Назначение разрешения для идентификатора виртуальной машины
Предоставьте созданному выше удостоверению разрешение для хранилища ключей, выполнив команду [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy).

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

### <a name="sign-in-to-the-virtual-machine"></a>Вход в виртуальную машину

Чтобы войти в виртуальную машину, следуйте инструкциям из статьи [How to connect and sign on to an Azure virtual machine running Windows](../../virtual-machines/windows/connect-logon.md) (Как подключиться к виртуальной машине Azure под управлением Windows и войти в нее).

## <a name="set-up-the-console-app"></a>Настройка консольного приложения

Создайте консольное приложение и установите необходимые пакеты с помощью команды `dotnet`.

### <a name="install-net-core"></a>Установка .NET Core

Чтобы установить .NET Core, перейдите на страницу [​​загрузки .NET](https://www.microsoft.com/net/download).

### <a name="create-and-run-a-sample-net-app"></a>Создание и запуск примера приложения .NET

Откройте командную строку.

Вы можете вывести Hello World на консоль, выполнив следующие команды.

```console
dotnet new console -n keyvault-console-app
cd keyvault-console-app
dotnet run
```

### <a name="install-the-package"></a>Установка пакета

В окне консоли установите клиентскую библиотеку секретов Azure Key Vault для .NET:

```console
dotnet add package Azure.Security.KeyVault.Secrets
```

Для работы с этим кратким руководством необходимо установить следующий пакет удостоверений для аутентификации в Azure Key Vault:

```console
dotnet add package Azure.Identity
```

## <a name="edit-the-console-app"></a>Изменение консольного приложения

Откройте файл *Program.cs* и добавьте следующие пакеты.

```csharp
using System;
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
```

Добавьте эти строки, обновив URI, чтобы отразить `vaultUri` хранилища ключей. Для проверки подлинности в хранилище ключей в следующем примере кода используется функция [DefaultAzureCredential()](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet), которая использует токен из управляемого удостоверения приложения. В ней также реализована экспоненциальная задержка для повторных попыток на случай регулирования запросов к хранилищу ключей.

```csharp
  class Program
    {
        static void Main(string[] args)
        {
            string secretName = "mySecret";

            var kvUri = "https://<your-key-vault-name>.vault.azure.net";
            SecretClientOptions options = new SecretClientOptions()
            {
                Retry =
                {
                    Delay= TimeSpan.FromSeconds(2),
                    MaxDelay = TimeSpan.FromSeconds(16),
                    MaxRetries = 5,
                    Mode = RetryMode.Exponential
                 }
            };

            var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential(),options);

            Console.Write("Input the value of your secret > ");
            string secretValue = Console.ReadLine();

            Console.Write("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...");

            client.SetSecret(secretName, secretValue);

            Console.WriteLine(" done.");

            Console.WriteLine("Forgetting your secret.");
            secretValue = "";
            Console.WriteLine("Your secret is '" + secretValue + "'.");

            Console.WriteLine("Retrieving your secret from " + keyVaultName + ".");

            KeyVaultSecret secret = client.GetSecret(secretName);

            Console.WriteLine("Your secret is '" + secret.Value + "'.");

            Console.Write("Deleting your secret from " + keyVaultName + " ...");

            client.StartDeleteSecret(secretName);

            System.Threading.Thread.Sleep(5000);
            Console.WriteLine(" done.");

        }
    }
```

В указанном коде показано, как выполнять операции с Azure Key Vault на виртуальной машине Windows.

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите виртуальную машину и хранилище ключей, если они больше не нужны.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Справочник по REST API для Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
