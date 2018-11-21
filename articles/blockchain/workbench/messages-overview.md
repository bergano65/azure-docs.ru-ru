---
title: Обзор сообщений об интеграции Azure Blockchain Workbench
description: Общие сведения об использовании сообщений в Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 11/12/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: f8f3584475415cf9ca19458f6da78d34df37f438
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2018
ms.locfileid: "51614367"
---
# <a name="azure-blockchain-workbench-messaging-integration"></a>Интеграция службы сообщений Azure Blockchain Workbench

Помимо REST API, Azure Blockchain Workbench также предоставляет интеграции на основе обмена сообщениями. Workbench публикует связанные с реестром события через службу "Сетка событий Azure", позволяя нисходящим объектам-получателям получать данные или предпринимать действия на основе этих событий. Для клиентов, которым требуется надежный обмен сообщениями, Azure Blockchain Workbench также доставляет сообщения в конечную точку служебной шины Azure.

## <a name="input-apis"></a>API ввода

Если вы хотите инициировать транзакции из внешних систем для создания пользователей, создания и обновления контрактов, можно использовать API ввода службы сообщений, чтобы осуществлять транзакции в реестре. Пример, демонстрирующий API ввода, см. в репозитории с [примерами интеграции службы сообщений](https://aka.ms/blockchain-workbench-integration-sample).

Ниже перечислены API ввода, которые доступны в настоящее время.

### <a name="create-user"></a>Создать пользователя

Создает пользователя.

Для данного запроса требуются следующие поля:

| **Имя**             | **Описание**                                      |
|----------------------|------------------------------------------------------|
| requestId            | GUID, предоставляемый клиентом                                |
| firstName            | Имя пользователя                              |
| lastName             | Фамилия пользователя                               |
| emailAddress         | Адрес электронной почты пользователя                           |
| externalId           | Идентификатор объекта пользователя Azure AD                      |
| connectionId         | Уникальный идентификатор подключения блокчейна |
| messageSchemaVersion | Версия схемы службы приложений                            |
| messageName          | **CreateUserRequest**                               |

Пример:

``` json
{
    "requestId": "e2264523-6147-41fc-bbbb-edba8e44562d",
    "firstName": "Ali",
    "lastName": "Alio",
    "emailAddress": "aa@contoso.com",
    "externalId": "6a9b7f65-ffff-442f-b3b8-58a35abd1bcd",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateUserRequest"
}
```

Blockchain Workbench возвращает ответ со следующими полями:

| **Имя**              | **Описание**                                                                                                             |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------|
| requestId             | GUID, предоставляемый клиентом |
| userId                | Идентификатор созданного пользователя |
| userChainIdentifier   | Адрес пользователя, который был создан в сети блокчейна. В Ethereum это будет адрес пользователя  **в цепочке** . |
| connectionId          | Уникальный идентификатор подключения блокчейна|
| messageSchemaVersion  | Версия схемы службы приложений |
| messageName           | **CreateUserUpdate** |
| status                | Состояние запроса на создание пользователя.  При успешном выполнении значение будет **Success**. При сбое значение будет **Failure**.     |
| additionalInformation | Предоставляемые дополнительные сведения на основе состояния |

Пример ответа при успешном выполнении запроса на **создание пользователя** из Blockchain Workbench:

``` json
{ 
    "requestId": "e2264523-6147-41fc-bb59-edba8e44562d", 
    "userId": 15, 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateUserUpdate", 
    "status": "Success", 
    "additionalInformation": { } 
} 
```

Если запрос не удалось выполнить, в дополнительных сведениях будут указаны подробности сбоя.

``` json
{
    "requestId": "e2264523-6147-41fc-bb59-edba8e44562d", 
    "userId": 15, 
    "userChainIdentifier": null, 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateUserUpdate", 
    "status": "Failure", 
    "additionalInformation": { 
        "errorCode": 4000, 
        "errorMessage": "User cannot be provisioned on connection." 
    }
}
```

### <a name="create-contract"></a>Создание контракта

Создает контракт.

Для данного запроса требуются следующие поля:

| **Имя**             | **Описание**                                                                                                           |
|----------------------|---------------------------------------------------------------------------------------------------------------------------|
| requestId            | GUID, предоставляемый клиентом |
| userChainIdentifier  | Адрес пользователя, который был создан в сети блокчейна. В Ethereum это адрес пользователя **в цепочке**. |
| applicationName      | Имя приложения |
| workflowName         | Имя рабочего процесса |
| parameters           | Параметры для создания контракта |
| connectionId         | Уникальный идентификатор подключения блокчейна |
| messageSchemaVersion | Версия схемы службы приложений |
| messageName          | **CreateContractRequest** |

Пример:

``` json
{ 
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211", 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "applicationName": "AssetTransfer", 
    "workflowName": "AssetTransfer", 
    "parameters": [ 
        { 
            "name": "description", 
            "value": "a 1969 dodge charger" 
        }, 
        { 
            "name": "price", 
            "value": "12345" 
        } 
    ], 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateContractRequest" 
}
```

Blockchain Workbench возвращает ответ со следующими полями:

| **Имя**                 | **Описание**                                                                   |
|--------------------------|-----------------------------------------------------------------------------------|
| requestId                | GUID, предоставляемый клиентом                                                             |
| contractId               | Уникальный идентификатор контракта в Azure Blockchain Workbench |
| contractLedgerIdentifier | Адрес контракта в реестре                                            |
| connectionId             | Уникальный идентификатор подключения блокчейна                               |
| messageSchemaVersion     | Версия схемы службы приложений                                                         |
| messageName              | **CreateContractUpdate**                                                      |
| status                   | Состояние запроса на создание контракта.  Возможные значения: **Submitted**, **Committed**, **Failure**.  |
| additionalInformation    | Предоставляемые дополнительные сведения на основе состояния                              |

Пример ответа при отправленном запросе на **создание контракта** из Blockchain Workbench:

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Submitted"
    "additionalInformation": { }
}
```

Пример ответа при исполненном запросе на **создание контракта** из Blockchain Workbench:

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Committed",
    "additionalInformation": { }
}
```

Если запрос не удалось выполнить, в дополнительных сведениях будут указаны подробности сбоя.

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": null,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Failure"
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract cannot be provisioned on connection."
    }
}
```

### <a name="create-contract-action"></a>Создание действия контракта

Создает действие контракта.

Для данного запроса требуются следующие поля:

| **Имя**                 | **Описание**                                                                                                           |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------|
| requestId                | GUID, предоставляемый клиентом |
| userChainIdentifier      | Адрес пользователя, который был создан в сети блокчейна. В Ethereum это адрес пользователя **в цепочке**. |
| contractLedgerIdentifier | Адрес контракта в реестре |
| workflowFunctionName     | Имя функции рабочего процесса |
| parameters               | Параметры для создания контракта |
| connectionId             | Уникальный идентификатор подключения блокчейна |
| messageSchemaVersion     | Версия схемы службы приложений |
| messageName              | **CreateContractActionRequest** |

Пример:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398",
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "workflowFunctionName": "modify",
    "parameters": [
        {
            "name": "description",
            "value": "a 1969 dodge charger"
        },
        {
            "name": "price",
            "value": "12345"
        }
    ],
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionRequest"
}
```

Blockchain Workbench возвращает ответ со следующими полями:

| **Имя**              | **Описание**                                                                   |
|-----------------------|-----------------------------------------------------------------------------------|
| requestId             | GUID, предоставляемый клиентом|
| contractId            | Уникальный идентификатор контракта в Azure Blockchain Workbench |
| connectionId          | Уникальный идентификатор подключения блокчейна |
| messageSchemaVersion  | Версия схемы службы приложений |
| messageName           | **CreateContractActionUpdate** |
| status                | Состояние запроса на создание действия контракта. Возможные значения: **Submitted**, **Committed**, **Failure**.                         |
| additionalInformation | Предоставляемые дополнительные сведения на основе состояния |

Пример ответа при отправленном запросе на **создание действия контракта** из Blockchain Workbench:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Submitted",
    "additionalInformation": { }
}
```

Пример ответа при исполненном запросе на **создание действия контракта** из Blockchain Workbench:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Committed"
    "additionalInformation": { }
}
```

Если запрос не удалось выполнить, в дополнительных сведениях будут указаны подробности сбоя.

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Failure"
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract action cannot be provisioned on connection."
    }
}
```

### <a name="input-api-error-codes-and-messages"></a>Коды и сообщения ошибок API ввода

**Код ошибки 4000. Недопустимый запрос**
- Недопустимое connectionId
- Не удалось выполнить десериализацию CreateUserRequest
- Не удалось выполнить десериализацию CreateContractRequest
- Не удалось выполнить десериализацию CreateContractActionRequest
- Приложение {определяемое по имени} не существует
- В приложении {определяемому по имени} нет рабочего процесса
- UserChainIdentifier не существует
- Контракт {определяемый по идентификатору реестра} не существует
- Контракт {определяемый по идентификатору реестра} не имеет функции {имя функции рабочего процесса}
- UserChainIdentifier не существует

**Код ошибки 4090. Ошибка из-за конфликта**
- Пользователь уже существует
- Контракт уже существует
- Действие контракта уже существует

**Код ошибки 5000. Внутренняя ошибка сервера**
- Сообщения об исключении

## <a name="event-notifications"></a>Уведомления о событиях

Уведомления о событиях можно использовать для уведомления пользователей и подчиненных систем о событиях, происходящих в Blockchain Workbench и сети блокчейна, к которой оно подключено. Уведомление о событии можно использовать непосредственно в коде или с такими инструментами, как Logic Apps и Flow, чтобы активировать поток данных в подчиненные системы.

Дополнительные сведения о различных сообщениях, которые могут быть получены, см. в [справочниках по сообщениям об уведомлениях](#notification-message-reference).

### <a name="consuming-event-grid-events-with-azure-functions"></a>Использование событий службы "Сетка событий Azure" с решением "Функции Azure"

Если пользователь хочет получать уведомления о событиях, которые происходят в Blockchain Workbench, через службу "Сетка событий",события из этой службы можно получать, используя решение "Функции Azure".

1. Создайте **приложение-функцию Azure** на портале Azure.
2. Создайте функцию.
3. Найдите шаблон для службы "Сетка событий". Будет показан базовый код шаблона для чтения сообщения. При необходимости измените код.
4. Сохраните функцию. 
5. Выберите службу "Сетка событий" из группы ресурсов Blockchain Workbench.

### <a name="consuming-event-grid-events-with-logic-apps"></a>Использование событий службы "Сетка событий Azure" со службой Logic Apps

1.  Создайте **приложение логики Azure** на портале Azure.
2.  При открытии этого приложения на портале Azure будет предложено выбрать триггер. Выберите **Azure Event Grid -- When a resource event occurs** (Сетка событий Azure — при возникновении события ресурса).
3. При появлении конструктора рабочих процессов вам будет предложено войти в систему.
4. Выберите подписку. Вам нужен ресурс в виде **Microsoft.EventGrid.Topics**. Выберите **имя ресурса** среди доступных в группе ресурсов Azure Blockchain Workbench.
5. Выберите службу "Сетка событий" из группы ресурсов Blockchain Workbench.

## <a name="using-service-bus-topics-for-notifications"></a>Использование разделов служебной шины для уведомлений

Разделы служебной шины могут использоваться для уведомления пользователей о событиях, произошедших в Blockchain Workbench. 

1.  Перейдите к служебной шине в группе ресурсов Workbench.
2.  Выберите **Разделы**.
3.  Выберите **workbench-external**.
4.  Создайте подписку для этого раздела. Получите ключ для нее.
5.  Создайте программу, которая подписывается на события из этой подписки.

### <a name="consuming-service-bus-messages-with-logic-apps"></a>Использование сообщений служебной шины с Logic Apps

1. Создайте **приложение логики Azure** на портале Azure.
2. При открытии этого приложения на портале Azure будет предложено выбрать триггер. Введите **Служебная шина** в поле поиска и выберите соответствующий триггер для типа взаимодействия со служебной шиной. Например, **Service Bus -- When a message is received in a topic subscription (auto-complete)** (Служебная шина — при получении сообщения в подписке раздела (автозавершение)).
3. При отображении конструктора рабочих процессов укажите информацию о подключении для служебной шины.
4. Выберите подписку и укажите раздел **workbench-external**.
5. Разработайте логику приложения, которая использует сообщения из этого триггера.

## <a name="notification-message-reference"></a>Справочник по сообщениям об уведомлениях

В зависимости от **имени операции** сообщения об уведомлениях будут иметь один из следующих типов сообщений.

### <a name="accountcreated"></a>AccountCreated

Указывает, что новая учетная запись была запрошена для добавления в указанную цепочку.

| ИМЯ    | ОПИСАНИЕ  |
|----------|--------------|
| UserId  | Идентификатор созданного пользователя. |
| ChainIdentifier | Адрес пользователя, который был создан в сети блокчейна. В Ethereum это будет адрес пользователя **в цепочке**. |

``` csharp
public class NewAccountRequest : MessageModelBase
{
  public int UserID { get; set; }
  public string ChainIdentifier { get; set; }
}
```

### <a name="contractinsertedorupdated"></a>ContractInsertedOrUpdated

Указывает, что запрос был сделан для вставки или обновления контракта в распределенном реестре.

| ИМЯ | ОПИСАНИЕ |
|-----|--------------|
| ChainID | Уникальный идентификатор цепочки, связанной с запросом |
| BlockId | Уникальный идентификатор блока в реестре |
| ContractId | Уникальный идентификатор контракта |
| ContractAddress |       Адрес контракта в реестре |
| TransactionHash  |     Хэш транзакции в реестре |
| OriginatingAddress |   Адрес инициатора транзакции |
| ActionName       |     Имя действия |
| IsUpdate        |      Определяет, является ли это обновлением |
| Параметры       |     Список объектов, которые определяют имя, значение и тип данных параметров, отправленных действию |
| TopLevelInputParams |  В сценариях, где контракт подключен к одному или нескольким контрактам, это параметры из контракта верхнего уровня. |

``` csharp
public class ContractInsertOrUpdateRequest : MessageModelBase
{
    public int ChainId { get; set; }
    public int BlockId { get; set; }
    public int ContractId { get; set; }
    public string ContractAddress { get; set; }
    public string TransactionHash { get; set; }
    public string OriginatingAddress { get; set; }
    public string ActionName { get; set; }
    public bool IsUpdate { get; set; }
    public List<ContractProperty> Parameters { get; set; }
    public bool IsTopLevelUpdate { get; set; }
    public List<ContractInputParameter> TopLevelInputParams { get; set; }
}
```

#### <a name="updatecontractaction"></a>UpdateContractAction

Указывает, что запрос был сделан для выполнения действия в определенном контракте распределенного реестра.

| ИМЯ                     | ОПИСАНИЕ                                                                                                                                                                   |
|--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ContractActionId         | Уникальный идентификатор этого действия контракта |
| ChainIdentifier          | Уникальный идентификатор цепочки |
| ConnectionId             | Уникальный идентификатор подключения |
| UserChainIdentifier      | Адрес пользователя, который был создан в сети блокчейна. В Ethereum это адрес пользователя **в цепочке**. |
| ContractLedgerIdentifier | Адрес контракта в реестре |
| WorkflowFunctionName     | Имя функции рабочего процесса |
| WorkflowName             | Имя рабочего процесса |
| WorkflowBlobStorageURL   | URL-адрес контракта в хранилище BLOB-объектов |
| ContractActionParameters | Параметры для действия контракта |
| TransactionHash          | Хэш транзакции в реестре |
| ProvisioningStatus      | Текущее состояние подготовки действия.</br>0 — Created (Создано).</br>1 — In Process (Выполняется).</br>2 — Complete (Завершено).</br> Состояние Complete указывает на подтверждение успешного добавления от реестра |

```csharp
public class ContractActionRequest : MessageModelBase
{
    public int ContractActionId { get; set; }
    public int ConnectionId { get; set; }
    public string UserChainIdentifier { get; set; }
    public string ContractLedgerIdentifier { get; set; }
    public string WorkflowFunctionName { get; set; }
    public string WorkflowName { get; set; }
    public string WorkflowBlobStorageURL { get; set; }
    public IEnumerable<ContractActionParameter> ContractActionParameters { get; set; }
    public string TransactionHash { get; set; }
    public int ProvisioningStatus { get; set; }
}
```

### <a name="updateuserbalance"></a>UpdateUserBalance

Указывает, что был выполнен запрос на обновление пользовательского баланса в определенном распределенном реестре.

> [!NOTE]
> Это сообщение создается только для реестров, в которых требуется финансирование учетных записей.
> 

| ИМЯ    | ОПИСАНИЕ                              |
|---------|------------------------------------------|
| Адрес | Адрес финансируемого пользователя |
| Balance | Пользовательский баланс         |
| ChainID | Уникальный идентификатор цепочки     |


``` csharp
public class UpdateUserBalanceRequest : MessageModelBase
{
    public string Address { get; set; }
    public decimal Balance { get; set; }
    public int ChainID { get; set; }
}
```

### <a name="insertblock"></a>InsertBlock

Сообщение указывает, что запрос был сделан для добавления блока в распределенном реестре.

| ИМЯ           | ОПИСАНИЕ                                                            |
|----------------|------------------------------------------------------------------------|
| ChainID        | Уникальный идентификатор цепочки, в которую был добавлен блок             |
| BlockId        | Уникальный идентификатор блока в Azure Blockchain Workbench |
| BlockHash      | Хэш блока                                                 |
| BlockTimestamp | Метка времени блока                                            |

``` csharp
public class InsertBlockRequest : MessageModelBase
{
    public int ChainId { get; set; }
    public int BlockId { get; set; }
    public string BlockHash { get; set; }
    public int BlockTimestamp { get; set; }
}
```

### <a name="inserttransaction"></a>InsertTransaction

Сообщение содержит сведения о запросе на добавление транзакции в распределенный реестр.

| ИМЯ            | ОПИСАНИЕ                                                            |
|-----------------|------------------------------------------------------------------------|
| ChainID         | Уникальный идентификатор цепочки, в которую был добавлен блок             |
| BlockId         | Уникальный идентификатор блока в Azure Blockchain Workbench |
| TransactionHash | Хэш транзакции                                           |
| Из            | Адрес инициатора транзакции                      |
| Кому              | Адрес целевого получателя транзакции              |
| Значение           | Значение, включенное в транзакцию                                 |
| IsAppBuilderTx  | Определяет, является ли это транзакцией Blockchain Workbench                         |

``` csharp
public class InsertTransactionRequest : MessageModelBase
{
    public int ChainId { get; set; }
    public int BlockId { get; set; }
    public string TransactionHash { get; set; }
    public string From { get; set; }
    public string To { get; set; }
    public decimal Value { get; set; }
    public bool IsAppBuilderTx { get; set; }
}
```

### <a name="assigncontractchainidentifier"></a>AssignContractChainIdentifier

Содержит сведения о назначении идентификатора цепочки для контракта. Например, в блокчейне Ethereum это адрес контракта в реестре.

| ИМЯ            | ОПИСАНИЕ                                                                       |
|-----------------|-----------------------------------------------------------------------------------|
| ContractId      | Уникальный идентификатор контракта в Azure Blockchain Workbench |
| ChainIdentifier | Идентификатор контракта в цепочке                             |

``` csharp
public class AssignContractChainIdentifierRequest : MessageModelBase
{
    public int ContractId { get; set; }
    public string ChainIdentifier { get; set; }
}
```

## <a name="classes-used-by-message-types"></a>Классы, используемые в типах сообщений

### <a name="messagemodelbase"></a>MessageModelBase

Базовая модель всех сообщений.

| ИМЯ          | ОПИСАНИЕ                          |
|---------------|--------------------------------------|
| OperationName | Имя операции           |
| RequestId     | Уникальный идентификатор запроса |

``` csharp
public class MessageModelBase
{
    public string OperationName { get; set; }
    public string RequestId { get; set; }
}
```

### <a name="contractinputparameter"></a>ContractInputParameter

Содержит имя, значение и тип параметра.

| ИМЯ  | ОПИСАНИЕ                 |
|-------|-----------------------------|
| ИМЯ  | Имя параметра  |
| Значение | Значение параметра |
| type  | Тип параметра  |

``` csharp
public class ContractInputParameter
{
    public string Name { get; set; }
    public string Value { get; set; }
    public string Type { get; set; }
}
```

#### <a name="contractproperty"></a>ContractProperty

Содержит идентификатор, имя, значение и тип свойства.

| ИМЯ  | ОПИСАНИЕ                |
|-------|----------------------------|
| Идентификатор    | Идентификатор свойства    |
| ИМЯ  | Имя свойства  |
| Значение | Значение свойства. |
| type  | Тип свойства  |

``` csharp
public class ContractProperty
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Value { get; set; }
    public string DataType { get; set; }
}
```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Шаблоны интеграции смарт-контрактов](integration-patterns.md)