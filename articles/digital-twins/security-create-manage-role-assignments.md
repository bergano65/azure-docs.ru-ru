---
title: Создание назначений ролей и управление ими в Azure Digital Twins | Документация Майкрософт
description: Создайте назначения ролей и управляйте ими в Azure Digital Twins.
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/26/2018
ms.author: lyrana
ms.custom: seodec18
ms.openlocfilehash: 72155799971760e9ddc93746dceafb1ea554d88b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66162112"
---
# <a name="create-and-manage-role-assignments-in-azure-digital-twins"></a>Создание назначений ролей и управление ими в Azure Digital Twins

Для управления доступом к ресурсам в Azure Digital Twins используется управление доступом на основе ролей ([RBAC](./security-role-based-access-control.md)).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="role-assignments-overview"></a>Обзор назначений ролей

Каждое назначение ролей соответствует следующему определению:

```JSON
{
  "roleId": "00e00ad7-00d4-4007-853b-b9968ad000d1",
  "objectId": "be2c6daa-a3a0-0c0a-b0da-c000000fbc5f",
  "objectIdType": "ServicePrincipalId",
  "path": "/",
  "tenantId": "00f000bf-86f1-00aa-91ab-2d7cd000db47"
}
```

В таблице ниже описывается каждый атрибут.

| Атрибут | ИМЯ | Обязательно для заполнения | type | Описание |
| --- | --- | --- | --- | --- |
| roleId | Идентификатор определения роли | Да | String | Уникальный идентификатор необходимого назначения ролей. Поиск определения ролей и их идентификаторов с помощью запроса API или проверки таблицы ниже. |
| objectId | Идентификатор объекта | Да | String | Идентификатор Azure Active Directory, идентификатор объекта субъекта-службы или доменное имя. Чему или кому назначается роль. Назначение ролей должно быть отформатировано в соответствии со связанным типом. Для objectIdType `DomainName` свойство objectId должно начинаться со знака `“@”`. |
| objectIdType | Тип идентификатора объекта | Да | String | Использованный вид идентификатора объекта. См. **Поддерживаемые objectIdTypes** ниже. |
| путь | Путь пространства | Да | String | Полный путь к объекту `Space`. Например, `/{Guid}/{Guid}`. Если идентификатору требуется назначение ролей для всего графа, укажите `"/"`. Этот символ обозначает корень, но его использование не рекомендуется. Всегда следуйте принципу минимальных привилегий. |
| tenantId | Идентификатор клиента | Varies | String | В большинстве случаев идентификатор клиента Azure Active Directory. Не разрешено для ObjectIdTypes свойства `DeviceId` и `TenantId`. Требуется для ObjectIdTypes свойства `UserId` и `ServicePrincipalId`. Необязательно для ObjectIdType свойства DomainName. |

### <a name="supported-role-definition-identifiers"></a>Поддерживаемые идентификаторы определения ролей

Каждое назначение ролей связывает определение ролей с сущностью в среде Azure Digital Twins.

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

### <a name="supported-object-identifier-types"></a>Поддерживаемые типы идентификатора объекта

Сначала был представлен атрибут **objectIdType**.

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

## <a name="role-assignment-operations"></a>Операции назначения ролей

Azure Digital Twins поддерживает полное функционирование операций *СОЗДАНИЕ*, *ЧТЕНИЕ* и *УДАЛЕНИЕ* для назначения ролей. Операции *ОБНОВЛЕНИЕ* обрабатываются путем добавления назначений ролей, удаление назначений ролей или изменение узлов [пространственного интеллектуального графа](./concepts-objectmodel-spatialgraph.md), к которым назначения ролей предоставляют доступ.

![Конечные точки назначения ролей][1]

В предоставленной справочной документации по Swagger содержатся дополнительные сведения о всех доступных API конечных точек, операций запроса и определения.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

<div id="grant"></div>

### <a name="grant-permissions-to-your-service-principal"></a>Предоставление разрешений для субъекта-службы

Предоставление разрешений для субъекта-службы часто является одним из первых шагов при работе с Azure Digital Twins. Это влечет за собой:

1. Выполнение входа в экземпляр Azure с помощью PowerShell.
1. Получение сведений о субъекте-службе.
1. Назначение необходимой роли для субъекта-службы.

Ваш идентификатор приложения передается через Azure Active Directory. Дополнительные сведения о настройке и подготовке Azure Digital Twins в Active Directory см. в статье [Краткое руководство. Поиск свободных помещений с помощью Azure Digital Twins](./quickstart-view-occupancy-dotnet.md).

Получив идентификатор приложения, выполните следующие команды PowerShell.

```shell
Login-AzAccount
Get-AzADServicePrincipal -ApplicationId  <ApplicationId>
```

Пользователь с ролью **Администратор** может затем назначить роль администратора пространства для пользователя, создав аутентифицированный запрос HTTP POST в URL-адресе.

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments
```

С помощью следующего текста JSON:

```JSON
{
  "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
  "objectId": "YOUR_SERVICE_PRINCIPLE_OBJECT_ID",
  "objectIdType": "ServicePrincipalId",
  "path": "YOUR_PATH",
  "tenantId": "YOUR_TENANT_ID"
}
```

<div id="all"></div>

### <a name="retrieve-all-roles"></a>Получение всех ролей

![Системные роли][2]

Чтобы получить список всех доступных ролей (определений ролей), создайте аутентифицированный запрос HTTP GET.

```plaintext
YOUR_MANAGEMENT_API_URL/system/roles
```

Успешный запрос возвращает массив JSON с записями для каждой роли, которую можно назначить.

```JSON
[
    {
        "id": "3cdfde07-bc16-40d9-bed3-66d49a8f52ae",
        "name": "DeviceAdministrator",
        "permissions": [
            {
                "notActions": [],
                "actions": [
                    "Read",
                    "Create",
                    "Update",
                    "Delete"
                ],
                "condition": "@Resource.Type Any_of {'Device', 'DeviceBlobMetadata', 'DeviceExtendedProperty', 'Sensor', 'SensorBlobMetadata', 'SensorExtendedProperty'} || ( @Resource.Type == 'ExtendedType' && (!Exists @Resource.Category || @Resource.Category Any_of { 'DeviceSubtype', 'DeviceType', 'DeviceBlobType', 'DeviceBlobSubtype', 'SensorBlobSubtype', 'SensorBlobType', 'SensorDataSubtype', 'SensorDataType', 'SensorDataUnitType', 'SensorPortType', 'SensorType' } ) )"
            },
            {
                "notActions": [],
                "actions": [
                    "Read"
                ],
                "condition": "@Resource.Type == 'Space' && @Resource.Category == 'WithoutSpecifiedRbacResourceTypes' || @Resource.Type Any_of {'ExtendedPropertyKey', 'SpaceExtendedProperty', 'SpaceBlobMetadata', 'SpaceResource', 'Matcher'}"
            }
        ],
        "accessControlPath": "/system",
        "friendlyPath": "/system",
        "accessControlType": "System"
    }
]
```

<div id="check"></div>

### <a name="check-a-specific-role-assignment"></a>Проверка конкретного назначения ролей

Чтобы проверить конкретное назначение ролей, создайте аутентифицированный запрос HTTP GET.

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments/check?userId=YOUR_USER_ID&path=YOUR_PATH&accessType=YOUR_ACCESS_TYPE&resourceType=YOUR_RESOURCE_TYPE
```

| **Значение параметра** | **Обязательный** |  **Тип** |  **Описание** |
| --- | --- | --- | --- |
| YOUR_USER_ID |  Истина | String |   Свойство objectId для objectIdType идентификатора пользователя. |
| YOUR_PATH | Истина | String |   Выбранный путь для проверки доступа. |
| YOUR_ACCESS_TYPE |  Истина | String |   Тип доступа для проверки. |
| YOUR_RESOURCE_TYPE | Истина | String |  Ресурс для проверки. |

Успешный запрос возвращает логическое значение `true` или `false` для указания того, назначен ли тип доступа пользователю для данного пути и ресурса.

### <a name="get-role-assignments-by-path"></a>Получение назначения ролей с помощью пути

Чтобы узнать все назначения ролей для пути, создайте аутентифицированный запрос HTTP GET.

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments?path=YOUR_PATH
```

| Значение | Заменить на |
| --- | --- |
| YOUR_PATH | Полный путь к пространству |

Успешный запрос возвращает массив JSON с каждым назначением ролей, связанным с выбранным параметром **path**.

```JSON
[
    {
        "id": "0000c484-698e-46fd-a3fd-c12aa11e53a1",
        "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
        "objectId": "0de38846-1aa5-000c-a46d-ea3d8ca8ee5e",
        "objectIdType": "UserId",
        "path": "/"
    }
]
```

### <a name="revoke-a-permission"></a>Отмена разрешения

Чтобы отменить разрешения от получателя, удалите назначение ролей путем создания аутентифицированного запроса HTTP DELETE.

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments/YOUR_ROLE_ASSIGNMENT_ID
```

| Параметр | Заменить на |
| --- | --- |
| *YOUR_ROLE_ASSIGNMENT_ID* | **Идентификатор** назначения ролей для удаления |

Успешный запрос DELETE вернет состояние ответа 204. Проверьте удаление назначения ролей, [проверив](#check), сохраняется ли оно.

### <a name="create-a-role-assignment"></a>Создание назначения роли

Чтобы создать назначение ролей, сделайте аутентифицированный запрос HTTP POST в URL-адресе.

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments
```

Проверьте, соответствует ли текст JSON следующей схеме.

```JSON
{
  "roleId": "YOUR_ROLE_ID",
  "objectId": "YOUR_OBJECT_ID",
  "objectIdType": "YOUR_OBJECT_ID_TYPE",
  "path": "YOUR_PATH",
  "tenantId": "YOUR_TENANT_ID"
}
```

Успешный запрос вернет состояние ответа 201 вместе с **идентификатором** созданного назначения ролей.

```JSON
"d92c7823-6e65-41d4-aaaa-f5b32e3f01b9"
```

## <a name="configuration-examples"></a>Примеры конфигурации

Следующие примеры демонстрируют, как настроить текст JSON в нескольких распространенных сценариях назначения ролей.

* **Пример**: Пользователю необходим административный доступ к этажу пространства клиента.

   ```JSON
   {
    "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
    "objectId" : " 0fc863aa-eb51-4704-a312-7d635d70e000",
    "objectIdType" : "UserId",
    "tenantId": " a0c20ae6-e830-4c60-993d-a00ce6032724",
    "path": "/ 000e349c-c0ea-43d4-93cf-6b00abd23a44/ d84e82e6-84d5-45a4-bd9d-006a000e3bab"
   }
   ```

* **Пример**: Приложение, которое запускает тестовые сценарии, имитирующие устройства и датчики.

   ```JSON
   {
    "roleId": "98e44ad7-28d4-0007-853b-b9968ad132d1",
    "objectId" : "cabf7aaa-af0b-41c5-000a-ce2f4c20000b",
    "objectIdType" : "ServicePrincipalId",
    "tenantId": " a0c20ae6-e000-4c60-993d-a91ce6000724",
    "path": "/"
   }
    ```

* **Пример**: Все пользователи, являющиеся частью домена, получают доступ на чтение для пространств, датчиков и пользователей. Этот доступ распространяется на соответствующие связанные объекты.

   ```JSON
   {
    "roleId": " b1ffdb77-c635-4e7e-ad25-948237d85b30",
    "objectId" : "@microsoft.com",
    "objectIdType" : "DomainName",
    "path": "/000e349c-c0ea-43d4-93cf-6b00abd23a00"
   }
   ```

## <a name="next-steps"></a>Дальнейшие действия

- Подробнее об управлении доступом на основе ролей Azure Digital Twins см. в статье [Подключение к API и аутентификация](./security-authenticating-apis.md).

- Дополнительные сведения о проверке подлинности API Azure Digital Twins см. в статье [Подключение к API и аутентификация](./security-authenticating-apis.md).

<!-- Images -->
[1]: media/security-roles/roleassignments.png
[2]: media/security-roles/system.png
