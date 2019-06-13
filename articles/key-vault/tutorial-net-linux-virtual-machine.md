---
title: Руководство. Использование виртуальной машины Linux и консольного приложения ASP.NET для хранения секретов в Azure Key Vault | Документация Майкрософт
description: Из этого руководства вы узнаете, как настроить приложение ASP.NET Core для чтения секрета из Azure Key Vault.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: 576ce0abc15a646738fea57dfabf43c889635a4b
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66416932"
---
# <a name="tutorial-use-a-linux-vm-and-a-net-app-to-store-secrets-in-azure-key-vault"></a>Руководство по использованию виртуальной машины Linux и приложения .NET для хранения секретов в Azure Key Vault

Azure Key Vault помогает защитить секреты, такие как ключи API и строки подключения к базам данных, необходимые для доступа к приложениям, службам и ИТ-ресурсам.

В этом руководстве описано, как настроить консольное приложение .NET, чтобы оно считывало данные из Azure Key Vault с помощью управляемых удостоверений для ресурсов Azure. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создайте хранилище ключей.
> * хранение секрета в Key Vault;
> * создание виртуальной машины Linux в Azure;
> * включение [управляемого удостоверения](../active-directory/managed-identities-azure-resources/overview.md) для виртуальной машины;
> * предоставление разрешений, необходимых консольному приложению для чтения данных из Key Vault;
> * получение секрета из Key Vault.

Прежде чем мы продолжим, ознакомьтесь с [основными понятиями о хранилище ключей](key-vault-whatis.md#basic-concepts).

## <a name="prerequisites"></a>Предварительные требования

* [Git](https://git-scm.com/downloads).
* Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.
* [Azure CLI 2.0 или более поздней версии](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) либо Azure Cloud Shell.

[!INCLUDE [Azure Cloud Shell](../../includes/cloud-shell-try-it.md)]

## <a name="understand-managed-service-identity"></a>Основные сведения о MSI

Azure Key Vault может безопасно хранить учетные данные, чтобы их не было в коде, но чтобы получить их, необходимо пройти проверку подлинности в хранилище ключей Azure. но это невозможно без учетных данных. Это классическая проблема замкнутого круга. Если используются Azure и Azure Active Directory (Azure AD), MSI предоставляет удостоверение начальной загрузки, что значительно упрощает начало работы.

При активации MSI для службы Azure (Виртуальные машины, Служба приложений или Функции) в Azure создается субъект-служба для экземпляра службы в Azure Active Directory. Azure внедряет учетные данные субъекта-службы в экземпляр службы.

![MSI](media/MSI.png)

Далее код вызывает локальную службу метаданных, доступную в ресурсе Azure, чтобы получить маркер доступа.
Код использует маркер доступа, который он получает от локального MSI_ENDPOINT для проверки подлинности в службе Azure Key Vault.

## <a name="sign-in-to-azure"></a>Вход в Azure

Чтобы войти в Azure с помощью Azure CLI, введите следующую команду:

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью команды `az group create`. Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими.

Создайте группу ресурсов в регионе "Западная часть США". Выберите имя группы ресурсов и замените `YourResourceGroupName`, как показано в следующем примере:

```azurecli-interactive
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Работая с этим руководством, вы будете использовать созданную группу ресурсов.

## <a name="create-a-key-vault"></a>Создайте хранилище ключей.

Теперь создайте хранилище ключей в новой группе ресурсов. Введите следующие сведения:

* Имя хранилища ключей: строка длиной от 3 до 24 символов, которая может содержать только цифры, буквы и дефисы (0–9, a–z, A–Z и \-).
* Имя группы ресурсов
* Расположение. **Западная часть США**

```azurecli-interactive
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

На этом этапе любые операции в этом хранилище ключей можно выполнять только из учетной записи Azure.

## <a name="add-a-secret-to-the-key-vault"></a>Добавление секрета в хранилище ключей

Теперь добавьте секрет. В реальном сценарии вы можете хранить здесь строки подключения SQL или другие сведения, которые должны быть защищены и при этом доступны приложению.

В этом примере введите следующие команды, чтобы создать секрет в хранилище ключей. Секрету присваиваются имя **AppSecret** и значение **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-linux-virtual-machine"></a>Создание виртуальной машины Linux

Создайте виртуальную машину с помощью команды `az vm create`.

В следующем примере создается виртуальная машина **myVM** и добавляется учетная запись пользователя **azureuser**. Параметр `--generate-ssh-keys` автоматически создает ключ SSH и сохраняет его в стандартное расположение ( **~/.ssh**). Чтобы использовать определенный набор ключей, примените параметр `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Создание виртуальной машины и вспомогательных ресурсов занимает несколько минут. В следующем примере выходных данных показано, что виртуальная машина успешно создана.

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

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Результат выполнения команды должен выглядеть следующим образом:

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

Запишите `systemAssignedIdentity`. Это значение понадобится вам на следующем шаге.

## <a name="give-the-vm-identity-permission-to-key-vault"></a>Предоставление Key Vault разрешения на удостоверение виртуальной машины

Теперь вы можете предоставить Key Vault разрешение на созданное вами удостоверение. Выполните следующую команду:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>Вход на виртуальную машину

Войдите на виртуальную машину с помощью терминала.

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-net-core-on-linux"></a>Установка .NET Core на Linux

В виртуальной машине Linux выполните приведенные ниже действия.

Зарегистрируйте ключ продукта Майкрософт как доверенный, выполнив следующие команды:

   ```console
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
   ```

Настройте нужную версию веб-канала пакета узла на основе операционной системы:

```console
   # Ubuntu 17.10
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-artful-prod artful main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
   
   # Ubuntu 17.04
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-zesty-prod zesty main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
   
   # Ubuntu 16.04 / Linux Mint 18
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-xenial-prod xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
   
   # Ubuntu 14.04 / Linux Mint 17
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-trusty-prod trusty main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
```

Установите .NET и проверьте версию:

   ```console
   sudo apt-get install dotnet-sdk-2.1.4
   dotnet --version
   ```

## <a name="create-and-run-a-sample-net-app"></a>Создание и запуск примера приложения .NET

Выполните команды ниже. В консоли должно появиться сообщение "Hello World".

```console
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

## <a name="edit-the-console-app-to-fetch-your-secret"></a>Изменение консольного приложения для получения секрета

Откройте файл Program.cs и добавьте эти пакеты:

   ```csharp
   using System;
   using System.IO;
   using System.Net;
   using System.Text;
   using Newtonsoft.Json;
   using Newtonsoft.Json.Linq;
   ```

Этот процесс состоит из двух этапов и предусматривает изменение файла класса, чтобы обеспечить приложению доступ к секрету в хранилище ключей.

1. Получите маркер из локальной конечной точки MSI на виртуальной машине, которая извлекает маркер из Azure Active Directory.
1. Передайте маркер в Key Vault и получите секрет.

   Измените файл класса, чтобы он содержал следующий код:

   ```csharp
    class Program
       {
           static void Main(string[] args)
           {
               // Step 1: Get a token from local (URI) Managed Service Identity endpoint which in turn fetches it from Azure Active Directory
               var token = GetToken();

               // Step 2: Fetch the secret value from Key Vault
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
               WebRequest kvRequest = WebRequest.Create("https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01");
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

Теперь вы знаете, как выполнять операции с Azure Key Vault в приложении .NET, работающем на виртуальной машине Linux в Azure.

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите группу ресурсов, виртуальную машину и все связанные ресурсы, если они вам больше не нужны. Чтобы сделать это, выберите группу ресурсов для виртуальной машины и нажмите **Удалить**.

Удалите хранилище ключей с помощью команды `az keyvault delete`:

```azurecli-interactive
az keyvault delete --name
                   [--resource group]
                   [--subscription]
```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Справочник по REST API для Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
