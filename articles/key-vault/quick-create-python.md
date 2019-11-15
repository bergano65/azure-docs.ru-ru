---
title: Краткое руководство по использованию клиентской библиотеки Azure Key Vault для Python
description: Узнайте, как создавать, извлекать и удалять секреты из Azure Key Vault с помощью клиентской библиотеки Python.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.topic: quickstart
ms.openlocfilehash: 3be246402c4acd63aee3518f2333d50ec307e9c0
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73647895"
---
# <a name="quickstart-azure-key-vault-client-library-for-python"></a>Краткое руководство. Использование клиентской библиотеки Azure Key Vault для Python

Приступите к работе с клиентской библиотекой Azure Key Vault для Python. Чтобы установить пакет и испробовать пример кода для выполнения базовых задач, выполните описанные ниже шаги.

Хранилище ключей Azure помогает защитить криптографические ключи и секреты, используемые облачными приложениями и службами. Клиентская библиотека Azure Key Vault для Python позволяет выполнять следующие задачи:

- Повысьте уровень безопасности и увеличьте контроль над ключами и паролями.
- Создавайте и импортируйте ключи шифрования за считаные минуты.
- Сократите время задержки с помощью масштабирования в облаке и глобального резервирования.
- Упрощение и автоматизация задач, связанных с SSL- и TLS-сертификатами.
- Используйте модули HSM, отвечающие стандартам FIPS 140-2 уровня 2.

[Справочная документация по API](/python/api/overview/azure/key-vault?view=azure-python) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault) | [Пакет (Python Package Index)](https://pypi.org/project/azure-keyvault/)

## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Python 2.7, Python 3.5.3 или более поздней версии.
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) или [Azure PowerShell](/powershell/azure/overview).

В этом кратком руководстве предполагается, что вы используете [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) в окне терминала Linux.

## <a name="setting-up"></a>Настройка

### <a name="install-the-package"></a>Установка пакета

Через окно консоли установите клиентскую библиотеку секретов Azure Key Vault для Python.

```console
pip install azure-keyvault-secrets
```

Для этого краткого руководства вам также понадобится установить пакет azure.identity.

```console
pip install azure.identity
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

Самый простой способ проверить подлинность облачного приложения NET — с помощью управляемых удостоверений. Дополнительные сведения см. в статье [Provide Key Vault authentication with a managed identity](managed-identity.md) (Предоставление аутентификации Key Vault с помощью управляемого удостоверения). Но для простоты в этом кратком руководстве создается консольное приложение .NET. Для проверки подлинности в Azure приложение рабочего стола должно использовать субъект-службу и политику управления доступом.

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

Запишите значения clientId и clientSecret, так как они нам потребуются далее на шаге [настройки переменных среды](#set-environmental-variables).

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Предоставление субъекту-службе доступа к хранилищу ключей

Создайте политику доступа для хранилища ключей, которая предоставляет разрешение субъекту-службе, передавая clientId в команду [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy). Предоставьте субъекту-службе разрешения get, list и set для ключей и секретов.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

#### <a name="set-environmental-variables"></a>Настройка переменных среды

Метод DefaultAzureCredential в нашем приложении использует три переменные среды: `AZURE_CLIENT_ID`, `AZURE_CLIENT_SECRET` и `AZURE_TENANT_ID`. Присвойте этим переменным значения clientId, clientSecret и tenantId, записанные на шаге [создания субъекта-службы](#create-a-service-principal), используя формат `export VARNAME=VALUE`. (Здесь задаются только переменные для текущей оболочки и процессов, созданных в этой оболочке. Чтобы добавить эти переменные в среду на постоянной основе, измените файл `/etc/environment `.) 

Кроме того, необходимо будет сохранить имя хранилища ключей как переменную среды `KEY_VAULT_NAME`.

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````

## <a name="object-model"></a>Объектная модель

Клиентская библиотека Azure Key Vault для Python позволяет управлять ключами и связанными с ними ресурсами, такими как сертификаты и секреты. В приведенных ниже примерах кода показано, как создать клиент, а также как задать, извлечь и удалить секрет.

Полный текст консольного приложения доступен по адресу https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app.

## <a name="code-examples"></a>Примеры кода

### <a name="add-directives"></a>Добавление директив

Добавьте в верхнюю часть кода следующие директивы:

```python
import os
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential
```

### <a name="authenticate-and-create-a-client"></a>Аутентификация и создание клиента

Аутентификация в хранилище ключей и создание клиента хранилища ключей зависят от переменных среды, приведенных на шаге [настройки переменные среды](#set-environmental-variables) выше. Имя хранилища ключей расширяется до универсального кода ресурса (URI) хранилища ключей в формате "https://<имя_хранилища_ключей>.vault.azure.net".

```python
credential = DefaultAzureCredential()

client = SecretClient(vault_endpoint=KVUri, credential=credential)
```

### <a name="save-a-secret"></a>Сохранение секрета

Теперь, когда приложение прошло аутентификацию, вы можете разместить секрет в хранилище ключей с помощью [метода client.SetSecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync). Для этого нужно указать имя секрета — в данном примере это mySecret.  

```python
client.set_secret(secretName, secretValue);
```

Команда [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) позволяет убедиться, что секрет задан успешно:

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Получение секрета

Теперь вы можете получить ранее заданное значение с помощью [метода client.GetSecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

```python
retrieved_secret = client.get_secret(secretName)
 ```

Секрет теперь сохраняется как `retrieved_secret.value`.

### <a name="delete-a-secret"></a>Удаление секрета.

Наконец, давайте удалим секрет из хранилища ключей с помощью [метода client.DeleteSecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

```python
client.delete_secret(secretName);
```

Команда [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) позволяет убедиться, что секрет успешно удален.

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если ресурсы больше не нужны, вы можете использовать интерфейс командной строки Azure или Azure PowerShell, чтобы удалить хранилище ключей и соответствующую группу ресурсов.

```azurecli
az group delete -g "myResourceGroup" -l "EastUS" 
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Пример кода

```python
import os
import cmd
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential

secretName = "mySecret";

keyVaultName = os.environ["KEY_VAULT_NAME"];
KVUri = "https://" + keyVaultName + ".vault.azure.net";

credential = DefaultAzureCredential()

client = SecretClient(vault_endpoint=KVUri, credential=credential)

print("Input the value of your secret > ");
secretValue = raw_input();

print("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...");

client.set_secret(secretName, secretValue);

print(" done.");

print("Forgetting your secret.");
secretValue = "";
print("Your secret is '" + secretValue + "'.");

print("Retrieving your secret from " + keyVaultName + ".");

retrieved_secret = client.get_secret(secretName)

print("Your secret is '" + retrieved_secret.value + "'.");
print("Deleting your secret from " + keyVaultName + " ...");

client.delete_secret(secretName);

print(" done.");
```

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы создали хранилище ключей, сохранили в нем секрет и извлекли его. Полный текст консольного приложения доступен [на сайте GitHub](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app).

Дополнительные сведения о Key Vault и его интеграции в приложения см. в следующих статьях.

- [Обзор Azure Key Vault](key-vault-overview.md)
- [Руководство разработчика Azure Key Vault](key-vault-developers-guide.md)
- Сведения о [ключах, секретах и сертификатах](about-keys-secrets-and-certificates.md)
- [Рекомендации по Azure Key Vault](key-vault-best-practices.md)