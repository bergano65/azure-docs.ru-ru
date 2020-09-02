---
title: Краткое руководство по использованию клиентской библиотеки Azure Key Vault для .NET (версии 4)
description: Узнайте, как создавать, извлекать и удалять секреты в Azure Key Vault с помощью клиентской библиотеки .NET (версии 4).
author: msmbaldwin
ms.author: mbaldwin
ms.date: 03/12/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: cf9c34dc2dde5c41edb7991451aebf5d90104196
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2020
ms.locfileid: "89003258"
---
# <a name="quickstart-azure-key-vault-client-library-for-net-sdk-v4"></a>Краткое руководство. Использование клиентской библиотеки Azure Key Vault для .NET (пакет SDK версии 4)

Начало работы с клиентской библиотекой Azure Key Vault для .NET. Чтобы установить пакет и испробовать пример кода для выполнения базовых задач, выполните описанные ниже шаги.

Хранилище ключей Azure помогает защитить криптографические ключи и секреты, используемые облачными приложениями и службами. Клиентская библиотека Azure Key Vault для .NET позволяет выполнять следующие задачи:

- Повысьте уровень безопасности и увеличьте контроль над ключами и паролями.
- Создавайте и импортируйте ключи шифрования за считаные минуты.
- Сократите время задержки с помощью масштабирования в облаке и глобального резервирования.
- Упрощение и автоматизация задач, связанных с TLS- и SSL-сертификатами.
- Используйте модули HSM, отвечающие стандартам FIPS 140-2 уровня 2.

[Справочная документация по API](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | [Пакет (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/).

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Пакет SDK для .NET Core 3.1 или более поздней версии](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) или [Azure PowerShell](/powershell/azure/).

В этом кратком руководстве предполагается, что вы выполняете команды `dotnet`, [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) и Windows в терминале Windows (например, [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-6), [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6) или [Azure Cloud Shell](https://shell.azure.com/)).

## <a name="setting-up"></a>Настройка

### <a name="create-new-net-console-app"></a>Создание консольного приложения .NET

В окне консоли выполните команду `dotnet new`, чтобы создать консольное приложение .NET с именем `key-vault-console-app`.

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
dotnet add package Azure.Security.KeyVault.Secrets
```

Для этого краткого руководства вам также понадобится установить следующие пакеты:

```console
dotnet add package Azure.Identity
```

### <a name="create-a-resource-group-and-key-vault"></a>Создание группы ресурсов и хранилища ключей

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

### <a name="create-a-service-principal"></a>Создание субъекта-службы

[!INCLUDE [Create a service principal](../../../includes/key-vault-sp-creation.md)]

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Предоставление субъекту-службе доступа к хранилищу ключей

[!INCLUDE [Give the service principal access to your key vault](../../../includes/key-vault-sp-kv-access.md)]

#### <a name="set-environmental-variables"></a>Настройка переменных среды

[!INCLUDE [Set environmental variables](../../../includes/key-vault-set-environmental-variables.md)]

## <a name="object-model"></a>Объектная модель

Клиентская библиотека Azure Key Vault для .NET позволяет управлять ключами и связанными с ними ресурсами, такими как сертификаты и секреты. В приведенных ниже примерах кода показано, как создать клиент, а также как задать, извлечь и удалить секрет.

Полный текст консольного приложения доступен по адресу https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app.

## <a name="code-examples"></a>Примеры кода

### <a name="add-directives"></a>Добавление директив

Добавьте в верхнюю часть кода следующие директивы:

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=directives)]

### <a name="authenticate-and-create-a-client"></a>Аутентификация и создание клиента

Аутентификация в хранилище ключей и создание клиента хранилища ключей зависят от переменных среды, приведенных на шаге [настройки переменные среды](#set-environmental-variables) выше. Имя хранилища ключей расширяется до URI хранилища ключей в формате https://\<your-key-vault-name\>.vault.azure.net. Для проверки подлинности в хранилище ключей в следующем примере кода используется функция [DefaultAzureCredential()](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet), которая считывает переменные среды для получение маркера доступа. 

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=authenticate)]

### <a name="save-a-secret"></a>Сохранение секрета

Теперь, когда приложение прошло аутентификацию, вы можете разместить секрет в хранилище ключей с помощью [метода client.SetSecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync). Для этого нужно указать имя секрета — в данном примере это mySecret.  

[!code-csharp[Set secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=setsecret)]

Команда [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) позволяет убедиться, что секрет задан успешно:

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

```azurepowershell
(Get-AzKeyVaultSecret -VaultName <your-unique-keyvault-name> -Name mySecret).SecretValueText
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

```azurepowershell
(Get-AzKeyVaultSecret -VaultName <your-unique-keyvault-name> -Name mySecret).SecretValueText
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если ресурсы больше не нужны, вы можете использовать интерфейс командной строки Azure или Azure PowerShell, чтобы удалить хранилище ключей и соответствующую группу ресурсов.

### <a name="delete-a-key-vault"></a>Удаление хранилища ключей
```azurecli
az keyvault delete --name <your-unique-keyvault-name>
```

```powershell
Remove-AzKeyVault -VaultName <your-unique-keyvault-name>
```

### <a name="purge-a-key-vault"></a>Очистка хранилища ключей
```azurecli
az keyvault purge --location eastus --name <your-unique-keyvault-name>
```

```powershell
Remove-AzKeyVault -VaultName <your-unique-keyvault-name> -InRemovedState -Location eastus
```

### <a name="delete-a-resource-group"></a>Удаление группы ресурсов

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Образец кода

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


## <a name="next-steps"></a>Дальнейшие действия

В этом кратком руководстве вы создали хранилище ключей, сохранили в нем секрет и извлекли его. Полный текст консольного приложения доступен [на сайте GitHub](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app).

Дополнительные сведения о Key Vault и его интеграции в приложения см. в следующих статьях.

- [Обзор Azure Key Vault](../general/overview.md)
- [Руководство разработчика Azure Key Vault](../general/developers-guide.md)
- [Рекомендации по Azure Key Vault](../general/best-practices.md)
