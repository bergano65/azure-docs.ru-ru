---
title: Краткое руководство по использованию клиентской библиотеки Azure Key Vault для Python. Управление секретами
description: Узнайте, как создавать, извлекать и удалять секреты из Azure Key Vault с помощью клиентской библиотеки Python.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: b31f22f9a3909df308fdcfa994833887828f2539
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87876365"
---
# <a name="quickstart-azure-key-vault-secrets-client-library-for-python"></a>Краткое руководство. Использование клиентской библиотеки секретов Azure Key Vault для Python

Приступите к работе с клиентской библиотекой Azure Key Vault для Python. Чтобы установить пакет и испробовать пример кода для выполнения базовых задач, выполните описанные ниже шаги.

Хранилище ключей Azure помогает защитить криптографические ключи и секреты, используемые облачными приложениями и службами. Клиентская библиотека Azure Key Vault для Python позволяет выполнять следующие задачи:

- Повысьте уровень безопасности и увеличьте контроль над ключами и паролями.
- Создавайте и импортируйте ключи шифрования за считаные минуты.
- Сократите время задержки с помощью масштабирования в облаке и глобального резервирования.
- Упрощение и автоматизация задач, связанных с TLS- и SSL-сертификатами.
- Используйте модули HSM, отвечающие стандартам FIPS 140-2 уровня 2.

[Справочная документация по API](/python/api/overview/azure/keyvault-secrets-readme?view=azure-python) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault) | [Пакет (Python Package Index)](https://pypi.org/project/azure-keyvault/)

## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Python 2.7, Python 3.5.3 или более поздней версии.
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) или [Azure PowerShell](/powershell/azure/).

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

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

### <a name="create-a-service-principal"></a>Создание субъекта-службы

[!INCLUDE [Create a service principal](../../../includes/key-vault-sp-creation.md)]

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Предоставление субъекту-службе доступа к хранилищу ключей

[!INCLUDE [Give the service principal access to your key vault](../../../includes/key-vault-sp-kv-access.md)]

#### <a name="set-environmental-variables"></a>Настройка переменных среды

[!INCLUDE [Set environmental variables](../../../includes/key-vault-set-environmental-variables.md)]

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

client = SecretClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-secret"></a>Сохранение секрета

Теперь, когда приложение прошло проверку подлинности, вы можете разместить секрет в хранилище ключей с помощью метода client.SetSecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync). Для этого нужно указать имя секрета. В данном примере это mySecret.  

```python
client.set_secret(secretName, secretValue)
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
client.delete_secret(secretName)
```

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

## <a name="sample-code"></a>Образец кода

```python
import os
import cmd
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = f"https://{keyVaultName}.vault.azure.net"

credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)

secretName = "mySecret"

print("Input the value of your secret > ")
secretValue = raw_input()

print(f"Creating a secret in {keyVaultName} called '{secretName}' with the value '{secretValue}` ...")

client.set_secret(secretName, secretValue)

print(" done.")

print("Forgetting your secret.")
secretValue = ""
print(f"Your secret is {secretValue}.")

print(f"Retrieving your secret from {keyVaultName}.")

retrieved_secret = client.get_secret(secretName)

print(f"Your secret is '{retrieved_secret.value}'.")
print(f"Deleting your secret from {keyVaultName} ...")

client.delete_secret(secretName)

print(" done.")
```

## <a name="next-steps"></a>Дальнейшие действия

В этом кратком руководстве вы создали хранилище ключей, сохранили в нем секрет и извлекли его. Дополнительные сведения о Key Vault и его интеграции в приложения см. в следующих статьях.

- [Обзор Azure Key Vault](../general/overview.md)
- [Руководство разработчика Azure Key Vault](../general/developers-guide.md)
- [Рекомендации по Azure Key Vault](../general/best-practices.md)
