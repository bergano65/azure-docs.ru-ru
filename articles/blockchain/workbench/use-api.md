---
title: Использование Azure Blockchain Workbench REST APIS
description: Сценарии использования Azure Blockchain Workbench Предварительный REST API
ms.date: 03/05/2020
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 3084fcf343bc42fe01bf352b6791916d62f63540
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672752"
---
# <a name="using-the-azure-blockchain-workbench-preview-rest-api"></a>Использование Azure Blockchain Workbench Предварительный REST API

Azure Blockchain Workbench Preview REST API предоставляет разработчикам и информационным работникам способ построения богатой интеграции в блокчейн-приложения. В этой статье освещаются несколько сценариев использования API Workbench REST. Например, предположим, что вы хотите создать пользовательский блокчейн-клиент, который позволяет подписным пользователям просматривать и взаимодействовать с назначенными ими блокчейн-приложениями. Клиент может использовать API Blockchain Workbench для просмотра примеров контрактов и действий по смарт-контрактам.

## <a name="blockchain-workbench-api-endpoint"></a>Блокчейн Workbench API конечная точка

Блокчейн Workbench AIS доступны через конечную точку для развертывания. Чтобы получить URL-адрес aPI для развертывания:

1. Войдите на [портал Azure](https://portal.azure.com).
1. В левом навигационном стеле выберите **группы ресурсов.**
1. Выберите группу ресурсов, назовунию развернутую Blockchain Workbench.
1. Щелкните заголовок столбца **Тип**, чтобы отсортировать список в алфавитном порядке.
1. Существует два ресурса с типом **Служба приложений**. Выберите ресурс типа **App Service** *с* суффиксом «-api».
1. В **обзоре**службы приложений скопируйте значение **URL,которое** представляет URL-адрес API для развернутой блокчейн Workbench.

    ![API-конечная точка службы App](media/use-api/app-service-api.png)

## <a name="authentication"></a>Проверка подлинности

Запросы на API Blockchain Workbench REST защищены с помощью Active Directory Azure (Azure AD).

Чтобы сделать аутентифицированный запрос на АДИ-аДИ REST, клиентский код требует аутентификации с действительными учетными данными, прежде чем вы сможете позвонить в API. Аутентификация координируется между различными субъектами Azure AD и предоставляет клиенту [токен доступа](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#access-token) в качестве доказательства проверки подлинности. Затем токен отправляется в заголовке авторизации HTTP запросов REST API. Чтобы узнать больше об аутентификации Azure AD, смотрите [active Directory Для разработчиков.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)

Ознакомиться с примерами проверки подлинности можно найти [образцы REST API.](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/rest-api-samples)

## <a name="using-postman"></a>Использование Postman

Если вы хотите протестировать или поэкспериментировать с API Workbench, вы можете использовать [Postman](https://www.postman.com) для вызова API для развертывания. [Загрузите образец коллекции Postman запросов Workbench API](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/rest-api-samples/postman) от GitHub. Подробнее о проверке подлинности и использовании примерных запросов API читайте в файле README.

## <a name="create-an-application"></a>Создание приложения

Для создания приложения Blockchain Workbench используются два вызова API. Этот метод может быть выполнен только пользователями, которые являются администраторами Workbench.

Используйте [Приложение POST API,](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/applicationspost) чтобы загрузить файл JSON приложения и получить идентификатор приложения.

### <a name="applications-post-request"></a>Заявки POST запрос

Используйте параметр **appFile** для отправки файла конфигурации как часть тела запроса.

``` http
POST /api/v1/applications
Content-Type: multipart/form-data;
Authorization : Bearer {access token}
Content-Disposition: form-data; name="appFile"; filename="/C:/smart-contract-samples/HelloWorld.json"
Content-Type: application/json
```

### <a name="applications-post-response"></a>Ответы на приложения POST

Созданный идентификатор приложения возвращается в ответ. Идентификатор приложения должен связать файл конфигурации с файлом кода при вызове следующего API.

``` http
HTTP/1.1 200 OK
Content-Type: "application/json"
1
```

### <a name="contract-code-post-request"></a>Запрос почтового кода контракта

Используйте [контрактный код Приложения POST API,](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/contractcodepost) передав идентификатор приложения, чтобы загрузить файл кода Solidity приложения. Полезная нагрузка может быть одним файлом Solidity или файлом на молнии, содержащим файлы Solidity.

Измените следующие значения:

| Параметр | Значение |
|-----------|-------|
| (applicationId) | Возврат значения из приложений POST API. |
| (леджерид) | Индекс книги. Значение обычно 1. Вы также можете проверить [таблицу книги](data-sql-management-studio.md) для значения. |

``` http
POST /api/v1/applications/{applicationId}/contractCode?ledgerId={ledgerId}
Content-Type: multipart/form-data;
Authorization : Bearer {access token}
Content-Disposition: form-data; name="contractFile"; filename="/C:/smart-contract-samples/HelloWorld.sol"
```

### <a name="contract-code-post-response"></a>Ответ POST код контракта

В случае успеха ответ включает в себя созданный идентификатор кода контракта из [таблицы ContractCode.](data-sql-management-studio.md)

``` http
HTTP/1.1 200 OK
Content-Type: "application/json"
2
```

## <a name="assign-roles-to-users"></a>Назначение ролей пользователям

Используйте [ролевые назначения Приложений POST API,](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/roleassignmentspost) передавая идентификатор приложения, идентификатор пользователя и идентификатор роли приложения, чтобы создать отображение от пользователя к роли в указанном приложении блокчейн. Этот метод может быть выполнен только пользователями, которые являются администраторами Workbench.

### <a name="role-assignments-post-request"></a>Назначение ролей POST-запрос

Измените следующие значения:

| Параметр | Значение |
|-----------|-------|
| (applicationId) | Возврат значения из приложения POST API. |
| «userId» | Значение идентификатора пользователя из [таблицы пользователя.](data-sql-management-studio.md) |
| (применениеRoleId) | Значение идентификатора роли приложения, связанное с идентификатором приложения из [таблицы ApplicationRole.](data-sql-management-studio.md) |

``` http
POST /api/v1/applications/{applicationId}/roleAssignments
Content-Type: application/json;
Authorization : Bearer {access token}

{
  "userId": {userId},
  "applicationRoleId": {applicationRoleId}
}
```

### <a name="role-assignments-post-response"></a>Роли заданий POST ответ

В случае успеха ответ включает в себя созданный идентификатор назначения ролей из [таблицы Ролевназначения.](data-sql-management-studio.md)

``` http
HTTP/1.1 200
1
```

## <a name="list-applications"></a>Список приложений

Используйте [API приложений GET](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/applicationsget) для получения всех приложений Blockchain Workbench для пользователя. В этом примере пользователь, вписав в него, имеет доступ к двум приложениям:

- [Передача активов](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer/readme.md)
- [Транспортировка с охлаждением](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/refrigerated-transportation/readme.md)

### <a name="applications-get-request"></a>Заявки GET запрос

``` http
GET /api/v1/applications
Authorization : Bearer {access token}
```

### <a name="applications-get-response"></a>Приложения GET ответ

Ответ содержит список всех приложений на базе блокчейна, к которым пользователь имеет доступ в Blockchain Workbench. Администраторы Blockchain Workbench получают все блокчейн-приложения. Администраторы, не связанные с Работой, получают все блокчейн-приложения, для которых они имеют по крайней мере одну связанную роль приложения или связанную роль экземпляра смарт-контрактов.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications?skip=2",
    "applications": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Allows transfer of assets between a buyer and a seller, with appraisal/inspection functionality",
            "displayName": "Asset Transfer",
            "createdByUserId": 1,
            "createdDtTm": "2018-04-28T05:59:14.4733333",
            "enabled": true,
            "applicationRoles": null
        },
        {
            "id": 2,
            "name": "RefrigeratedTransportation",
            "description": "Application to track end-to-end transportation of perishable goods.",
            "displayName": "Refrigerated Transportation",
            "createdByUserId": 7,
            "createdDtTm": "2018-04-28T18:25:38.71",
            "enabled": true,
            "applicationRoles": null
        }
    ]
}
```

## <a name="list-workflows-for-an-application"></a>Получение списка рабочих процессов для приложения

Используйте [Приложения Workflows GET API,](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/workflowsget) чтобы перечислить все рабочие процессы указанного приложения blockchain, к которому пользователь имеет доступ в Blockchain Workbench. Для каждого приложения на базе блокчейна доступен один или несколько рабочих процессов, и для каждого рабочего процесса могут быть доступны экземпляры смарт-контракта. Для клиентского приложения на базе блокчейна, у которого есть только один рабочий процесс, мы рекомендуем пропустить поток взаимодействия с пользователем, позволяющий пользователям выбрать соответствующий рабочий процесс.

### <a name="application-workflows-request"></a>Запрос рабочих процессов приложений

``` http
GET /api/v1/applications/{applicationId}/workflows
Authorization: Bearer {access token}
```

### <a name="application-workflows-response"></a>Ответ на рабочие процессы приложений

Администраторы Blockchain Workbench получают все рабочие процессы блокчейна. Остальные администраторы (не Workbench) получают все рабочие процессы, для которых у них есть по крайней мере одна роль связанного приложения или экземпляра смарт-контракта.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications/1/workflows?skip=1",
    "workflows": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Handles the business logic for the asset transfer scenario",
            "displayName": "Asset Transfer",
            "applicationId": 1,
            "constructorId": 1,
            "startStateId": 1
        }
    ]
}
```

## <a name="create-a-contract-instance"></a>Создание примера контракта

Используйте [Контракты V2 POST API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/contractsv2/contractpost) для создания нового экземпляра смарт-контрактов для рабочего процесса. Пользователи могут создать новый экземпляр смарт-контракта только в том случае, если пользователь связан с ролью приложения, которая может инициировать экземпляр смарт-контракта для рабочего процесса.

> [!NOTE]
> В этом примере используется версия 2 API. AA-аДИ контракта версии 2 обеспечивают большую детализацию для связанных полей ProvisioningStatus.

### <a name="contracts-post-request"></a>Запрос контрактов POST

Измените следующие значения:

| Параметр | Значение |
|-----------|-------|
| (рабочий процессId) | Значение идентификатора рабочего процесса является конструктором контракта из [таблицы Рабочего процесса.](data-sql-management-studio.md) |
| (контрактCodeId) | Идентификатор кода контракта из [таблицы ContractCode.](data-sql-management-studio.md) Соотвествуйте идентификатор приложения и идентификатор книги для экземпляра контракта, который вы хотите создать. |
| (соединениеId) | Значение идентификатора соединения из [таблицы подключения.](data-sql-management-studio.md) |

Для тела запроса установите значения, используя следующую информацию:

| Параметр | Значение |
|-----------|-------|
| рабочий процессФункционID | Идентификатор из [таблицы WorkflowFunction](data-sql-management-studio.md). |
| рабочий процессДействия Параметры | Именные значения пар параметров передаются конструктору. Для каждого параметра используйте значение рабочего процессаФункциональный параметр id из таблицы [WorkflowFunctionParameter.](data-sql-management-studio.md) |

``` http
POST /api/v2/contracts?workflowId={workflowId}&contractCodeId={contractCodeId}&connectionId={connectionId}
Content-Type: application/json;
Authorization : Bearer {access token}

{
  "workflowFunctionID": 2,
  "workflowActionParameters": [
    {
      "name": "message",
      "value": "Hello, world!",
      "workflowFunctionParameterId": 3
    }
  ]
}
```

### <a name="contracts-post-response"></a>Контракты POST ответ

В случае успеха API назначений ролей возвращает ContractActionID из [таблицы ContractActionParameter.](data-sql-management-studio.md)

``` http
HTTP/1.1 200 OK
4
```

## <a name="list-smart-contract-instances-for-a-workflow"></a>Вывести список экземпляров смарт-контракта для рабочего процесса.

Используйте [контракты GET API,](/rest/api/azure-blockchain-workbench/contractsv2/contractsget) чтобы показать все экземпляры смарт-контрактов для рабочего процесса. Кроме того, можно разрешить пользователям изучить любой из отображаемых экземпляров смарт-контрактов.

### <a name="contracts-request"></a>Запрос контрактов

В этом примере пользователь применит один из смарт-контрактов для выполнения действий.

``` http
GET api/v1/contracts?workflowId={workflowId}
Authorization: Bearer {access token}
```

### <a name="contracts-response"></a>Ответы на контракты

В ответе перечислены все экземпляры смарт-контрактов указанного рабочего процесса. Администраторы Workbench получают все экземпляры смарт-контрактов. Остальные администраторы (не Workbench) получают все экземпляры смарт-контрактов, для которых у них есть по крайней мере одна роль связанного приложения или экземпляра смарт-контракта.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts?skip=3&workflowId=1",
    "contracts": [
        {
            "id": 1,
            "provisioningStatus": 2,
            "connectionID": 1,
            "ledgerIdentifier": "0xbcb6127be062acd37818af290c0e43479a153a1c",
            "deployedByUserId": 1,
            "workflowId": 1,
            "contractCodeId": 1,
            "contractProperties": [
                {
                    "workflowPropertyId": 1,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 2,
                    "value": "My first car"
                },
                {
                    "workflowPropertyId": 3,
                    "value": "54321"
                },
                {
                    "workflowPropertyId": 4,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 5,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 6,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 7,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 8,
                    "value": "0xd882530eb3d6395e697508287900c7679dbe02d7"
                }
            ],
            "transactions": [
                {
                    "id": 1,
                    "connectionId": 1,
                    "transactionHash": "0xf3abb829884dc396e03ae9e115a770b230fcf41bb03d39457201449e077080f4",
                    "blockID": 241,
                    "from": "0xd882530eb3d6395e697508287900c7679dbe02d7",
                    "to": null,
                    "value": 0,
                    "isAppBuilderTx": true
                }
            ],
            "contractActions": [
                {
                    "id": 1,
                    "userId": 1,
                    "provisioningStatus": 2,
                    "timestamp": "2018-04-29T23:41:14.9333333",
                    "parameters": [
                        {
                            "name": "Description",
                            "value": "My first car"
                        },
                        {
                            "name": "Price",
                            "value": "54321"
                        }
                    ],
                    "workflowFunctionId": 1,
                    "transactionId": 1,
                    "workflowStateId": 1
                }
            ]
        }
    ]
}
```

## <a name="list-available-actions-for-a-contract"></a>Получение списка доступных действий для контракта

Используйте [Contract Action GET API,](/rest/api/azure-blockchain-workbench/contractsv2/contractactionget) чтобы показать доступные действия пользователя с учетом состояния контракта. 

### <a name="contract-action-request"></a>Запрос действий по контракту

В этом примере пользователь просматривает все доступные действия для нового смарт-контракта, который они создали.

``` http
GET /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
```

### <a name="contract-action-response"></a>Ответ на действия по контракту

Ответ содержит список всех действий, к которым пользователь может применить текущее состояние указанного экземпляра смарт-контракта.

* Изменить: это действие позволяет пользователю изменить описание и цену ресурса.
* Завершить: это действие дает пользователю возможность завершить контракт ресурса.

Пользователи получают все необходимые действия, если они имеют роль связанного приложения или экземпляра смарт-контракта для текущего состояния указанного экземпляра смарт-контракта.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts/1/actions?skip=2",
    "workflowFunctions": [
        {
            "id": 2,
            "name": "Modify",
            "description": "Modify the description/price attributes of this asset transfer instance",
            "displayName": "Modify",
            "parameters": [
                {
                    "id": 1,
                    "name": "description",
                    "description": "The new description of the asset",
                    "displayName": "Description",
                    "type": {
                        "id": 2,
                        "name": "string",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                },
                {
                    "id": 2,
                    "name": "price",
                    "description": "The new price of the asset",
                    "displayName": "Price",
                    "type": {
                        "id": 3,
                        "name": "money",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                }
            ],
            "workflowId": 1
        },
        {
            "id": 3,
            "name": "Terminate",
            "description": "Used to cancel this particular instance of asset transfer",
            "displayName": "Terminate",
            "parameters": [],
            "workflowId": 1
        }
    ]
}
```

## <a name="execute-an-action-for-a-contract"></a>Выполнение действия для контракта

Используйте [Contract Action POST API](/rest/api/azure-blockchain-workbench/contractsv2/contractactionpost) для принятия мер для указанного экземпляра смарт-контракта.

### <a name="contract-action-post-request"></a>Запрос POST действия контракта

В этом случае рассмотрим сценарий, при котором пользователь хотел бы изменить описание и цену актива.

``` http
POST /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
actionInformation: {
    "workflowFunctionId": 2,
    "workflowActionParameters": [
        {
            "name": "description",
            "value": "My updated car"
        },
        {
            "name": "price",
            "value": "54321"
        }
    ]
}
```

Пользователи могут выполнить действие только с учетом текущего состояния указанного экземпляра смарт-контракта и роли связанного приложения или экземпляра смарт-контракта пользователя.

### <a name="contract-action-post-response"></a>Ответ POST по контракту

При успешном выполнении вызова POST возвращается ответ HTTP 200 OK без текста ответа.

``` http
HTTP/1.1 200 OK
Content-type: application/json
```

## <a name="next-steps"></a>Дальнейшие действия

Для получения справочной информации по [Azure Blockchain Workbench REST API reference](https://docs.microsoft.com/rest/api/azure-blockchain-workbench)API Blockchain Workbench см.
