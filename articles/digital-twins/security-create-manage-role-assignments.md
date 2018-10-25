---
title: Общие сведения о подключении устройств Azure Digital Twins и их аутентификации | Документация Майкрософт
description: Сведения о подключении к устройствам и выполнения аутентификации с помощью Azure Digital Twins
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: lyrana
ms.openlocfilehash: adfb4c369ea1b324da8562a5b0b245ebdecff602
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323921"
---
# <a name="create-and-manage-role-assignments"></a>Создание назначений ролей и управление ими

Для управления доступом к ресурсам в Azure Digital Twins используется управление доступом на основе ролей ([RBAC](./security-role-based-access-control.md)).

Каждое назначение ролей включает в себя:

* **Идентификатор объекта** (идентификатор Azure Active Directory, идентификатор объекта субъекта-службы или доменное имя).
* **Тип идентификатора объекта**.
* **Идентификатор определения роли**.
* **Путь к пространству**.
* (В большинстве случаев) **идентификатор клиента** Azure Active Directory.

## <a name="role-definition-identifiers"></a>Идентификаторы определения ролей

В таблице ниже показано, что можно получить, запрашивая API системы или ролей:

| **Роль** | **Идентификатор** |
| --- | --- |
| Администратор пространства | 98e44ad7-28d4-4007-853b-b9968ad132d1 |
| администратора пользователей;| dfaac54c-f583-4dd2-b45d-8d4bbc0aa1ac |
| Администратор устройств | 3cdfde07-bc16-40d9-bed3-66d49a8f52ae |
| Администратор ключей | 5a0b1afc-e118-4068-969f-b50efb8e5da6 |
| Администратор токенов | 38a3bb21-5424-43b4-b0bf-78ee228840c3 |
| Пользователь | b1ffdb77-c635-4e7e-ad25-948237d85b30 |
| Специалист технической поддержки | 6e46958b-dc62-4e7c-990c-c3da2e030969 |
| Установщик устройства | b16dd9fe-4efe-467b-8c8c-720e2ff8817c |
| Устройство шлюза | d4c69766-e9bd-4e61-bfc1-d8b6e686c7a8 |

## <a name="supported-objectidtypes"></a>Поддерживаемые ObjectIdTypes

Поддерживаются такие `ObjectIdTypes`:

* `UserId`
* `DeviceId`
* `DomainName`
* `TenantId`
* `ServicePrincipalId`
* `UserDefinedFunctionId`

## <a name="create-a-role-assignment"></a>Создание назначения роли

```plaintext
HTTP POST /api/v1.0/roleassignments
```

| **Имя** | **Обязательный** | **Тип** | **Описание** |
| --- | --- | --- | --- |
| roleId| Yes |строка | Идентификатор определения роли. Определения ролей и их идентификаторы можно найти, запрашивая системный API. |
| objectId | Yes |строка | Идентификатор объекта для назначения ролей, который должен быть отформатирован в соответствии со связанным типом. Для ObjectIdType свойства `DomainName` свойство ObjectId должно начинаться со знака `“@”`. |
| objectIdType | Yes |строка | Тип назначение роли. Должен быть одной из следующих строк в этой таблице. |
| tenantId | Varies | строка |Идентификатор клиента. Не разрешено для ObjectIdTypes свойства `DeviceId` и `TenantId`. Требуется для ObjectIdTypes свойства `UserId` и `ServicePrincipalId`. Необязательно для ObjectIdType свойства DomainName. |
| path* | Yes | строка |Полный путь к объекту `Space`. Пример. `/{Guid}/{Guid}` Если идентификатору требуется назначение ролей для всего графа, укажите `"/"` (обозначает корень). Однако не рекомендуется так делать и **вы всегда должны следовать принципу наименьших привилегий**. |

## <a name="sample-configuration"></a>Пример конфигурации

Пользователю необходим административный доступ к этажу пространства клиента:

  ```JSON
    {
      "RoleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
      "ObjectId" : " 0fc863bb-eb51-4704-a312-7d635d70e599",
      "ObjectIdType" : "UserId",
      "TenantId": " a0c20ae6-e830-4c60-993d-a91ce6032724",
      "Path": "/ 091e349c-c0ea-43d4-93cf-6b57abd23a44/ d84e82e6-84d5-45a4-bd9d-006a118e3bab"
    }
  ```

Приложение, которое запускает тестовые сценарии, имитирующие устройства и датчики:

  ```JSON
    {
      "RoleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
      "ObjectId" : "cabf7acd-af0b-41c5-959a-ce2f4c26565b",
      "ObjectIdType" : "ServicePrincipalId",
      "TenantId": " a0c20ae6-e830-4c60-993d-a91ce6032724",
      "Path": "/"
    }
  ```

Все пользователи, являющиеся частью домена, получат доступ на чтение для пространств, датчиков и пользователей, включая соответствующие связанные объекты:

  ```JSON
    {
      "RoleId": " b1ffdb77-c635-4e7e-ad25-948237d85b30",
      "ObjectId" : "@microsoft.com",
      "ObjectIdType" : "DomainName",
      "Path": "/091e349c-c0ea-43d4-93cf-6b57abd23a44"
    }
  ```

Чтобы получить назначение роли:

```plaintext
HTTP GET /api/v1/roleassignments?path={path}
```

| **Имя** | **Где** | **Обязательный** |    **Тип** |  **Описание** |
| --- | --- | --- | --- | --- |
| Путь | Путь | Истина | Строка | Полный путь к пространству |

Чтобы удалить назначение роли:

```plaintext
HTTP DELETE /api/v1/roleassignments/{id}
```

| **Имя** | **Где** | **Обязательный** | **Тип** | **Описание** |
| --- | --- | --- | --- | --- |
| ИД | Путь | Истина | Строка |   Идентификатор назначения ролей |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о безопасности Azure Digital Twins см. в статье [Подключение к API и аутентификация](./security-authenticating-apis.md).
