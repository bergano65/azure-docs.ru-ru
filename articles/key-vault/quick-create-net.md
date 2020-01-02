---
title: Краткое руководство по использованию клиентской библиотеки Azure Key Vault для .NET (версии 4)
description: Узнайте, как создавать, извлекать и удалять секреты в Azure Key Vault с помощью клиентской библиотеки .NET (версии 4).
author: msmbaldwin
ms.author: mbaldwin
ms.date: 05/20/2019
ms.service: key-vault
ms.topic: quickstart
ms.openlocfilehash: 3322a9f2a7e09ee76c0fff94a2747c791ecf4804
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975386"
---
# <a name="quickstart-azure-key-vault-client-library-for-net-sdk-v4"></a>Краткое руководство. Использование клиентской библиотеки Azure Key Vault для .NET (пакет SDK версии 4)

Начало работы с клиентской библиотекой Azure Key Vault для .NET. Чтобы установить пакет и испробовать пример кода для выполнения базовых задач, выполните описанные ниже шаги.

Хранилище ключей Azure помогает защитить криптографические ключи и секреты, используемые облачными приложениями и службами. Клиентская библиотека Azure Key Vault для .NET позволяет выполнять следующие задачи:

- Повысьте уровень безопасности и увеличьте контроль над ключами и паролями.
- Создавайте и импортируйте ключи шифрования за считаные минуты.
- Сократите время задержки с помощью масштабирования в облаке и глобального резервирования.
- Упрощение и автоматизация задач, связанных с SSL- и TLS-сертификатами.
- Используйте модули HSM, отвечающие стандартам FIPS 140-2 уровня 2.

[Справочная документация по API](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | [Пакет (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/).

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Пакет SDK для .NET Core 2.1 или более поздней версии](https://dotnet.microsoft.com/download/dotnet-core/2.1).
* [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) или [Azure PowerShell](/powershell/azure/overview).

В этом кратком руководстве предполагается, что вы выполняете команды `dotnet`, [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) и Windows в терминале Windows (например, [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-6), [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6) или [Azure Cloud Shell](https://shell.azure.com/)).

## <a name="setting-up"></a>Настройка

### <a name="create-new-net-console-app"></a>Создание консольного приложения .NET

В окне консоли выполните команду `dotnet new`, чтобы создать консольное приложение .NET с именем `akv-dotnet`.

```console
dotnet new console -n key-vault-console-app
```

Измените каталог на созданную папку приложения. Чтобы создать приложение, выполните следующую команду:

```console
dotnet build
```

Выходные данные сборки не должны содержать предупреждений или ошибок.

```console
Build succeeded.
 0 Warning(s)
 0 Error(s)
```

### <a name="install-the-package"></a>Установка пакета

Через окно консоли установите клиентскую библиотеку Azure Key Vault для .NET:

```console
dotnet add package Azure.Security.KeyVault.Secrets --version 4.0.0
```

Для этого краткого руководства вам также понадобится установить следующие пакеты:

```console
dotnet add package Azure.Identity --version 1.0.0
```

### <a name="create-a-resource-group-and-key-vault"></a>Создание группы ресурсов и хранилища ключей

В этом кратком руководстве используется предварительно созданное хранилище ключей Azure. Хранилище ключей можно создать, выполнив действия, описанные в кратких руководствах по [Azure CLI](quick-create-cli.md), [Azure PowerShell](quick-create-powershell.md) или [порталу Azure](quick-create-portal.md). Можно также выполнить команду Azure CLI, которая предложена ниже:

> [!Important]
> Каждое хранилище ключей должно иметь уникальное имя. В следующих примерах замените <your-unique-keyvault-name> именем своего хранилища ключей.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Создание субъекта-службы

Самый простой способ проверить подлинность облачного приложения NET — с помощью управляемых удостоверений. Дополнительные сведения см. в статье [Provide Key Vault authentication with a managed identity](managed-identity.md) (Предоставление проверки подлинности Key Vault с помощью управляемого удостоверения). Но для простоты в рамках этого краткого руководства создается консольное приложение .NET. Для проверки подлинности в Azure приложение рабочего стола должно использовать субъект-службу и политику управления доступом.

Создайте субъект-службу с помощью команды Azure CLI [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac):

```azurecli
az ad sp create-for-rbac -n "http://mySP" --sdk-auth
```

Эта операция возвращает ряд пар "ключ — значение". 

```console
{
  "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
  "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
  "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
  "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Запишите значение clientId, clientSecret и tenantId, так как мы будем использовать их в следующих шагах.

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Предоставление субъекту-службе доступа к хранилищу ключей

Создайте политику доступа для хранилища ключей, которая предоставляет разрешение субъекту-службе, передавая clientId в команду [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy). Предоставьте субъекту-службе разрешения get, list и set для ключей и секретов.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

#### <a name="set-environmental-variables"></a>Настройка переменных среды

Метод DefaultAzureCredential в нашем приложении использует три переменные среды: `AZURE_CLIENT_ID`, `AZURE_CLIENT_SECRET` и `AZURE_TENANT_ID`. Присвойте этим переменным значения clientId, clientSecret и tenantId, записанные на шаге [создания субъекта-службы](#create-a-service-principal) ранее.

Кроме того, необходимо будет сохранить имя хранилища ключей как переменную среды `KEY_VAULT_NAME`.

```console
setx AZURE_CLIENT_ID <your-clientID>

setx AZURE_CLIENT_SECRET <your-clientSecret>

setx AZURE_TENANT_ID <your-tenantId>

setx KEY_VAULT_NAME <your-key-vault-name>
````

При каждом вызове `setx` вы будете получать ответ об успешном выполнении "SUCCESS: Specified value was saved." (Указанное значение сохранено.)

## <a name="object-model"></a>Объектная модель

Клиентская библиотека Azure Key Vault для .NET позволяет управлять ключами и связанными с ними ресурсами, такими как сертификаты и секреты. В приведенных ниже примерах кода показано, как создать клиент, а также как задать, извлечь и удалить секрет.

Полный текст консольного приложения доступен по адресу https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app.

## <a name="code-examples"></a>Примеры кода

### <a name="add-directives"></a>Добавление директив

Добавьте в верхнюю часть кода следующие директивы:

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=directives)]

### <a name="authenticate-and-create-a-client"></a>Аутентификация и создание клиента

Аутентификация в хранилище ключей и создание клиента хранилища ключей зависят от переменных среды, приведенных на шаге [настройки переменные среды](#set-environmental-variables) выше. Имя хранилища ключей расширяется до универсального кода ресурса (URI) хранилища ключей в формате "https://\<имя_хранилища_ключей\>.vault.azure.net".

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=authenticate)]

### <a name="save-a-secret"></a>Сохранение секрета

Теперь, когда приложение прошло аутентификацию, вы можете разместить секрет в хранилище ключей с помощью [метода client.SetSecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync). Для этого нужно указать имя секрета — в данном примере это mySecret.  

[!code-csharp[Set secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=setsecret)]

Команда [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) позволяет убедиться, что секрет задан успешно:

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Получение секрета

Теперь вы можете получить ранее заданное значение с помощью [метода client.GetSecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

[!code-csharp[Get secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=getsecret)]

Секрет теперь сохраняется как `secret.Value`.

### <a name="delete-a-secret"></a>Удаление секрета.

Наконец, давайте удалим секрет из хранилища ключей с помощью [метода client.DeleteSecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

[!code-csharp[Delete secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=deletesecret)]

Команда [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) позволяет убедиться, что секрет успешно удален.

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если ресурсы больше не нужны, вы можете использовать интерфейс командной строки Azure или Azure PowerShell, чтобы удалить хранилище ключей и соответствующую группу ресурсов.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Пример кода

```csharp
using System;
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;

namespace key_vault_console_app
{
    class Program
    {
        static void Main(string[] args)
        {
            string secretName = "mySecret";

            string keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
            var kvUri = "https://" + keyVaultName + ".vault.azure.net";

            var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());

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
}
```


## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы создали хранилище ключей, сохранили в нем секрет и извлекли его. Полный текст консольного приложения доступен [на сайте GitHub](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app).

Чтобы получить дополнительные сведения о Key Vault и его интеграции в приложения, ознакомьтесь с перечисленными ниже статьями.

- [Проверка подлинности с взаимодействием между службами в Azure Key Vault с помощью .NET](service-to-service-authentication.md)
- [Обзор Azure Key Vault](key-vault-overview.md)
- [Руководство разработчика Azure Key Vault](key-vault-developers-guide.md)
- Сведения о [ключах, секретах и сертификатах](about-keys-secrets-and-certificates.md)
- [Рекомендации по Azure Key Vault](key-vault-best-practices.md)
