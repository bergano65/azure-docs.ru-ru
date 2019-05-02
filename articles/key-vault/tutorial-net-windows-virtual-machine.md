---
title: Руководство. Использование Azure Key Vault с виртуальной машиной Windows в .NET | Документация Майкрософт
description: Благодаря этому руководству вы настроите приложение ASP.NET Core для считывания секрета из хранилища ключей.
services: key-vault
author: mbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: 7fdd2f1832995d279f58af52f96270f7aa3bb5a6
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64686160"
---
# <a name="tutorial-use-azure-key-vault-with-a-windows-virtual-machine-in-net"></a>Руководство по Использование Azure Key Vault с виртуальной машиной Windows в .NET

Azure Key Vault помогает защитить секреты, такие как ключи API, строки подключения к базам данных, необходимые для доступа к приложениям, службам и ИТ-ресурсам.

Из этого руководства вы узнаете, как настроить консольное приложение для считывания сведений из Azure Key Vault. Для этого используйте управляемые удостоверения для ресурсов Azure. 

В этом руководстве описаны следующие процедуры.

> [!div class="checklist"]
> * Создайте группу ресурсов.
> * Создать хранилище ключей.
> * Добавление секрета в хранилище ключей.
> * получение секрета из хранилища ключей;
> * Создайте виртуальную машину Azure.
> * Включить [управляемое удостоверение](../active-directory/managed-identities-azure-resources/overview.md) для виртуальной машины.
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

Затем выберите имя группы ресурсов для заполнителя. В следующем примере создается группа ресурсов в регионе "Западная часть США":

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

## <a name="install-net-core"></a>Установка .NET Core

Чтобы установить .NET Core, перейдите на страницу [​​загрузки .NET](https://www.microsoft.com/net/download).

## <a name="create-and-run-a-sample-net-app"></a>Создание и запуск примера приложения .NET

Откройте окно командной строки.

Вы можете вывести Hello World на консоль, выполнив следующие команды.

```batch
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

## <a name="edit-the-console-app"></a>Изменение консольного приложения

Откройте файл *Program.cs* и добавьте следующие пакеты.

```csharp
using System;
using System.IO;
using System.Net;
using System.Text;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

Измените файл класса, чтобы он содержал код в следующем двухэтапном процессе.

1. Получение токена из локальной конечной точки MSI на виртуальной машине. При этом также извлекается токен из Azure AD.
1. Передача токена в хранилище ключей и получение секрета. 

```csharp
 class Program
    {
        static void Main(string[] args)
        {
            // Step 1: Get a token from the local (URI) Managed Service Identity endpoint, which in turn fetches it from Azure AD
            var token = GetToken();

            // Step 2: Fetch the secret value from your key vault
            System.Console.WriteLine(FetchSecretValueFromKeyVault(token));
        }

        static string GetToken()
        {
            WebRequest request = WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net");
            request.Headers.Add("Metadata", "true");
            WebResponse response = request.GetResponse();
            return ParseWebResponse(response, "access_token");
        }

        static string FetchSecretValueFromKeyVault(string token)
        {
            WebRequest kvRequest = WebRequest.Create("https://<YourVaultName>.vault.azure.net/secrets/<YourSecretName>?api-version=2016-10-01");
            kvRequest.Headers.Add("Authorization", "Bearer "+  token);
            WebResponse kvResponse = kvRequest.GetResponse();
            return ParseWebResponse(kvResponse, "value");
        }

        private static string ParseWebResponse(WebResponse response, string tokenName)
        {
            string token = String.Empty;
            using (Stream stream = response.GetResponseStream())
            {
                StreamReader reader = new StreamReader(stream, Encoding.UTF8);
                String responseString = reader.ReadToEnd();

                JObject joResponse = JObject.Parse(responseString);    
                JValue ojObject = (JValue)joResponse[tokenName];             
                token = ojObject.Value.ToString();
            }
            return token;
        }
    }
```

В указанном коде показано, как выполнять операции с Azure Key Vault на виртуальной машине Windows.

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите виртуальную машину и хранилище ключей, если они больше не нужны.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Справочник по REST API для Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
