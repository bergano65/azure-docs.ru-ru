---
title: включить файл
description: включить файл
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: 4be8821a949527fefcc9005b1de7f4f7c438c568
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90945748"
---
## <a name="prerequisites"></a>Предварительные требования

- Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) бесплатно.
- [Python](https://www.python.org/downloads/) 2.7, 3.5 или более поздней версии.
- Активный ресурс Служб коммуникации и строка подключения. [Создайте ресурс Служб коммуникации.](../create-communication-resource.md)

## <a name="setting-up"></a>Настройка

### <a name="create-a-new-python-application"></a>Создание приложения Python

1. Откройте терминал или командное окно, создайте каталог для своего приложения и перейдите к нему.

   ```console
   mkdir user-tokens-quickstart && cd user-tokens-quickstart
   ```

1. Используйте текстовый редактор, чтобы создать файл с именем **issue-tokens.py** в корневом каталоге проекта и добавить структуру для программы, включая базовую обработку исключений. В следующих разделах показано, как добавить в этот файл весь исходный код для примера из этого краткого руководства.

   ```python
   import os
   from azure.communication.administration import CommunicationIdentityClient

   try:
      print('Azure Communication Services - User Access Tokens Quickstart')
      # Quickstart code goes here
   except Exception as ex:
      print('Exception:')
      print(ex)
   ```

### <a name="install-the-package"></a>Установка пакета

Оставаясь в каталоге приложения, установите пакет клиентской библиотеки Python для администрирования Служб коммуникации Azure с помощью команды `pip install`.

```console
pip install azure-communication-administration
```

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

## <a name="authenticate-the-client"></a>Аутентификация клиента

Создайте экземпляр `CommunicationIdentityClient` с использованием строки подключения. Приведенный ниже код извлекает строку подключения для ресурса из переменной среды с именем `COMMUNICATION_SERVICES_CONNECTION_STRING`. См. сведения о том, как [управлять строкой подключения ресурса](../create-communication-resource.md#store-your-connection-string).

Добавьте этот код в блок `try`.

```python
# This code demonstrates how to fetch your connection string
# from an environment variable.
connection_string = os.environ['COMMUNICATION_SERVICES_CONNECTION_STRING']

# Instantiate the identity client
client = CommunicationIdentityClient.from_connection_string(connection_string)
```

## <a name="create-a-user"></a>Создание пользователя

Службы коммуникации Azure позволяют использовать упрощенный каталог удостоверений. Чтобы создать новую запись в каталоге с уникальным идентификатором (`Id`), используйте метод `create_user`. Необходимо обеспечить сопоставление между пользователями приложения и удостоверениями, созданными Службами коммуникации (например, храня удостоверения в базе данных сервера приложений).

```python
user = client.create_user()
print("\nCreated a user with ID: " + user.identifier + ":")
```

## <a name="issue-user-access-tokens"></a>Выдача маркеров доступа пользователей

Чтобы выдать маркер доступа для пользователя Служб коммуникации, используйте метод `issue_token`. Если не указать необязательный параметр `user`, новый пользователь будет создан и возвращен вместе с маркером.

```python
# Issue an access token with the "voip" scope for a new user
token_result = client.issue_token(user, ["voip"])
expires_on = token_result.expires_on.strftime('%d/%m/%y %I:%M %S %p')
print("\nIssued a token with 'voip' scope that expires at " + expires_on + ":")
print(token_result.token)
```

Маркеры доступа пользователей — это кратковременные учетные данные, которые необходимо повторно выдавать, чтобы предотвратить сбои в работе служб. Свойство ответа `expires_on` указывает на время существования маркера.

## <a name="revoke-user-access-tokens"></a>Отзыв маркеров доступа пользователей

Иногда может потребоваться явно отозвать маркеры доступа пользователя, например когда пользователь изменяет пароль, используемый для проверки подлинности в службе. Такая возможность реализуется посредством клиентской библиотеки администрирования для Служб коммуникации Azure.

```python  
client.revoke_tokens(user)
print("\nSuccessfully revoked all tokens for user with ID: " + user.identifier)
```

## <a name="delete-a-user"></a>Удаление пользователя

При удалении удостоверения отзываются все активные маркеры и запрещается выдача последующих маркеров для удостоверений. Вместе с этим удаляется и все хранимое содержимое, связанное с пользователем.

```python
client.delete_user(user)
print("\nDeleted the user with ID: " + user.identifier)
```

## <a name="run-the-code"></a>Выполнение кода

В окне консоли перейдите в каталог, содержащий файл *issue-token.py*, а затем выполните команду `python`, чтобы запустить приложение.

```console
python ./issue-token.py
```
