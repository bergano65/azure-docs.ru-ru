---
title: Автоматизация подготовки приложений с помощью SCIM в Azure Active Directory | Документация Майкрософт
description: Azure Active Directory может выполнять автоматическую подготовку пользователей и групп для любого приложения или хранилища удостоверений, представляющим собой веб-службу с интерфейсом, определенным в спецификации протокола SCIM.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 5/06/2019
ms.author: mimart
ms.reviewer: asmalser
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 143919cb804be771d547e2913818d486c7f8adda
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/17/2019
ms.locfileid: "65824480"
---
# <a name="using-system-for-cross-domain-identity-management-scim-to-automatically-provision-users-and-groups-from-azure-active-directory-to-applications"></a>Использование системы для управления удостоверениями между доменами (SCIM) для автоматической подготовки пользователей и групп из Azure Active Directory в приложения

## <a name="overview"></a>Обзор

SCIM является стандартизированный протокол и схемы, имеет своей целью повышение уровня согласованности диска в Управление удостоверениями во всех системах. Если приложение поддерживает конечную точку SCIM для управления пользователями, служба подготовки пользователей Azure AD будет отправлять запросы на создание, изменение или удаление назначенных пользователей и групп в эту конечную точку. 

Многие приложения, для который Azure AD поддерживает [предварительно интегрированные автоматической подготовки пользователей](../saas-apps/tutorial-list.md) реализации SCIM, которое означает, что для получения пользователя уведомления об изменении.  Помимо этого, клиенты могут подключить приложения, поддерживающие конфигурацию из [спецификации протокола SCIM 2.0](https://tools.ietf.org/html/rfc7644) параметр универсального интеграции «не из коллекции» на портале Azure. 

В профиле SCIM 2.0, который реализует Azure AD как часть его универсальный соединитель SCIM для приложений не из коллекции является основной темой этой статьи. Однако успешное тестирование приложения, поддерживающего SCIM с Azure AD универсального соединителя — шаг для приложения из коллекции Azure AD как поддерживающий подготовки пользователей. Дополнительные сведения о том, как приложения, указанного в коллекции приложений Azure AD см. в разделе [как: Добавьте свое приложение в коллекции приложений Azure AD](../develop/howto-app-gallery-listing.md).
 

>[!IMPORTANT]
>Поведение реализации SCIM Azure AD в последний раз обновлялось 18 декабря 2018 г. Дополнительные сведения об изменениях см. в разделе, [посвященном соответствию требованиям протокола SCIM 2.0 службы подготовки пользователей Azure AD](application-provisioning-config-problem-scim-compatibility.md).

![][0]
*Рисунок 1. Подготовка из Azure Active Directory для приложения или хранилища удостоверений, реализующий SCIM*

В этой статье разделено на четыре раздела:

* **[Подготовка пользователей и групп для сторонних приложений, которые поддерживают SCIM 2.0](#provisioning-users-and-groups-to-applications-that-support-scim)**  — Если ваша организация использует сторонних приложений, реализует поддерживает профиль SCIM 2.0 Azure AD, можно приступить к автоматизации оба При подготовке и отзыве пользователей и групп уже сегодня.

* **[Основные сведения о реализации Azure AD SCIM](#understanding-the-azure-ad-scim-implementation)**  -Если вы создаете приложение, поддерживающее API управления пользователями SCIM 2.0, в этом разделе подробно описывается способ реализации клиента Azure AD SCIM, и как следует моделировать протокол SCIM запрос, обработка и ответы.
  
* **[Создание конечной точки SCIM, с помощью библиотеки Microsoft CLI](#building-a-scim-endpoint-using-microsoft-cli-libraries)**  -библиотеки Common Language Infrastructure (CLI), а также примеры кода показано, как разрабатывать конечную точку SCIM и преобразование сообщений SCIM.  

* **[Справочник по схеме пользователей и групп](#user-and-group-schema-reference)**  -описывается схема пользователей и групп, поддерживаемые реализацией Azure AD SCIM для приложения не из коллекции. 

## <a name="provisioning-users-and-groups-to-applications-that-support-scim"></a>Подготовка пользователей и групп для приложений, поддерживающих SCIM
Azure AD можно настроить автоматическую подготовку назначенных пользователей и групп для приложений, которые реализуют определенные профиль [протокола SCIM 2.0](https://tools.ietf.org/html/rfc7644). Особенности профиля описаны в [основные сведения о реализации Azure AD SCIM](#understanding-the-azure-ad-scim-implementation).

Рекомендуется связаться с поставщиком приложения или ознакомиться с документацией поставщика для получения подтверждения соответствия этим требованиям.

>[!IMPORTANT]
>Реализация Azure AD SCIM построена на основе службы, который предназначен для постоянно синхронизации пользователей между Azure AD подготовки пользователя Azure AD и целевом приложении и реализует конкретный набор стандартных операций. Важно понимать эти шаблоны, чтобы понять поведение клиента Azure AD SCIM. Дополнительные сведения см. в разделе [что происходит во время подготовки пользователей?](user-provisioning.md#what-happens-during-provisioning).

### <a name="getting-started"></a>Приступая к работе
Описанные ранее в этой статье приложения, поддерживающие профиль SCIM, можно подключать к Azure Active Directory с помощью функции "Приложение не из коллекции", доступной в коллекции приложений Azure AD. После подключения Azure AD запускает процесс синхронизации каждые 40 минут, запрашивая конечную точку SCIM приложения для получения назначенных пользователей и групп, и создает или изменяет их согласно сведениям о назначении.

**Подключение приложения, поддерживающего SCIM:**

1. Войдите в [портал Azure Active Directory](https://aad.portal.azure.com). 

1. Выберите **корпоративные приложения** в левой области. Отображается список всех настроенных приложений, включая приложения, которые были добавлены из коллекции.

1. Выберите **+ новое приложение** > **все** > **приложение не из коллекции**.

1. Введите имя для вашего приложения и выберите **добавить** для создания объекта приложения. Новое приложение добавляется в список корпоративных приложений и открывает его экран управления приложения.
    
   ![][1]
   *Рисунок 2. Использование коллекции приложений Azure AD*
    
1. На экране управления приложения, выберите **Подготовка** на левой панели.
1. В меню **Режим подготовки к работе** выберите **Автоматический**.
    
   ![][2]
   *Рисунок 3. Настройка автоматической подготовки пользователей на портале Azure*
    
1. В поле **URL-адрес клиента** введите URL-адрес конечной точки SCIM приложения. Пример: https://api.contoso.com/scim/v2/
1. Если конечной точке SCIM требуется токен носителя OAuth от издателя, отличного от Azure AD, скопируйте необходимый токен носителя OAuth в необязательное для заполнения поле **Секретный токен**. Если это поле оставить пустым, Azure AD включает токен носителя OAuth, выданный Azure AD с каждым запросом. Приложения, использующие Azure AD в качестве поставщика удостоверений, могут проверить этот выданный Azure AD токен.
1. Выберите **проверить подключение** Azure Active Directory попробует подключиться к конечной точке SCIM. Если попытка завершается неудачей, сведения об ошибке отображается.  

    >[!NOTE]
    >**Проверка подключения** запрашивает конечную точку SCIM для пользователя, который не существует, используя случайный идентификатор GUID в качестве свойства сопоставления, выбранного в конфигурации Azure AD. Ожидаемый правильный ответ — HTTP 200 OK с пустым сообщением ListResponse SCIM. 

1. Если попытка соединения с приложением будет успешной, затем выберите **Сохранить** сохранить учетные данные администратора.
1. В разделе **Сопоставления** доступны на выбор два набора сопоставлений атрибутов: для объектов-пользователей и для объектов групп. Выберите каждый из них, чтобы просмотреть атрибуты, которые синхронизируются из Azure Active Directory с вашим приложением. Атрибуты, выбранные как свойства **Matching**, используются для сопоставления пользователей и групп в вашем приложении для операций обновления. Чтобы зафиксировать изменения, щелкните **Сохранить**.

    >[!NOTE]
    >При необходимости можно отключить синхронизацию объектов групп, отключив сопоставление групп. 

1. Поле **Область** в разделе **Параметры** определяет, какие пользователи и группы синхронизируются. Выберите **синхронизация только назначенных пользователей и групп** (рекомендуется), чтобы синхронизировать только пользователей и групп, назначенных в **пользователей и групп** вкладки.
1. По завершении конфигурации задайте **состояние подготовки** для **на**.
1. Выберите **Сохранить** запустить службу подготовки Azure AD. 
1. Если синхронизация только назначенных пользователей и групп (рекомендуется), не забудьте выбрать **пользователей и групп** вкладку и назначение пользователей или групп, которые требуется синхронизировать.

После запуска начальной синхронизации, можно выбрать **журналы аудита** в левой панели, чтобы отслеживать ход выполнения, показывающий все действия, выполняемой службой подготовки в приложении. Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](check-status-user-account-provisioning.md).

> [!NOTE]
> Начальная синхронизация занимает больше времени, чем более поздней версии синхронизации, они выполняются примерно каждые 40 минут до тех пор, пока служба запущена. 

## <a name="understanding-the-azure-ad-scim-implementation"></a>Основные сведения о реализации Azure AD SCIM

Если вы создаете приложение, поддерживающее API управления пользователями SCIM 2.0, в этом разделе подробно описывается способ реализации клиента Azure AD SCIM, и как следует моделировать протокола SCIM запроса обработки и ответов. После реализации конечной точки SCIM, можно проверить его, выполнив процедуру, описанную в предыдущем разделе.

В рамках [спецификации протокола SCIM 2.0](http://www.simplecloud.info/#Specification), приложения должны удовлетворять следующим требованиям:

* Поддерживает создание пользователей и при необходимости также группы, как описано в разделе [3.3 протокола SCIM](https://tools.ietf.org/html/rfc7644#section-3.3).  
* Поддержка изменения пользователей или групп с запросы PATCH, как указано в [разделе 3.5.2 протокола SCIM](https://tools.ietf.org/html/rfc7644#section-3.5.2).  
* Поддержка получения известного ресурса для пользователя или группу, созданную ранее, как указано в [разделе 3.4.1 протокола SCIM](https://tools.ietf.org/html/rfc7644#section-3.4.1).  
* Позволяет выполнять запросы к пользователям или группам, как описано в разделе [3.4.2 протокола SCIM](https://tools.ietf.org/html/rfc7644#section-3.4.2).  По умолчанию пользователи извлекаются путем их `id` и запрашивать их `username` и `externalid`, и групп запрашиваться `displayName`.  
* Поддержка запросов пользователей по Идентификатору и диспетчеру, как описано в разделе 3.4.2 протокола SCIM.  
* Поддержка запросов групп по Идентификатору и члену, как описано в разделе 3.4.2 протокола SCIM.  
* Принимает маркер одного носителя для проверки подлинности и авторизации Azure AD в приложение.

При реализации конечную точку SCIM для обеспечения совместимости с Azure AD, следуйте следующим рекомендациям:

* `id` Это свойство для всех ресурсов. Каждый ответ, который возвращает ресурс следует убедиться, каждый ресурс имеет этого свойства, за исключением `ListResponse` с нулевые члены.
* Ответ на запрос и фильтр запроса всегда должно быть `ListResponse`.
* Группы — это необязательно, но только поддерживается, если реализация SCIM поддерживает запросы PATCH.
* Нет необходимости включать весь ресурс в ответе PATCH.
* Microsoft Azure AD использует только следующие операторы:  
     - `eq`
     - `and`
* Не требуют с учетом регистра структурные элементы в SCIM, определенного исправление `op` значения операций, как определено в https://tools.ietf.org/html/rfc7644#section-3.5.2. Azure AD выдает значения «op» как `Add`, `Replace`, и `Remove`.
* Microsoft Azure AD выполняет запрос для получения случайных пользователей и групп, чтобы убедиться, что конечной точке и учетные данные допустимы. Он также выполняется как часть **проверить подключение** потока в [портала Azure](https://portal.azure.com). 
* Атрибут, который можно запросить ресурсы в должна быть задана как соответствующий атрибут в приложении в [портала Azure](https://portal.azure.com). Дополнительные сведения см. в разделе [Настройка сопоставления атрибутов подготовки пользователей](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings)

### <a name="user-provisioning-and-de-provisioning"></a>Подготовка и отзыв пользователей
На следующем рисунке показано сообщения, которые Azure Active Directory отправляет службе SCIM для управления жизненным циклом пользователя в хранилище удостоверений приложения.  

![][4]
*Рисунок 4. Последовательность действий при подготовке и отзыве пользователя*

### <a name="group-provisioning-and-de-provisioning"></a>Подготовка и отзыв групп
Группы при подготовке и отзыве являются необязательными. При реализации и включена, на следующем рисунке показаны сообщения, Azure AD отправляет службе SCIM для управления жизненным циклом группы в хранилище удостоверений приложения.  Эти сообщения отличаются от сообщений о пользователях, двумя способами: 

* Запросы на получение группы указать атрибут члены должны быть исключены из любого ресурса, в ответ на запрос.  
* Запросы, которые должны определить, имеет ли ссылочный атрибут указанное значение, применяются в отношении атрибута members.  

![][5]
*Рисунок 5. Последовательность действий при подготовке и отзыве группы*

### <a name="scim-protocol-requests-and-responses"></a>SCIM запросов и ответов протокола
Этот раздел содержит пример SCIM запросы генерируемые клиентом Azure AD SCIM и пример ответы. Для получения наилучших результатов необходимо кодировать приложение для обработки этих запросов, в следующем формате и отправляет ответы.

>[!IMPORTANT]
>Чтобы понять, как и когда служба подготовки пользователей Azure AD выдает операции, описанные ниже, см. в разделе [что происходит во время подготовки пользователей?](user-provisioning.md#what-happens-during-provisioning).

- [Операции пользователя](#user-operations)
  - [Создание пользователя](#create-user)
    - [Запрос](#request)
    - [Ответ](#response)
  - [Получить пользователя](#get-user)
    - [Запрос](#request-1)
    - [Ответ](#response-1)
  - [Получить пользователя по запросу](#get-user-by-query)
    - [Запрос](#request-2)
    - [Ответ](#response-2)
  - [Получить пользователя запросом - нулевого результата](#get-user-by-query---zero-results)
    - [Запрос](#request-3)
    - [Ответ](#response-3)
  - [Обновление пользователя [многозначных свойствах]](#update-user-multi-valued-properties)
    - [Запрос](#request-4)
    - [Ответ](#response-4)
  - [Обновление пользователя [свойств с одним значением]](#update-user-single-valued-properties)
    - [Запрос](#request-5)
    - [Ответ](#response-5)
  - [Удалить пользователя](#delete-user)
    - [Запрос](#request-6)
    - [Ответ](#response-6)
- [Операции группы](#group-operations)
  - [Создание группы](#create-group)
    - [Запрос](#request-7)
    - [Ответ](#response-7)
  - [Получение группы](#get-group)
    - [Запрос](#request-8)
    - [Ответ](#response-8)
  - [Get Group by displayName](#get-group-by-displayname)
    - [Запрос](#request-9)
    - [Ответ](#response-9)
  - [Обновить группу [Non-member attributes]](#update-group-non-member-attributes)
    - [Запрос](#request-10)
    - [Ответ](#response-10)
  - [Группа обновлений [добавление членов]](#update-group-add-members)
    - [Запрос](#request-11)
    - [Ответ](#response-11)
  - [Обновить группу [удалить участников]](#update-group-remove-members)
    - [Запрос](#request-12)
    - [Ответ](#response-12)
  - [Удалить группу](#delete-group)
    - [Запрос](#request-13)
    - [Ответ](#response-13)

### <a name="user-operations"></a>Операции пользователя

* Пользователи могут запросить `userName` или `email[type eq "work"]` атрибуты.  

#### <a name="create-user"></a>Создать пользователя

###### <a name="request"></a>Запрос
*/ Пользователи БЛОГА*
```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "active": true,
    "emails": [{
        "primary": true,
        "type": "work",
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com"
    }],
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName"
    },
    "roles": []
}
```

##### <a name="response"></a>Отклик
*HTTP/1.1 201 Создано*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "48af03ac28ad4fb88478",
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```


#### <a name="get-user"></a>Получить пользователя

###### <a name="request"></a>Запрос
*GET /Users/5d48a0a8e9f04aa38008* 

###### <a name="response"></a>Отклик
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5d48a0a8e9f04aa38008",
    "externalId": "58342554-38d6-4ec8-948c-50044d0a33fd",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_feed3ace-693c-4e5a-82e2-694be1b39934",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_22370c1a-9012-42b2-bf64-86099c2a1c22@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```
#### <a name="get-user-by-query"></a>Получить пользователя по запросу

##### <a name="request"></a>Запрос
*GET /Users?filter=userName eq "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081"*

##### <a name="response"></a>Отклик
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
        "id": "2441309d85324e7793ae",
        "externalId": "7fce0092-d52e-4f76-b727-3955bd72c939",
        "meta": {
            "resourceType": "User",
            "created": "2018-03-27T19:59:26.000Z",
            "lastModified": "2018-03-27T19:59:26.000Z"
            
        },
        "userName": "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081",
        "name": {
            "familyName": "familyName",
            "givenName": "givenName"
        },
        "active": true,
        "emails": [{
            "value": "Test_User_91b67701-697b-46de-b864-bd0bbe4f99c1@testuser.com",
            "type": "work",
            "primary": true
        }]
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="get-user-by-query---zero-results"></a>Получить пользователя запросом - нулевого результата

##### <a name="request"></a>Запрос
*GET /Users?filter=userName eq "non-existent user"*

##### <a name="response"></a>Отклик
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 0,
    "Resources": [],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="update-user-multi-valued-properties"></a>Обновление пользователя [многозначных свойствах]

##### <a name="request"></a>Запрос
*PATCH/Users/6764549bef60420686bc HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [
            {
            "op": "Replace",
            "path": "emails[type eq \"work\"].value",
            "value": "updatedEmail@microsoft.com"
            },
            {
            "op": "Replace",
            "path": "name.familyName",
            "value": "updatedFamilyName"
            }
    ]
}
```

##### <a name="response"></a>Отклик
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "6764549bef60420686bc",
    "externalId": "6c75de36-30fa-4d2d-a196-6bdcdb6b6539",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_fbb9dda4-fcde-4f98-a68b-6c5599e17c27",
    "name": {
        "formatted": "givenName updatedFamilyName",
        "familyName": "updatedFamilyName",
        "givenName": "givenName"
    },
    "active": true,
    "emails": [{
        "value": "updatedEmail@microsoft.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="update-user-single-valued-properties"></a>Обновление пользователя [свойств с одним значением]

##### <a name="request"></a>Запрос
*PATCH/Users/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "userName",
        "value": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com"
    }]
}
```

##### <a name="response"></a>Отклик
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5171a35d82074e068ce2",
    "externalId": "aa1eca08-7179-4eeb-a0be-a519f7e5cd1a",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "userName": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_49dc1090-aada-4657-8434-4995c25a00f7@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="delete-user"></a>Удаление пользователя

##### <a name="request"></a>Запрос
*УДАЛИТЬ /Users/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response"></a>Отклик
*HTTP/1.1 204 Нет содержимого*

### <a name="group-operations"></a>Операции группы

* Группы всегда должны создаваться с помощью пустых элементов списка.
* Группы можно запрашивать с `displayName` атрибута.
* Обновление запроса PATCH группы должен передать *HTTP 204 No Content* в ответе. Возвращение текста со списком всех элементов не рекомендуется.
* Нет необходимости для поддержки возвращение всех элементов группы.

#### <a name="create-group"></a>Создать группу

##### <a name="request"></a>Запрос
*POST/Groups HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group", "http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/2.0/Group"],
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "displayName": "displayName",
    "members": [],
    "meta": {
        "resourceType": "Group"
    }
}
```

##### <a name="response"></a>Отклик
*HTTP/1.1 201 Создано*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "927fa2c08dcb4a7fae9e",
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "displayName": "displayName",
    "members": []
}
```

#### <a name="get-group"></a>Получить группу

##### <a name="request"></a>Запрос
*GET/группы/40734ae655284ad3abcc? excludedAttributes = члены HTTP/1.1*

##### <a name="response"></a>Отклик
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "40734ae655284ad3abcc",
    "externalId": "60f1bb27-2e1e-402d-bcc4-ec999564a194",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "displayName": "displayName",
}
```

#### <a name="get-group-by-displayname"></a>Get Group by displayName

##### <a name="request"></a>Запрос
*/ Groups GET? excludedAttributes = фильтр & члены = displayName eq «displayName» HTTP/1.1*

##### <a name="response"></a>Отклик
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
        "id": "8c601452cc934a9ebef9",
        "externalId": "0db508eb-91e2-46e4-809c-30dcbda0c685",
        "meta": {
            "resourceType": "Group",
            "created": "2018-03-27T22:02:32.000Z",
            "lastModified": "2018-03-27T22:02:32.000Z",
            
        },
        "displayName": "displayName",
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}
```
#### <a name="update-group-non-member-attributes"></a>Обновить группу [Non-member attributes]

##### <a name="request"></a>Запрос
*PATCH/группы/fa2ce26709934589afc5 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "displayName",
        "value": "1879db59-3bdf-4490-ad68-ab880a269474updatedDisplayName"
    }]
}
```

##### <a name="response"></a>Отклик
*HTTP/1.1 204 Нет содержимого*

### <a name="update-group-add-members"></a>Группа обновлений [добавление членов]

##### <a name="request"></a>Запрос
*PATCH /Groups/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Add",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response"></a>Отклик
*HTTP/1.1 204 Нет содержимого*

#### <a name="update-group-remove-members"></a>Обновить группу [удалить участников]

##### <a name="request"></a>Запрос
*PATCH /Groups/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Remove",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response"></a>Отклик
*HTTP/1.1 204 Нет содержимого*

#### <a name="delete-group"></a>Удалить группу

##### <a name="request"></a>Запрос
*DELETE /Groups/cdb1ce18f65944079d37 HTTP/1.1*

##### <a name="response"></a>Отклик
*HTTP/1.1 204 Нет содержимого*


## <a name="building-a-scim-endpoint-using-microsoft-cli-libraries"></a>Создание конечной точки SCIM, с помощью библиотеки Microsoft CLI
Создание веб-службу SCIM, который взаимодействует с Azure Active Directory, вы можете включить автоматическую подготовку пользователей практически любого приложения или хранилища удостоверений.

Вот как это работает

1. Azure AD предоставляет общую библиотеку language infrastructure (CLI) с именем Microsoft.SystemForCrossDomainIdentityManagement, вместе с кодом, примерах описываются ниже. Системные интеграторы и разработчики могут использовать эту библиотеку для создания и развертывания конечной точки SCIM веб-службы, которая может подключаться Azure AD к хранилищу удостоверений любого приложения.
2. Веб-служба реализует механизмы сопоставления стандартной пользовательской схемы c пользовательской схемой и протоколом, которые использует приложение. 
3. Azure AD хранит URL-адрес конечной точки как часть информации о пользовательском приложении в коллекции приложений.
4. Администратор назначает для этого приложения пользователей и группы в Azure AD. После назначения они помещаются в очередь для синхронизации с целевого приложения. Процесс синхронизации каждые 40 минут выполняет задания из этой очереди.

### <a name="code-samples"></a>Примеры кода
Чтобы упростить этот процесс [примеры кода](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) не указаны, которые создают SCIM веб-службы конечной точки и демонстрируют автоматическую подготовку пользователей. Образец представляет поставщика, который обслуживает файл со строками значений с разделителями запятыми, представляющий пользователей и групп.    

**Предварительные требования**

* Visual Studio 2013 или более поздней версии
* [Пакет Azure SDK для .NET](https://azure.microsoft.com/downloads/)
* Компьютер под управлением Windows, поддерживающий платформу ASP.NET 4.5, на котором будет размещена конечная точка SCIM. Этот компьютер должен быть доступен из облака.
* [Подписка Azure с пробной или лицензированной версией Azure AD Premium.](https://azure.microsoft.com/services/active-directory/)

### <a name="getting-started"></a>Начало работы
Начнем с самого простого способа реализации конечной точки SCIM. Этот пример кода принимает запросы на подготовку от Azure AD и сохраняет пользователей в файл формата CSV (значения, разделенные запятыми).

#### <a name="to-create-a-sample-scim-endpoint"></a>Создание конечной точки SCIM

1. Скачайте пакет с примером кода со страницы [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)
1. Распакуйте пакет и поместите его в любую удобную папку на компьютере Windows, например C:\AzureAD-BYOA-Provisioning-Samples.
1. В этой папке запустите проект FileProvisioning\Host\FileProvisioningService.csproj в Visual Studio.
1. Выберите **средства** > **диспетчер пакетов NuGet** > **консоль диспетчера пакетов**и выполните следующие команды для Чтобы разрешить ссылки на решения в проекте FileProvisioningService:

   ```
    Update-Package -Reinstall
   ```

1. Создайте проект FileProvisioningService.
1. Запустите в Windows приложение командной строки (с правами администратора) и с помощью команды **cd** перейдите в каталог **\AzureAD-BYOA-Provisioning-Samples\FileProvisioning\Host\bin\Debug**.
1. Выполните следующую команду, заменив `<ip-address>` IP-адреса или домена имя компьютера Windows:

   ```
    FileSvc.exe http://<ip-address>:9000 TargetFile.csv
   ```

1. В Windows в разделе **параметры Windows** > **параметры сети и Интернета**выберите **брандмауэра Windows**  >   **Дополнительные параметры**и создайте **правило для входящего трафика** , разрешающее входящий доступ к порту 9000.
1. Если компьютер Windows находится за маршрутизатором, маршрутизатор должен быть настроен для запуска преобразования сетевых адресов между внешним доступом к Интернету и портом 9000 на компьютере Windows. Эта настройка необходима для Azure AD для доступа к этой конечной точки в облаке.

#### <a name="to-register-the-sample-scim-endpoint-in-azure-ad"></a>Регистрация примера конечной точки SCIM в Azure AD

1. Войдите в [портал Azure Active Directory](https://aad.portal.azure.com). 

1. Выберите **корпоративные приложения** в левой области. Отображается список всех настроенных приложений, включая приложения, которые были добавлены из коллекции.

1. Выберите **+ новое приложение** > **все** > **приложение не из коллекции**.

1. Введите имя для вашего приложения и выберите **добавить** для создания объекта приложения. Созданный объект приложения будет представлять не только конечную точку SCIM, но и все целевое приложение, для которого вы настраиваете подготовку пользователей и единый вход.

1. На экране управления приложения, выберите **Подготовка** на левой панели.

1. В меню **Режим подготовки к работе** выберите **Автоматический**.
    
   ![][2]
   *Рисунок 6. Настройка автоматической подготовки пользователей на портале Azure*
    
1. В поле **URL-адрес клиента** введите доступные в Интернете URL-адрес и порт конечной точки SCIM. Это будет строка типа http://testmachine.contoso.com:9000 или http://\<ip-address>:9000/, где \<ip-address> является IP-адресом, доступным в Интернете. 

1. Если конечной точке SCIM требуется токен носителя OAuth от издателя, отличного от Azure AD, скопируйте необходимый токен носителя OAuth в необязательное для заполнения поле **Секретный токен**. Если это поле оставлено пустым, Azure AD будет включать токен носителя OAuth, выданный Azure AD с каждым запросом. Приложения, использующие Azure AD в качестве поставщика удостоверений, могут проверить этот выданный Azure AD токен.

1. Выберите **проверить подключение** Azure Active Directory попробует подключиться к конечной точке SCIM. Если попытка завершается неудачей, сведения об ошибке отображается.  

    >[!NOTE]
    >**Проверка подключения** запрашивает конечную точку SCIM для пользователя, который не существует, используя случайный идентификатор GUID в качестве свойства сопоставления, выбранного в конфигурации Azure AD. Ожидаемый правильный ответ — HTTP 200 OK с пустым сообщением ListResponse SCIM.

1. Если попытка соединения с приложением будет успешной, затем выберите **Сохранить** сохранить учетные данные администратора.

1. В разделе **Сопоставления** доступны на выбор два набора сопоставлений атрибутов: для объектов-пользователей и для объектов групп. Выберите каждый из них, чтобы просмотреть атрибуты, которые синхронизируются из Azure Active Directory с вашим приложением. Атрибуты, выбранные как свойства **Matching**, используются для сопоставления пользователей и групп в вашем приложении для операций обновления. Чтобы зафиксировать изменения, щелкните **Сохранить**.

1. Поле **Область** в разделе **Параметры** определяет, какие пользователи или группы синхронизируются. Выберите **«синхронизация только назначенных пользователей и групп** (рекомендуется), чтобы синхронизировать только пользователей и групп, назначенных в **пользователей и групп** вкладки.

1. По завершении конфигурации задайте **состояние подготовки** для **на**.

1. Выберите **Сохранить** запустить службу подготовки Azure AD. 

1. Если синхронизация только назначенных пользователей и групп (рекомендуется), не забудьте выбрать **пользователей и групп** вкладку и назначение пользователей или групп, которые требуется синхронизировать.

После запуска начальной синхронизации, можно выбрать **журналы аудита** в левой панели, чтобы отслеживать ход выполнения, показывающий все действия, выполняемой службой подготовки в приложении. Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](check-status-user-account-provisioning.md).

Наконец, последний этап проверки примера. Откройте файл TargetFile.csv в папке \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug на компьютере Windows. Когда процесс подготовки будет запущен, в этом файле отобразятся сведения обо всех пользователях и группах, которые были назначены и подготовлены.

### <a name="development-libraries"></a>Библиотеки для разработчика
Чтобы разработать свою веб-службу, которая соответствует спецификации SCIM, ознакомьтесь с этими библиотеками Майкрософт, которые помогут ускорить процесс разработки. 

- Библиотеки Common Language Infrastructure (CLI) для использования с языками, основанными на этой инфраструктуре, например C#. Одна из этих библиотек, Microsoft.SystemForCrossDomainIdentityManagement.Service, объявляет интерфейс, Microsoft.SystemForCrossDomainIdentityManagement.IProvider, показанный на следующем рисунке. Разработчик, использующий эти библиотеки, может реализовать класс с этим интерфейсом и обращаться к нему как к поставщику службы. Эти библиотеки позволяют разработчику развернуть веб-службы, которая соответствует спецификации SCIM. Веб-служба может размещаться в службах IIS, или в любой исполняемой сборке CLI. Запрос преобразовывается в вызовы методов поставщика, которые программирует разработчик для управления хранилищем удостоверений.
  
   ![][3]
  
- [Обработчики ExpressRoute](https://expressjs.com/guide/routing.html) используются для анализа объектов запроса Node.js, которые представляют собой вызовы (как определено в спецификации SCIM), направленные к веб-службе Node.js.   

### <a name="building-a-custom-scim-endpoint"></a>Создание пользовательской конечной точки SCIM
Разработчики, использующие библиотеки CLI могут размещать свои службы в любой исполняемой сборке CLI или в службах IIS. Ниже приведен пример кода для размещения службы в исполняемой сборке по адресу http://localhost:9000: 

    private static void Main(string[] arguments)
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IProvider and 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
      new DevelopersMonitor();
    Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
      new DevelopersProvider(arguments[1]);
    Microsoft.SystemForCrossDomainIdentityManagement.Service webService = null;
    try
    {
        webService = new WebService(monitor, provider);
        webService.Start("http://localhost:9000");

        Console.ReadKey(true);
    }
    finally
    {
        if (webService != null)
        {
            webService.Dispose();
            webService = null;
        }
    }
    }

    public class WebService : Microsoft.SystemForCrossDomainIdentityManagement.Service
    {
    private Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor;
    private Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider;

    public WebService(
      Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitoringBehavior, 
      Microsoft.SystemForCrossDomainIdentityManagement.IProvider providerBehavior)
    {
        this.monitor = monitoringBehavior;
        this.provider = providerBehavior;
    }

    public override IMonitor MonitoringBehavior
    {
        get
        {
            return this.monitor;
        }

        set
        {
            this.monitor = value;
        }
    }

    public override IProvider ProviderBehavior
    {
        get
        {
            return this.provider;
        }

        set
        {
            this.provider = value;
        }
    }
    }

Эта служба должна иметь HTTP-адрес и сертификат проверки подлинности сервера одного из следующих корневых центров сертификации: 

* CNNIC;
* Comodo;
* CyberTrust;
* DigiCert;
* GeoTrust;
* GlobalSign;
* Go Daddy;
* VeriSign;
* WoSign

Сертификат проверки подлинности сервера можно привязать к порту на узле Windows с помощью служебной программы сетевой оболочки. 

    netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}  

Здесь в качестве значения аргумента certhash предоставляется отпечаток сертификата, а в качестве значения аргумента appid — произвольный глобальный уникальный идентификатор.  

Чтобы разместить службу в службах IIS, разработчик должен создать сборку библиотеки кода CLI с класс Startup в пространстве имен по умолчанию сборки.  Ниже приведен пример такого класса. 

    public class Startup
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor and  
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter starter;

    public Startup()
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
          new DevelopersMonitor();
        Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
          new DevelopersProvider();
        this.starter = 
          new Microsoft.SystemForCrossDomainIdentityManagement.WebApplicationStarter(
            provider, 
            monitor);
    }

    public void Configuration(
      Owin.IAppBuilder builder) // Defined in Owin.dll.  
    {
        this.starter.ConfigureApplication(builder);
    }
    }

### <a name="handling-endpoint-authentication"></a>Обработка аутентификации на конечной точке
Запросы от Azure Active Directory содержат токен носителя OAuth 2.0.   Любая служба, получающая запрос должна убедиться, что токен выдала Azure Active Directory для ожидаемого клиента Azure Active Directory, для доступа к веб-службы Azure Active Directory Graph.  В токене издатель обозначается утверждением iss, например «iss»:» https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/«.  В этом примере базовый адрес утверждения со значением https://sts.windows.net, определяющее Azure Active Directory в качестве поставщика, относительного адреса сегмента, cbb1a5ac-f33b-45fa-9bf5-f37db0fed422, — это уникальный идентификатор клиента Azure Active Directory для выданный маркер.  Если токен выпущен для доступа к веб-службе Azure Active Directory Graph, то утверждение aud такого токена должно иметь значение 00000002-0000-0000-c000-000000000000, то есть значение идентификатора этой веб-службы.  Каждое из приложений, которые зарегистрированы в одном клиенте может появиться же `iss` утверждения с запросами SCIM.

Разработчики, использующие библиотеки CLI, предоставляемые корпорацией Майкрософт для создания службы SCIM могут аутентифицировать запросы от Azure Active Directory с помощью пакета Microsoft.Owin.Security.ActiveDirectory, выполнив следующие действия: 

1. Реализуйте в поставщике свойство Microsoft.SystemForCrossDomainIdentityManagement.IProvider.StartupBehavior, которое будет возвращать метод, вызываемый при каждом запуске службы. 

   ```
     public override Action\<Owin.IAppBuilder, System.Web.Http.HttpConfiguration.HttpConfiguration\> StartupBehavior
     {
       get
       {
         return this.OnServiceStartup;
       }
     }

     private void OnServiceStartup(
       Owin.IAppBuilder applicationBuilder,  // Defined in Owin.dll.  
       System.Web.Http.HttpConfiguration configuration)  // Defined in System.Web.Http.dll.  
     {
     }
   ```

2. К этому методу, чтобы любой запрос на любой из конечных точек службы, проверку подлинности как выпустившая маркер, выданный службой Azure Active Directory для указанного клиента для доступа к веб-службы Azure AD Graph, добавьте следующий код: 

   ```
     private void OnServiceStartup(
       Owin.IAppBuilder applicationBuilder IAppBuilder applicationBuilder, 
       System.Web.Http.HttpConfiguration HttpConfiguration configuration)
     {
       // IFilter is defined in System.Web.Http.dll.  
       System.Web.Http.Filters.IFilter authorizationFilter = 
         new System.Web.Http.AuthorizeAttribute(); // Defined in System.Web.Http.dll.configuration.Filters.Add(authorizationFilter);

       // SystemIdentityModel.Tokens.TokenValidationParameters is defined in    
       // System.IdentityModel.Token.Jwt.dll.
       SystemIdentityModel.Tokens.TokenValidationParameters tokenValidationParameters =     
         new TokenValidationParameters()
         {
           ValidAudience = "00000002-0000-0000-c000-000000000000"
         };

       // WindowsAzureActiveDirectoryBearerAuthenticationOptions is defined in 
       // Microsoft.Owin.Security.ActiveDirectory.dll
       Microsoft.Owin.Security.ActiveDirectory.
       WindowsAzureActiveDirectoryBearerAuthenticationOptions authenticationOptions =
         new WindowsAzureActiveDirectoryBearerAuthenticationOptions()    {
         TokenValidationParameters = tokenValidationParameters,
         Tenant = "03F9FCBC-EA7B-46C2-8466-F81917F3C15E" // Substitute the appropriate tenant’s 
                                                       // identifier for this one.  
       };

       applicationBuilder.UseWindowsAzureActiveDirectoryBearerAuthentication(authenticationOptions);
     }
   ```

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>Обработка подготовку и отзыв для пользователей

1. Azure Active Directory запрашивает службу пользователя, у которого значение атрибута externalId совпадает со значением атрибута mailNickname пользователя Azure AD. Этот запрос выражается как запрос протокола передачи гипертекста (HTTP), как показано в примере, при котором jyoung является примером mailNickname пользователя в Azure Active Directory.

    >[!NOTE]
    > Это только пример. Не все пользователи будут иметь атрибут mailNickname, и значение, которое у пользователя могут не быть уникальными в каталоге. Кроме того, атрибут, используемый для сопоставления (который в данном случае является externalId) можно настроить в [сопоставления атрибутов в Azure AD](customize-application-attributes.md).

   ````
    GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
    Authorization: Bearer ...
   ````
   Если служба построена с использованием библиотеки CLI, предоставляемые корпорацией Майкрософт для реализации служб SCIM, то запрос будет преобразован в вызов метода Query поставщика услуги.  Подпись этого метода будет выглядеть так: 
   ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]> Query(
      Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
      string correlationIdentifier);
   ````
   Ниже приводится определение интерфейса Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters. 
   ````
    public interface IQueryParameters: 
      Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
        System.Collections.Generic.IReadOnlyCollection <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
        { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
      system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
      { get; }
      System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
      { get; }
      string SchemaIdentifier 
      { get; }
    }

   ```
     GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
     Authorization: Bearer ...
   ```

   If the service was built using the Common Language Infrastructure libraries provided by Microsoft for implementing SCIM services, then the request is translated into a call to the Query method of the service’s provider.  Here is the signature of that method: 

   ```
     // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
     // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
     // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  
 
     System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]>  Query(
       Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
       string correlationIdentifier);
   ```

   Here is the definition of the Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters interface: 

   ```
     public interface IQueryParameters: 
       Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
     {
         System.Collections.Generic.IReadOnlyCollection  <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
         { get; }
     }

     public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
     {
       system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
       { get; }
       System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
       { get; }
       string SchemaIdentifier 
       { get; }
     }

     public interface Microsoft.SystemForCrossDomainIdentityManagement.IFilter
     {
         Microsoft.SystemForCrossDomainIdentityManagement.IFilter AdditionalFilter 
           { get; set; }
         string AttributePath 
           { get; } 
         Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator FilterOperator 
           { get; }
         string ComparisonValue 
           { get; }
     }

     public enum Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator
     {
         Equals
     }
   ```

   In the following sample of a query for a user with a given value for the externalId attribute, values of the arguments passed to the Query method are: 
   * parameters.AlternateFilters.Count: 1
   * parameters.AlternateFilters.ElementAt(0).AttributePath: "externalId"
   * parameters.AlternateFilters.ElementAt(0).ComparisonOperator: ComparisonOperator.Equals
   * parameters.AlternateFilter.ElementAt(0).ComparisonValue: "jyoung"
   * correlationIdentifier: System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin.RequestId"] 

2. If the response to a query to the web service for a user with an externalId attribute value that matches the mailNickname attribute value of a user doesn't return any users, then Azure Active Directory requests that the service provision a user corresponding to the one in Azure Active Directory.  Here is an example of such a request: 

   ````
    Авторизация HTTP/1.1 https://.../scim/Users POST: Носителя...  Тип содержимого: application/scim + json {«схемы»: [«urn: ietf:params:scim:schemas:core:2.0:User», «urn: ietf:params:scim:schemas:extension:enterprise:2.0User»] «externalId»: «jyoung», «userName»: «jyoung», «active»: true, «адреса»: null,    «displayName»: «Радости Young», «сообщения электронной почты»: [{«type»: «рабочая», «value»:»jyoung@Contoso.com«, «primary»: true}], «meta»: {«resourceType»: «User»}, «name»: {«familyName»: «Маленькие», «givenName»: «Радости»}, свойство «phoneNumbers»: значение "null", «preferredLa nguage»: null, «title»: значение "null", «отдел»: null, «manager»: null}
   ````
   The CLI libraries provided by Microsoft for implementing SCIM services would translate that request into a call to the Create method of the service’s provider.  The Create method has this signature: 
   ````
    System.Threading.Tasks.Tasks определяется в библиотеке mscorlib.dll.  
    Microsoft.SystemForCrossDomainIdentityManagement.Resource определяется в / / Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  

    System.Threading.Tasks.Task < Microsoft.SystemForCrossDomainIdentityManagement.Resource > Создать (Microsoft.SystemForCrossDomainIdentityManagement.Resource ресурсов, correlationIdentifier строку);
   ````
   In a request to provision a user, the value of the resource argument is an instance of the Microsoft.SystemForCrossDomainIdentityManagement. Core2EnterpriseUser class, defined in the Microsoft.SystemForCrossDomainIdentityManagement.Schemas library.  If the request to provision the user succeeds, then the implementation of the method is expected to return an instance of the Microsoft.SystemForCrossDomainIdentityManagement. Core2EnterpriseUser class, with the value of the Identifier property set to the unique identifier of the newly provisioned user.  

3. To update a user known to exist in an identity store fronted by an SCIM, Azure Active Directory proceeds by requesting the current state of that user from the service with a request such as: 
   ````
    ПОЛУЧИТЕ авторизацию ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1: Носителя...
   ````
   In a service built using the CLI libraries provided by Microsoft for implementing SCIM services, the request is translated into a call to the Retrieve method of the service’s provider.  Here is the signature of the Retrieve method: 
   ````
    System.Threading.Tasks.Tasks определяется в библиотеке mscorlib.dll.  
    Microsoft.SystemForCrossDomainIdentityManagement.Resource и / или Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters / / определяются в Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    System.Threading.Tasks.Task < Microsoft.SystemForCrossDomainIdentityManagement.Resource > Получение (параметры Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters, correlationIdentifier строку);

    открытый интерфейс Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters:   
        {Идентификатор_ресурса Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier {get;}} IRetrievalParameters открытый интерфейс Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier {string идентификатор {get; set;} Строка Microsoft.SystemForCrossDomainIdentityManagement.SchemaIdentifier {get; set;}}
   ````
   In the example of a request to retrieve the current state of a user, the values of the properties of the object provided as the value of the parameters argument are as follows: 
  
   * Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

4. If a reference attribute is to be updated, then Azure Active Directory queries the service to determine whether the current value of the reference attribute in the identity store fronted by the service already matches the value of that attribute in Azure Active Directory. For users, the only attribute of which the current value is queried in this way is the manager attribute. Here is an example of a request to determine whether the manager attribute of a particular user object currently has a certain value: 

   If the service was built using the CLI libraries provided by Microsoft for implementing SCIM services, then the request is translated into a call to the Query method of the service’s provider. The value of the properties of the object provided as the value of the parameters argument are as follows: 
  
   * parameters.AlternateFilters.Count: 2
   * parameters.AlternateFilters.ElementAt(x).AttributePath: "ID"
   * parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
   * parameters.AlternateFilter.ElementAt(x).ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
   * parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
   * parameters.AlternateFilter.ElementAt(y).ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
   * parameters.RequestedAttributePaths.ElementAt(0): "ID"
   * parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

   Here, the value of the index x can be 0 and the value of the index y can be 1, or the value of x can be 1 and the value of y can be 0, depending on the order of the expressions of the filter query parameter.   

5. Here is an example of a request from Azure Active Directory to an SCIM service to update a user: 
   ````
    Авторизация HTTP/1.1 ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 ИСПРАВЛЕНИЯ: Носителя...  Тип содержимого: приложения/scim + json {«схемы»: [«urn: ietf:params:scim:api:messages:2.0:PatchOp»], «Операции»: [{«op»: «Добавить», «path»: «manager», «value»: [{«$ref»: « http://.../scim/Users/2819c223-7f76-453a-919d-413861904646», «value»: «2819c223-7f76-453a-919d-413861904646»}]}]}
   ````
   The Microsoft CLI libraries for implementing SCIM services would translate the request into a call to the Update method of the service’s provider. Here is the signature of the Update method: 
   ````
    System.Threading.Tasks.Tasks и / или System.Collections.Generic.IReadOnlyCollection<T> / / определены в библиотеке mscorlib.dll.  
    Microsoft.SystemForCrossDomainIdentityManagement.IPatch, / / Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, / / Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, / / Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, / / Microsoft.SystemForCrossDomainIdentityManagement.OperationName, / / Microsoft.SystemForCrossDomainIdentityManagement.IPath и / или Microsoft.SystemForCrossDomainIdentityManagement.OperationValue / / являются все определенные в Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

    System.Threading.Tasks.Task обновление (Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, correlationIdentifier строку);

    открытый интерфейс Microsoft.SystemForCrossDomainIdentityManagement.IPatch Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase PatchRequest {{get; set;} Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier ResourceIdentifier {get; set;}        
    }

    открытый класс PatchRequest2:    Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase {открытый System.Collections.Generic.IReadOnlyCollection < Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation > операции {get;}


   Если служба создана с использованием библиотек Common Language Infrastructure корпорации Майкрософт для реализации служб SCIM, то такой запрос будет преобразован в вызов метода Query поставщика службы. В нем передается аргумент parameters, значением которого будет объект со следующими свойствами. 
  
* parameters.AlternateFilters.Count: 2
* parameters.AlternateFilters.ElementAt(x).AttributePath: ID
* parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(x).ComparisonValue:  "54D382A4-2050-4C03-94D1-E769F1D15682"
* parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
* parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(y).ComparisonValue:  "2819c223-7f76-453a-919d-413861904646"
* parameters.RequestedAttributePaths.ElementAt(0): ID
* parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

  Здесь значение индекса x может быть равен 0 и значение y индекс может быть 1, или значение x может быть 1 и значение y может быть равен 0, в зависимости от порядка выражений параметр запроса filter.   

1. Ниже приведен пример запроса от Azure Active Directory к службе SCIM для обновления пользователя. 

   ```
     PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
     Authorization: Bearer ...
     Content-type: application/scim+json
     {
       "schemas": 
       [
         "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
       "Operations":
       [
         {
           "op":"Add",
           "path":"manager",
           "value":
             [
               {
                 "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
                 "value":"2819c223-7f76-453a-919d-413861904646"}]}]}
   ```

   Библиотеки Microsoft CLI для реализации службы SCIM преобразуют такой запрос в вызов метода Update поставщика услуги. Подпись метода Update выглядит следующим образом. 

   ```
     // System.Threading.Tasks.Tasks and 
     // System.Collections.Generic.IReadOnlyCollection<T>
     // are defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IPatch, 
     // Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, 
     // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
     // Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, 
     // Microsoft.SystemForCrossDomainIdentityManagement.OperationName, 
     // Microsoft.SystemForCrossDomainIdentityManagement.IPath and 
     // Microsoft.SystemForCrossDomainIdentityManagement.OperationValue 
     // are all defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

     System.Threading.Tasks.Task Update(
       Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, 
       string correlationIdentifier);

     public interface Microsoft.SystemForCrossDomainIdentityManagement.IPatch
     {
     Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase 
       PatchRequest 
         { get; set; }
     Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
       ResourceIdentifier 
         { get; set; }        
     }

     public class PatchRequest2: 
       Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase
     {
     public System.Collections.Generic.IReadOnlyCollection
       <Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation> 
         Operations
         { get;}

     public void AddOperation(
       Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation operation);
     }

     public class PatchOperation
     {
     public Microsoft.SystemForCrossDomainIdentityManagement.OperationName 
       Name
       { get; set; }

     public Microsoft.SystemForCrossDomainIdentityManagement.IPath 
       Path
       { get; set; }

     public System.Collections.Generic.IReadOnlyCollection
       <Microsoft.SystemForCrossDomainIdentityManagement.OperationValue> Value
       { get; }

     public void AddValue(
       Microsoft.SystemForCrossDomainIdentityManagement.OperationValue value);
     }

     public enum OperationName
     {
       Add,
       Remove,
       Replace
     }

     public interface IPath
     {
       string AttributePath { get; }
       System.Collections.Generic.IReadOnlyCollection<IFilter> SubAttributes { get; }
       Microsoft.SystemForCrossDomainIdentityManagement.IPath ValuePath { get; }
     }

     public class OperationValue
     {
       public string Reference
       { get; set; }

       public string Value
       { get; set; }
     }
   ```

    В примере запроса на обновление пользователя в качестве значения аргумента patch передается объект со следующими значениями свойств. 
  
   * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"
   * (PatchRequest as PatchRequest2).Operations.Count: 1
   * (PatchRequest as PatchRequest2).Operations.ElementAt(0).OperationName: OperationName.Add
   * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Path.AttributePath: "manager"
   * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.Count: 1
   * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Reference: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
   * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Value: 2819c223-7f76-453a-919d-413861904646

1. Для отзыва пользователя из хранилища удостоверений, интерфейсом для которого является служба SCIM, Azure AD отправляет запрос следующего вида. 

   ```
     DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
     Authorization: Bearer ...
   ```

   Если служба создана с использованием библиотек Common Language Infrastructure корпорации Майкрософт для реализации служб SCIM, то такой запрос будет преобразован в вызов метода Delete поставщика службы.   Подпись метода Delete будет выглядеть так: 

   ```
     // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
     // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
     System.Threading.Tasks.Task Delete(
       Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
         resourceIdentifier, 
       string correlationIdentifier);
   ```

   В примере запроса на отзыв пользователя в качестве значения аргумента resourceIdentifier передается объект со следующими значениями свойств. 

1. Для отзыва пользователя из хранилища удостоверений, интерфейсом для которого является служба SCIM, Azure AD отправляет запрос следующего вида. 
   ````
    DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
   ````
   Если служба построена с использованием библиотеки CLI, предоставляемые корпорацией Майкрософт для реализации служб SCIM, то запрос будет преобразован в вызов метода Delete поставщика услуги.   Подпись метода Delete будет выглядеть так: 
   ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
    System.Threading.Tasks.Task Delete(
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
        resourceIdentifier, 
      string correlationIdentifier);
   ````
   В примере запроса на отзыв пользователя в качестве значения аргумента resourceIdentifier передается объект со следующими значениями свойств. 
  
   * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="user-and-group-schema-reference"></a>Справочник по схеме пользователей и групп
Azure Active Directory может предоставлять веб-службе SCIM ресурсы двух типов.  Этими типами являются пользователи и группы.  

Ресурсы пользователя определяются с помощью идентификатора схемы `urn:ietf:params:scim:schemas:extension:enterprise:2.0:User`, который входит в данную спецификацию протокола: https://tools.ietf.org/html/rfc7643.  По умолчанию сопоставление атрибутов пользователей в Azure Active Directory к атрибутам ресурсы пользователя предоставляется в таблице 1.  

Ресурсы группы определяются с помощью идентификатора схемы `urn:ietf:params:scim:schemas:core:2.0:Group`. В таблице 2 показано по умолчанию сопоставление атрибутов групп в Azure Active Directory на атрибуты, группы ресурсов.  

### <a name="table-1-default-user-attribute-mapping"></a>Таблица 1. Сопоставление атрибутов пользователя по умолчанию

| Пользователь Azure Active Directory | "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted |активно |
| displayName |displayName |
| Facsimile-TelephoneNumber |phoneNumbers[type eq "fax"].value |
| givenName |name.givenName |
| jobTitle |title |
| почта |emails[type eq "work"].value |
| mailNickname |externalId |
| руководитель |manager |
| mobile |phoneNumbers[type eq "mobile"].value |
| objectId |ИД |
| postalCode |addresses[type eq "work"].postalCode |
| proxy-Addresses |emails[type eq "other"].Value |
| physical-Delivery-OfficeName |addresses[type eq "other"].Formatted |
| streetAddress |addresses[type eq "work"].streetAddress |
| surname |name.familyName |
| telephone-Number |phoneNumbers[type eq "work"].value |
| user-PrincipalName |userName |

### <a name="table-2-default-group-attribute-mapping"></a>Таблица 2. Сопоставление атрибутов групп по умолчанию

| Группа Azure Active Directory | urn:ietf:params:scim:schemas:core:2.0:Group |
| --- | --- |
| displayName |externalId |
| почта |emails[type eq "work"].value |
| mailNickname |displayName |
| члены |члены |
| objectId |ИД |
| proxyAddresses |emails[type eq "other"].Value |


## <a name="related-articles"></a>Связанные статьи
* [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS](user-provisioning.md)
* [Настройка сопоставления атрибутов для подготовки пользователей](customize-application-attributes.md)
* [Запись выражений для сопоставления атрибутов](functions-for-customizing-application-data.md)
* [Фильтры области для подготовки пользователей](define-conditional-rules-for-provisioning-user-accounts.md)
* [Уведомления о подготовке учетных записей](user-provisioning.md)
* [Список учебников по интеграции приложений SaaS](../saas-apps/tutorial-list.md)

<!--Image references-->
[0]: ./media/use-scim-to-provision-users-and-groups/scim-figure-1.png
[1]: ./media/use-scim-to-provision-users-and-groups/scim-figure-2a.png
[2]: ./media/use-scim-to-provision-users-and-groups/scim-figure-2b.png
[3]: ./media/use-scim-to-provision-users-and-groups/scim-figure-3.png
[4]: ./media/use-scim-to-provision-users-and-groups/scim-figure-4.png
[5]: ./media/use-scim-to-provision-users-and-groups/scim-figure-5.png
