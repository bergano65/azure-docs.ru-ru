---
title: Краткое руководство по использованию клиентской библиотеки Azure Key Vault для .NET
description: Сведения о критериях, применяемых к формату и содержимому для создания кратких руководств по клиентским библиотекам Azure SDK.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 05/20/2019
ms.service: key-vault
ms.topic: quickstart
ms.openlocfilehash: d24323996e222caf6456372cbc65681d2055c3db
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996646"
---
# <a name="quickstart-azure-key-vault-client-library-for-net"></a>Краткое руководство. Клиентская библиотека Azure Key Vault для .NET

Начало работы с клиентской библиотекой Azure Key Vault для .NET. Чтобы установить пакет и испробовать пример кода для выполнения базовых задач, выполните описанные ниже шаги.

Хранилище ключей Azure помогает защитить криптографические ключи и секреты, используемые облачными приложениями и службами. Клиентская библиотека Azure Key Vault для .NET позволяет выполнять следующие задачи:

- Повысьте уровень безопасности и увеличьте контроль над ключами и паролями.
- Создавайте и импортируйте ключи шифрования за считаные минуты.
- Сократите время задержки с помощью масштабирования в облаке и глобального резервирования.
- Упрощение и автоматизация задач, связанных с SSL- и TLS-сертификатами.
- Используйте модули HSM, отвечающие стандартам FIPS 140-2 уровня 2.

[Справочная документация по API](/dotnet/api/overview/azure/key-vault?view=azure-dotnet) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/KeyVault) | [Пакет (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/).

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Пакет SDK для .NET Core 2.1 или более поздней версии](https://dotnet.microsoft.com/download/dotnet-core/2.1).
* [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) или [Azure PowerShell](/powershell/azure/overview).

В этом кратком руководстве предполагается, что вы выполняете команды `dotnet`, [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) и Windows в терминале Windows (например, [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-6), [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6) или [Azure Cloud Shell](https://shell.azure.com/)).

## <a name="setting-up"></a>Настройка

### <a name="create-new-net-console-app"></a>Создание консольного приложения .NET

Создайте консольное приложение .NET Core на C# в предпочитаемой интегрированной среде разработки или редакторе.

В окне консоли выполните команду `dotnet new`, чтобы создать консольное приложение с именем `akv-dotnet`.


```console
dotnet new console -n akvdotnet
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
dotnet add package Microsoft.Azure.KeyVault
```

Для этого краткого руководства вам также понадобится установить следующие пакеты:

```console
dotnet add package System.Threading.Tasks
dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory
dotnet add package Microsoft.Azure.Management.ResourceManager.Fluent
```

### <a name="create-a-resource-group-and-key-vault"></a>Создание группы ресурсов и хранилища ключей

В этом кратком руководстве используется предварительно созданное хранилище ключей Azure. Хранилище ключей можно создать, выполнив действия, описанные в кратких руководствах по [Azure CLI](quick-create-cli.md), [Azure PowerShell](quick-create-powershell.md) или [порталу Azure](quick-create-portal.md). Можно также выполнить команду Azure CLI, которая предложена ниже:

> [!Important]
> Каждый Key Vault должен иметь уникальное имя. В следующем примере показан процесс создания Key Vault с именем *myKV*. Вам следует указать другое имя и использовать его далее на всем протяжении краткого руководства.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Создание субъекта-службы

Самым простым методом для проверки подлинности облачного приложения .NET является управляемое удостоверение. Дополнительные сведения см. в статье [Проверка подлинности с взаимодействием между службами в Azure Key Vault с помощью .NET](service-to-service-authentication.md). Но для простоты в этом кратком руководстве создается консольное приложение .NET. Для проверки подлинности в Azure приложение рабочего стола должно использовать субъект-службу.
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

Запишите значения clientId и clientSecret, так как они нам потребуются на шаге [Проверка подлинности в хранилище ключей](#authenticate-to-your-key-vault).

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Предоставление субъекту-службе доступа к хранилищу ключей

Создайте политику доступа для хранилища ключей, которая предоставляет разрешение субъекту-службе, передавая clientId в команду [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy). Предоставьте субъекту-службе разрешения get, list и set для ключей и секретов.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

## <a name="object-model"></a>Объектная модель

Клиентская библиотека Azure Key Vault для .NET позволяет управлять ключами и связанными с ними ресурсами, такими как сертификаты и секреты. В приведенных ниже примерах кода показано, как задать секрет и получить секретный код.

Полный текст консольного приложения доступен по адресу https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet.

## <a name="code-examples"></a>Примеры кода

### <a name="add-directives"></a>Добавление директив

Добавьте в верхнюю часть кода следующие директивы:

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=directives)]

### <a name="authenticate-to-your-key-vault"></a>Проверка подлинности в хранилище ключей

В этом кратком руководстве по .NET используются переменные среды для хранения учетных данных, которые нельзя размещать в коде. 

Перед сборкой и запуском приложения выполните команду `setx`, чтобы задать переменные среды `akvClientId`, `akvClientSecret`, `akvTenantId` и `akvSubscriptionId` с записанными выше значениями.

```console
setx akvClientId <your-clientID>

setx akvClientSecret <your-clientSecret>
````

При каждом вызове `setx` вы будете получать ответ об успешном выполнении "SUCCESS: Specified value was saved." (Указанное значение сохранено.)

Укажите эти переменные среды в строках кода, а затем выполните для приложения проверку подлинности, передавая переменные в [класс KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient):

[!code-csharp[Authentication](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=authentication)]

### <a name="save-a-secret"></a>Сохранение секрета

Теперь, когда приложение прошло проверку подлинности, вы можете разместить секрет в хранилище ключей с помощью [метода SetSecretAsync](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync). Для этого вам нужен URL-адрес хранилища ключей в формате `https://<your-unique-keyvault-name>.vault.azure.net/secrets/`. Также потребуется имя секрета — в нашем примере это "mySecret".  Вы можете сохранить эти строковые значения в переменные, чтобы использовать их повторно.

[!code-csharp[Set secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=setsecret)]

Команда [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) позволяет убедиться, что секрет задан успешно:

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Получение секрета

Теперь вы можете получить ранее заданное значение с помощью [метода GetSecretAsync](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

[!code-csharp[Get secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=getsecret)]

Секрет теперь сохраняется как `keyvaultSecret.Value;`.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если ресурсы больше не нужны, вы можете использовать интерфейс командной строки Azure или Azure PowerShell, чтобы удалить хранилище ключей и соответствующую группу ресурсов.

```azurecli
az group delete -g "myResourceGroup" -l "EastUS" 
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы создали хранилище ключей, сохранили в нем секрет и извлекли его. Полный текст консольного приложения доступен [на сайте GitHub](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet).

Чтобы получить дополнительные сведения о Key Vault и его интеграции в приложения, ознакомьтесь с перечисленными ниже статьями.

- [Проверка подлинности с взаимодействием между службами в Azure Key Vault с помощью .NET](service-to-service-authentication.md)
- [Обзор Azure Key Vault](key-vault-overview.md)
- [Руководство разработчика Azure Key Vault](key-vault-developers-guide.md)
- Сведения о [ключах, секретах и сертификатах](about-keys-secrets-and-certificates.md)
- [Рекомендации по Azure Key Vault](key-vault-best-practices.md)
