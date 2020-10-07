---
title: Краткое руководство по использованию клиентской библиотеки Azure Key Vault для Python. Управление ключами
description: Узнайте, как создавать, извлекать и удалять ключи из Azure Key Vault с помощью клиентской библиотеки Python.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: 44942067756f82c224decc218de17bf7dbc69734
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2020
ms.locfileid: "89482145"
---
# <a name="quickstart-azure-key-vault-keys-client-library-for-python"></a>Краткое руководство. Использование клиентской библиотеки ключей Azure Key Vault для Python

Приступите к работе с клиентской библиотекой Azure Key Vault для Python. Чтобы установить пакет и испробовать пример кода для выполнения базовых задач, выполните описанные ниже шаги. Используя Key Vault для хранения криптографических ключей, вам не надо сохранять их в коде. Это повышает безопасность приложения.

[Справочная документация по API](/python/api/overview/azure/keyvault-keys-readme?view=azure-python) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys) | [Пакет (Python Package Index)](https://pypi.org/project/azure-keyvault-keys/)

## <a name="set-up-your-local-environment"></a>Настройка локальной среды

[!INCLUDE [Set up your local environment](../../../includes/key-vault-python-qs-setup.md)]

7. Установите библиотеку ключей Azure Key Vault:

    ```terminal
    pip install azure-keyvault-keys
    ```

## <a name="create-a-resource-group-and-key-vault"></a>Создание группы ресурсов и хранилища ключей

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

## <a name="give-the-service-principal-access-to-your-key-vault"></a>Предоставление субъекту-службе доступа к хранилищу ключей

Выполните следующую команду [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy), чтобы авторизовать субъект-службу для удаления, получения, перечисления и создания операций с ключами. 

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az keyvault set-policy --name %KEY_VAULT_NAME% --spn %AZURE_CLIENT_ID% --resource-group KeyVault-PythonQS-rg --key-permissions delete get list create
```

# <a name="bash"></a>[bash](#tab/bash)

```azurecli
az keyvault set-policy --name $KEY_VAULT_NAME --spn $AZURE_CLIENT_ID --resource-group KeyVault-PythonQS-rg --key-permissions delete get list create
```

---

Эта команда зависит от переменных среды `KEY_VAULT_NAME` и `AZURE_CLIENT_ID`, созданных на предыдущих шагах.

Дополнительные сведения см. в статье [Назначение политики доступа к Key Vault](../general/assign-access-policy-cli.md).

## <a name="create-the-sample-code"></a>Создание примера кода

Клиентская библиотека Azure Key Vault для Python позволяет управлять криптографическими ключами и связанными с ними ресурсами, такими как сертификаты и секреты. В приведенном примере кода показано, как создать клиент, а также как задать, извлечь и удалить секрет.

Создайте файл с именем *kv_keys.py*, который содержит этот код.

```python
import os
from azure.keyvault.keys import KeyClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)

keyName = input("Input a name for your key > ")

print(f"Creating a key in {keyVaultName} called '{keyName}' ...")

rsa_key = client.create_rsa_key(keyName, size=2048)

print(" done.")

print(f"Retrieving your key from {keyVaultName}.")

retrieved_key = client.get_key(keyName)

print(f"Key with name '{retrieved_key.name}' was found.")
print(f"Deleting your key from {keyVaultName} ...")

poller = client.begin_delete_key(keyName)
deleted_key = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>Выполнение кода

Убедитесь, что код из предыдущего раздела находится в файле с именем *kv_keys.py*. Затем выполните следующую команду, чтобы запустить код:

```terminal
python kv_keys.py
```

- Если происходят ошибки разрешений, убедитесь, что вы [выполнили команду `az keyvault set-policy`](#give-the-service-principal-access-to-your-key-vault).
- Повторное выполнение кода с тем же именем ключа может вызвать ошибку (Conflict) Key <name> is currently in a deleted but recoverable state. ((Конфликт) Ключ <name> в настоящее время находится в удаленном состоянии, но допускающем восстановление). Используйте другое имя ключа.

## <a name="code-details"></a>Сведения о коде

### <a name="authenticate-and-create-a-client"></a>Аутентификация и создание клиента

В приведенном выше коде объект [`DefaultAzureCredential`](/python/api/azure-identity/azure.identity.defaultazurecredential?view=azure-python) использует переменные среды, созданные вами для субъекта-службы. Вы предоставляете эти учетные данные при каждом создании клиентского объекта из библиотеки Azure, например [`KeyClient`](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python), а также универсальный код ресурса (URI), с которым вы хотите работать через этот клиент:

```python
credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)
```

## <a name="save-a-key"></a>Сохранение ключа

После получения клиентского объекта для хранилища ключей можно сохранить ключ с помощью метода [create_rsa_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#create-rsa-key-name----kwargs-): 

```python
rsa_key = client.create_rsa_key(keyName, size=2048)
```

Кроме того, можно использовать метод [create_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#create-key-name--key-type----kwargs-) или [create_ec_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#create-ec-key-name----kwargs-).

Вызов метода `create` создает вызов REST API Azure для хранилища ключей.

При обработке запроса Azure проверяет подлинность удостоверения вызывающего объекта (субъекта-службы) с помощью объекта учетных данных, предоставленных клиенту.

Azure также проверяет, авторизован ли вызывающий объект для выполнения запрошенного действия. Вы предоставили эту авторизацию субъекту-службе ранее с помощью [команды `az keyvault set-policy` ](#give-the-service-principal-access-to-your-key-vault).

## <a name="retrieve-a-key"></a>Получение ключа

Чтобы прочитать ключ из Key Vault, используйте метод [get_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#get-key-name--version-none----kwargs-):

```python
retrieved_key = client.get_key(keyName)
 ```

Команда Azure CLI [az keyvault key show](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-show) позволяет также убедиться, что ключ задан успешно.

### <a name="delete-a-key"></a>Удаление ключа

Чтобы удалить ключ, используйте метод [begin_delete_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#begin-delete-key-name----kwargs-):

```python
poller = client.begin_delete_key(keyName)
deleted_key = poller.result()
```

Метод `begin_delete_key` является асинхронным и возвращает объект модуля опроса. При вызове `result` модуля опроса метод ожидает его завершения.

Команда Azure CLI [az keyvault key show](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-show) позволяет убедиться, что ключ удален.

После удаления ключ остается в удаленном, но восстанавливаемом состоянии в течение некоторого времени. При повторном выполнении кода используйте другое имя ключа.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите также поэкспериментировать с [сертификатами](../certificates/quick-create-python.md) и [секретами](../secrets/quick-create-python.md), можно повторно использовать Key Vault, созданный в этой статье.

В противном случае после завершения работы с созданными в этой статье ресурсами используйте следующую команду, чтобы удалить группу ресурсов и все содержащиеся в ней ресурсы:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Дальнейшие действия

- [Обзор хранилища ключей Azure](../general/overview.md)
- [Руководство разработчика Azure Key Vault](../general/developers-guide.md)
- [Рекомендации по использованию Key Vault](../general/best-practices.md)
- [Проверка подлинности с помощью Key Vault](../general/authentication.md)
