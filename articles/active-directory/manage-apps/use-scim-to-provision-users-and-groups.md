---
title: Подготовка пользователей SCIM с помощью Azure Active Directory | Документация Майкрософт
description: Узнайте, как создать конечную точку SCIM, интегрировать API SCIM с Azure Active Directory и начать автоматизировать пользователей и группы подготовки в своих приложениях.
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
ms.date: 10/01/2019
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9344feeadc5f4146c3b3f853082cd9255100af5c
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949637"
---
# <a name="scim-user-provisioning-with-azure-active-directory"></a>SCIM подготовка пользователей с помощью Azure Active Directory

Система для управления междоменными удостоверениями (SCIM) является стандартизированным протоколом и схемой, нацеленной на более высокую степень согласованности при управлении удостоверениями в разных системах. Когда приложение поддерживает конечную точку SCIM для управления пользователями, служба подготовки пользователей Azure AD может отправить запросы на создание, изменение и удаление назначенных пользователей и групп в этой конечной точке.

Многие приложения, для которых Azure AD поддерживает [предварительно интегрированную автоматическую подготовку пользователей](../saas-apps/tutorial-list.md) , реализуют scim как средства для получения уведомлений о смене пользователей.  Кроме того, клиенты могут подключаться к приложениям, поддерживающим конкретный профиль [спецификации протокола SCIM 2,0](https://tools.ietf.org/html/rfc7644) , с помощью универсального параметра интеграции "не из коллекции" в портал Azure.

Основное внимание в этой статье заключается в профиле SCIM 2,0, который Azure AD реализует как часть универсального соединителя SCIM для приложений, не относящихся к коллекции. Однако успешное тестирование приложения, которое поддерживает SCIM с универсальным соединителем Azure AD, — это шаг для получения приложения, указанного в коллекции Azure AD, в качестве вспомогательной подготовки пользователей. Дополнительные сведения о получении приложения в коллекции приложений Azure AD см. в разделе [How: Перечислите свое приложение в коллекции приложений Azure AD @ no__t-0.

> [!IMPORTANT]
> Поведение реализации SCIM Azure AD в последний раз обновлялось 18 декабря 2018 г. Дополнительные сведения об изменениях см. в разделе, [посвященном соответствию требованиям протокола SCIM 2.0 службы подготовки пользователей Azure AD](application-provisioning-config-problem-scim-compatibility.md).

подготовка @no__t 0Shows из Azure AD в приложение или хранилище удостоверений @ no__t-1<br/>
*Рис. 1. Подготовка от Azure Active Directory к приложению или хранилищу удостоверений, реализующему SCIM @ no__t-0

Эта статья разбита на четыре раздела:

* **[Подготовка пользователей и групп для сторонних приложений, поддерживающих SCIM 2,0](#provisioning-users-and-groups-to-applications-that-support-scim)** . Если ваша организация использует стороннее приложение, которое РЕАЛИЗУЕТ профиль scim 2,0, поддерживаемый Azure AD, можно запустить автоматизацию подготовки и Сейчас следует отменить подготовку пользователей и групп.
* **[Основные сведения о реализации Azure AD scim](#understanding-the-azure-ad-scim-implementation)** . Если вы создаете приложение, которое поддерживает API управления пользователями scim 2,0, в этом разделе подробно описано, как реализуется клиент Azure AD scim и как следует МОДЕЛИРОВАТЬ протокол scim. Обработка запросов и ответы.
* **[Создание конечной точки scim с помощью библиотек Microsoft CLI (библиотеки](#building-a-scim-endpoint-using-microsoft-cli-libraries)** Common Language INFRASTRUCTURE (CLI)) вместе с примерами кода показывает, как разрабатывать конечную точку scim и переводить сообщения scim.  
* **[Справочник по схеме пользователей и групп](#user-and-group-schema-reference)** . Описание схемы пользователей и групп, поддерживаемых реализацией Azure AD scim для приложений, не относящихся к коллекции.

## <a name="provisioning-users-and-groups-to-applications-that-support-scim"></a>Подготовка пользователей и групп для приложений, поддерживающих SCIM

Azure AD можно настроить на автоматическую подготавливать назначенных пользователей и группы к приложениям, реализующим конкретный профиль [протокола SCIM 2,0](https://tools.ietf.org/html/rfc7644). Особенности профиля описаны в статье [Общие сведения о реализации Azure AD scim](#understanding-the-azure-ad-scim-implementation).

Рекомендуется связаться с поставщиком приложения или ознакомиться с документацией поставщика для получения подтверждения соответствия этим требованиям.

> [!IMPORTANT]
> Реализация Azure AD SCIM создана на основе службы подготовки пользователей Azure AD, которая обеспечивает постоянную синхронизацию пользователей между Azure AD и целевым приложением и реализует очень конкретный набор стандартных операций. Важно понимать эти поведения, чтобы понять поведение клиента Azure AD SCIM. Дополнительные сведения см. в разделе [что происходит во время подготовки пользователей?](user-provisioning.md#what-happens-during-provisioning).

### <a name="getting-started"></a>Приступая к работе

Описанные ранее в этой статье приложения, поддерживающие профиль SCIM, можно подключать к Azure Active Directory с помощью функции "Приложение не из коллекции", доступной в коллекции приложений Azure AD. После подключения Azure AD запускает процесс синхронизации каждые 40 минут, запрашивая конечную точку SCIM приложения для получения назначенных пользователей и групп, и создает или изменяет их согласно сведениям о назначении.

**Подключение приложения, поддерживающего SCIM:**

1. Войдите на [портал Azure Active Directory](https://aad.portal.azure.com). Обратите внимание, что вы можете получить доступ к бесплатной пробной версии Azure Active Directory с лицензиями P2, зарегистрировавшись в [программе разработчика](https://developer.microsoft.com/office/dev-program) .
1. В области слева выберите **корпоративные приложения** . Отображается список всех настроенных приложений, включая приложения, добавленные из коллекции.
1. Выберите **+ новое приложение** > **все** **приложения, не**относящиеся к коллекции  > .
1. Введите имя приложения и нажмите кнопку **Добавить** , чтобы создать объект приложения. Новое приложение будет добавлено в список корпоративных приложений и откроется на экране управления приложениями.

   ![Screenshot показывает коллекцию приложений Azure AD @ no__t-1<br/>
   *Рис. 2. Использование коллекции приложений Azure AD*

1. На экране Управление приложениями выберите **Подготовка** на панели слева.
1. В меню **Режим подготовки к работе** выберите **Автоматический**.

   ![Пример. Страница подготовки приложения в портал Azure @ no__t-0<br/>
   *Рис. 3. Настройка автоматической подготовки пользователей на портале Azure*

1. В поле **URL-адрес клиента** введите URL-адрес конечной точки SCIM приложения. Пример: https://api.contoso.com/scim/
1. Если конечной точке SCIM требуется токен носителя OAuth от издателя, отличного от Azure AD, скопируйте необходимый токен носителя OAuth в необязательное для заполнения поле **Секретный токен**. Если это поле оставлено пустым, Azure AD включает маркер носителя OAuth, выданный в Azure AD с каждым запросом. Приложения, использующие Azure AD в качестве поставщика удостоверений, могут проверить этот выданный Azure AD токен.
1. Выберите **проверить подключение** , чтобы Azure Active Directory попытаться подключиться к КОНЕЧНОЙ точке scim. Если попытка не удалась, выводится информация об ошибке.  

    > [!NOTE]
    > **Проверка подключения** запрашивает конечную точку SCIM для пользователя, который не существует, используя случайный идентификатор GUID в качестве свойства сопоставления, выбранного в конфигурации Azure AD. Ожидаемый правильный ответ — HTTP 200 OK с пустым сообщением ListResponse SCIM.

1. Если попытка подключения к приложению завершилась удачно, нажмите кнопку **сохранить** , чтобы сохранить учетные данные администратора.
1. В разделе **Сопоставления** доступны на выбор два набора сопоставлений атрибутов: для объектов-пользователей и для объектов групп. Выберите каждый из них, чтобы просмотреть атрибуты, которые синхронизируются из Azure Active Directory с вашим приложением. Атрибуты, выбранные как свойства **Matching**, используются для сопоставления пользователей и групп в вашем приложении для операций обновления. Чтобы зафиксировать изменения, щелкните **Сохранить**.

    > [!NOTE]
    > При необходимости можно отключить синхронизацию объектов групп, отключив сопоставление групп.

1. Поле **Область** в разделе **Параметры** определяет, какие пользователи и группы синхронизируются. Выберите **синхронизировать только назначенные пользователи и группы** (рекомендуется), чтобы синхронизировать только пользователей и группы, назначенные на вкладке **Пользователи и группы** .
1. После завершения настройки задайте для параметра **состояние подготовки** значение **вкл**.
1. Нажмите кнопку **сохранить** , чтобы запустить службу подготовки Azure AD.
1. Если выполняется синхронизация только назначенных пользователей и групп (рекомендуется), обязательно перейдите на вкладку **Пользователи и группы** и назначьте пользователей или группы, которые требуется синхронизировать.

После запуска начального цикла можно выбрать **журналы аудита** на панели слева, чтобы отслеживать ход выполнения, в котором отображаются все действия, выполняемые службой подготовки в приложении. Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](check-status-user-account-provisioning.md).

> [!NOTE]
> Начальный цикл занимает больше времени, чем последующие операции синхронизации, что происходит примерно каждые 40 минут при условии, что служба запущена.

**Чтобы опубликовать приложение в коллекции приложений Azure AD, выполните следующие действия.**

Если вы создаете приложение, которое будет использовать больше одного клиента, вы можете сделать его доступным в коллекции приложений Azure AD. Это упростит организациям Поиск приложения и настройку подготовки. Публикация приложения в коллекции Azure AD и обеспечение доступности подготовки для других пользователей — это просто. Узнайте [здесь](https://docs.microsoft.com/azure/active-directory/develop/howto-app-gallery-listing), как это сделать. 
## <a name="understanding-the-azure-ad-scim-implementation"></a>Основные сведения о реализации Azure AD SCIM

Если вы создаете приложение, которое поддерживает API управления пользователями SCIM 2,0, в этом разделе подробно описано, как реализуется клиент Azure AD SCIM и как следует моделировать обработку запросов и ответов протокола SCIM. После реализации конечной точки SCIM ее можно протестировать, выполнив процедуру, описанную в предыдущем разделе.

В [спецификации протокола SCIM 2,0](http://www.simplecloud.info/#Specification)приложение должно соответствовать следующим требованиям:

* Поддерживает создание пользователей и, при необходимости, групп, в разделе [3,3 протокола scim](https://tools.ietf.org/html/rfc7644#section-3.3).  
* Поддерживает изменение пользователей или групп с запросами PATCH в соответствии с [разделом 3.5.2 протокола scim](https://tools.ietf.org/html/rfc7644#section-3.5.2).  
* Поддерживает получение известного ресурса для пользователя или группы, созданной ранее, в соответствии с [разделом 3.4.1 протокола scim](https://tools.ietf.org/html/rfc7644#section-3.4.1).  
* Поддерживает запросы пользователей или групп в соответствии с разделом [3.4.2 протокола scim](https://tools.ietf.org/html/rfc7644#section-3.4.2).  По умолчанию пользователи извлекаются по `id` и запрашиваются по `username` и `externalid`, а группы запрашиваются по `displayName`.  
* Поддерживает запросы пользователей по ИДЕНТИФИКАТОРу и руководителю в соответствии с разделом 3.4.2 протокола SCIM.  
* Поддерживает запросы групп по ИДЕНТИФИКАТОРу и по члену, как в разделе 3.4.2 протокола SCIM.  
* Принимает один токен носителя для проверки подлинности и авторизации Azure AD в приложении.

При реализации конечной точки SCIM для обеспечения совместимости с Azure AD следуйте приведенным ниже общим рекомендациям.

* `id` является обязательным свойством для всех ресурсов. Каждый ответ, возвращающий ресурс, должен гарантировать, что каждый ресурс имеет это свойство, за исключением `ListResponse` с нулевым числом членов.
* Ответ на запрос запроса или фильтра всегда должен быть `ListResponse`.
* Группы являются необязательными, но поддерживаются только в том случае, если реализация SCIM поддерживает запросы на исправление.
* Не обязательно включать весь ресурс в ответ на исправление.
* В Microsoft Azure AD используются только следующие операторы:  
     - `eq`
     - `and`
* Не требуйте совпадения с учетом регистра для структурных элементов в SCIM, в частности, значений операций PATCH `op`, как определено в https://tools.ietf.org/html/rfc7644#section-3.5.2. Azure AD выдает значения "op" как `Add`, `Replace` и `Remove`.
* Microsoft Azure AD выполняет запросы на выбор случайного пользователя и группы, чтобы убедиться, что конечная точка и учетные данные действительны. Это также делается как часть **тестового потока подключения** в [портал Azure](https://portal.azure.com). 
* Атрибут, на котором могут запрашиваться ресурсы, должен быть установлен в качестве совпадающего атрибута для приложения в [портал Azure](https://portal.azure.com). Дополнительные сведения см. в разделе [Настройка сопоставлений атрибутов подготовки пользователей](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings) .

### <a name="user-provisioning-and-de-provisioning"></a>Подготовка и отзыв пользователей

На следующем рисунке показаны сообщения, которые Azure Active Directory отправляется в службу SCIM для управления жизненным циклом пользователя в хранилище удостоверений приложения.  

@no__t — 0Shows подготовка и отзыв учетной записи пользователя @ no__t-1<br/>
*Рис. 4. Последовательность действий при подготовке и отзыве пользователя*

### <a name="group-provisioning-and-de-provisioning"></a>Подготовка и отзыв групп

Подготовка и Отмена подготовки групп являются необязательными. При реализации и включении на следующем рисунке показаны сообщения, которые Azure AD отправляет в службу SCIM для управления жизненным циклом группы в хранилище удостоверений приложения.  Эти сообщения отличаются от сообщений о пользователях двумя способами:

* Запросы на получение групп указывают, что атрибут Members должен быть исключен из любого ресурса, предоставленного в ответ на запрос.  
* Запросы, которые должны определить, имеет ли ссылочный атрибут указанное значение, применяются в отношении атрибута members.  

@no__t 0Shows подготовки и отмены подготовки группы @ no__t-1<br/>
*Рис. 5. Последовательность действий при подготовке и отзыве группы*

### <a name="scim-protocol-requests-and-responses"></a>Запросы и ответы протокола SCIM
В этом разделе приведены примеры запросов SCIM, созданных клиентом Azure AD SCIM, и ожидаемых ответов. Для получения наилучших результатов следует закодировать приложение для обработки этих запросов в этом формате и выдать ожидаемые ответы.

> [!IMPORTANT]
> Сведения о том, как и когда служба подготовки пользователей Azure AD выдает операции, описанные ниже, см. в разделе [что происходит во время подготовки пользователей?](user-provisioning.md#what-happens-during-provisioning).

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
  - [Получить пользователя по запросу — 0 результатов](#get-user-by-query---zero-results)
    - [Запрос](#request-3)
    - [Ответ](#response-3)
  - [Обновление пользователя [многозначные свойства]](#update-user-multi-valued-properties)
    - [Запрос](#request-4)
    - [Ответ](#response-4)
  - [Обновление пользователя [свойства с одним значением]](#update-user-single-valued-properties)
    - [Запрос](#request-5)
    - [Ответ](#response-5)
  - [Удаление пользователя](#delete-user)
    - [Запрос](#request-6)
    - [Ответ](#response-6)
- [Операции с группами](#group-operations)
  - [Создать группу](#create-group)
    - [Запрос](#request-7)
    - [Ответ](#response-7)
  - [Получить группу](#get-group)
    - [Запрос](#request-8)
    - [Ответ](#response-8)
  - [Получить Group By displayName](#get-group-by-displayname)
    - [Запрос](#request-9)
    - [Ответ](#response-9)
  - [Update Group [атрибуты, не являющиеся членами]](#update-group-non-member-attributes)
    - [Запрос](#request-10)
    - [Ответ](#response-10)
  - [Обновить группу [добавить участников]](#update-group-add-members)
    - [Запрос](#request-11)
    - [Ответ](#response-11)
  - [Обновление группы [удаление участников]](#update-group-remove-members)
    - [Запрос](#request-12)
    - [Ответ](#response-12)
  - [Удалить группу](#delete-group)
    - [Запрос](#request-13)
    - [Ответ](#response-13)

### <a name="user-operations"></a>Операции пользователя

* Пользователи могут запрашиваться с помощью атрибутов `userName` или `email[type eq "work"]`.  

#### <a name="create-user"></a>Создать пользователя

###### <a name="request"></a>Запрос

*ОПУБЛИКОВАТЬ/Users*
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

*HTTP/1.1 201 создан*
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

###### <a name="request-1"></a>Получения
*ПОЛУЧИТЬ/Users/5d48a0a8e9f04aa38008* 

###### <a name="response-1"></a>Ответ
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

##### <a name="request-2"></a>Получения

*GET /Users?filter=userName eq "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081"*

##### <a name="response-2"></a>Ответ

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

#### <a name="get-user-by-query---zero-results"></a>Получить пользователя по запросу — 0 результатов

##### <a name="request-3"></a>Получения

*ПОЛУЧИТЬ/Users? Filter = имя_пользователя EQ "несуществующий пользователь"*

##### <a name="response-3"></a>Ответ

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

#### <a name="update-user-multi-valued-properties"></a>Обновление пользователя [многозначные свойства]

##### <a name="request-4"></a>Получения

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

##### <a name="response-4"></a>Ответ

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

#### <a name="update-user-single-valued-properties"></a>Обновление пользователя [свойства с одним значением]

##### <a name="request-5"></a>Получения

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

##### <a name="response-5"></a>Ответ

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

##### <a name="request-6"></a>Получения

*Удаление/Users/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response-6"></a>Ответ

*HTTP/1.1 204 без содержимого*

### <a name="group-operations"></a>Операции с группами

* Группы всегда должны создаваться со списком пустых членов.
* К группам можно обращаться по атрибуту `displayName`.
* Обновление запроса исправления группы должно привести к возникновению в ответе *HTTP 204 без содержимого* . Не рекомендуется возвращать текст со списком всех членов.
* Не требуется поддерживать возврат всех членов группы.

#### <a name="create-group"></a>Создать группу

##### <a name="request-7"></a>Получения

*POST/Граупс HTTP/1.1*
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

##### <a name="response-7"></a>Ответ

*HTTP/1.1 201 создан*
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

##### <a name="request-8"></a>Получения

*GET/Groups/40734ae655284ad3abcc? Ексклудедаттрибутес = Members HTTP/1.1*

##### <a name="response-8"></a>Ответ
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

#### <a name="get-group-by-displayname"></a>Получить Group By displayName

##### <a name="request-9"></a>Получения
*GET/Граупс? Ексклудедаттрибутес = Members & Filter = displayName EQ "displayName" HTTP/1.1*

##### <a name="response-9"></a>Ответ

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

#### <a name="update-group-non-member-attributes"></a>Update Group [атрибуты, не являющиеся членами]

##### <a name="request-10"></a>Получения

*PATCH/Groups/fa2ce26709934589afc5 HTTP/1.1*
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

##### <a name="response-10"></a>Ответ

*HTTP/1.1 204 без содержимого*

### <a name="update-group-add-members"></a>Обновить группу [добавить участников]

##### <a name="request-11"></a>Получения

*PATCH/Groups/a99962b9f99d4c4fac67 HTTP/1.1*
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

##### <a name="response-11"></a>Ответ

*HTTP/1.1 204 без содержимого*

#### <a name="update-group-remove-members"></a>Обновление группы [удаление участников]

##### <a name="request-12"></a>Получения

*PATCH/Groups/a99962b9f99d4c4fac67 HTTP/1.1*
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

##### <a name="response-12"></a>Ответ

*HTTP/1.1 204 без содержимого*

#### <a name="delete-group"></a>Удалить группу

##### <a name="request-13"></a>Получения

*DELETE /Groups/cdb1ce18f65944079d37 HTTP/1.1*

##### <a name="response-13"></a>Ответ

*HTTP/1.1 204 без содержимого*

## <a name="building-a-scim-endpoint-using-microsoft-cli-libraries"></a>Создание конечной точки SCIM с помощью библиотек Microsoft CLI

Создав веб-службу SCIM, которая взаимодействует с Azure Active Directory, можно включить автоматическую подготовку пользователей для практически любого приложения или хранилища удостоверений.

Вот как это работает

1. Azure AD предоставляет библиотеку общей инфраструктуры (CLI) с именем Microsoft. SystemForCrossDomainIdentityManagement, которая включена в примеры кода, описанные ниже. Системные интеграторы и разработчики могут использовать эту библиотеку для создания и развертывания конечной точки веб-службы на основе SCIM, которая может подключить Azure AD к хранилищу удостоверений любого приложения.
2. Веб-служба реализует механизмы сопоставления стандартной пользовательской схемы c пользовательской схемой и протоколом, которые использует приложение. 
3. Azure AD хранит URL-адрес конечной точки как часть информации о пользовательском приложении в коллекции приложений.
4. Администратор назначает для этого приложения пользователей и группы в Azure AD. После назначения они помещаются в очередь для синхронизации с целевым приложением. Процесс синхронизации каждые 40 минут выполняет задания из этой очереди.

### <a name="code-samples"></a>Примеры кода

Чтобы упростить этот процесс, предоставляются [примеры кода](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) , которые создают конечную точку веб-службы scim и демонстрируют автоматическую подготовку. Примером является поставщик, который поддерживает файл со строками, разделенными запятыми, представляющими пользователей и группы.

**Предварительные требования**

* Visual Studio 2013 или более поздней версии
* [Пакет Azure SDK для .NET](https://azure.microsoft.com/downloads/)
* Компьютер под управлением Windows, поддерживающий платформу ASP.NET 4.5, на котором будет размещена конечная точка SCIM. Этот компьютер должен быть доступен из облака.
* [Подписка Azure с пробной или лицензированной версией Azure AD Premium.](https://azure.microsoft.com/services/active-directory/)

### <a name="getting-started"></a>Приступая к работе

Начнем с самого простого способа реализации конечной точки SCIM. Этот пример кода принимает запросы на подготовку от Azure AD и сохраняет пользователей в файл формата CSV (значения, разделенные запятыми).

#### <a name="to-create-a-sample-scim-endpoint"></a>Создание конечной точки SCIM

1. Скачайте пакет с примером кода со страницы [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)
1. Распакуйте пакет и поместите его в любую удобную папку на компьютере Windows, например C:\AzureAD-BYOA-Provisioning-Samples.
1. В этой папке запустите проект FileProvisioning\Host\FileProvisioningService.csproj в Visual Studio.
1. Выберите **инструменты** > **диспетчер пакетов NuGet** > **консоль диспетчера пакетов**и выполните следующие команды для проекта филепровисионингсервице, чтобы разрешить ссылки на решение:

   ```powershell
    Update-Package -Reinstall
   ```

1. Создайте проект FileProvisioningService.
1. Запустите в Windows приложение командной строки (с правами администратора) и с помощью команды **cd** перейдите в каталог **\AzureAD-BYOA-Provisioning-Samples\FileProvisioning\Host\bin\Debug**.
1. Выполните следующую команду, заменив `<ip-address>` на IP-адрес или доменное имя компьютера Windows:

   ```
    FileSvc.exe http://<ip-address>:9000 TargetFile.csv
   ```

1. В разделе **Параметры windows** > **сеть & параметры Интернета**выберите **Брандмауэр Windows** > **Дополнительные параметры**и создайте **правило входящего трафика** , разрешающее входящий доступ к порту 9000.
1. Если компьютер Windows находится за маршрутизатором, маршрутизатор должен быть настроен на выполнение преобразования сетевого доступа между своим портом 9000, который предоставляется в Интернете, и портом 9000 на компьютере Windows. Эта конфигурация необходима для доступа Azure AD к этой конечной точке в облаке.

#### <a name="to-register-the-sample-scim-endpoint-in-azure-ad"></a>Регистрация примера конечной точки SCIM в Azure AD

1. Войдите на [портал Azure Active Directory](https://aad.portal.azure.com). 
1. В области слева выберите **корпоративные приложения** . Отображается список всех настроенных приложений, включая приложения, добавленные из коллекции.
1. Выберите **+ новое приложение** > **все** **приложения, не**относящиеся к коллекции  > .
1. Введите имя приложения и нажмите кнопку **Добавить** , чтобы создать объект приложения. Созданный объект приложения будет представлять не только конечную точку SCIM, но и все целевое приложение, для которого вы настраиваете подготовку пользователей и единый вход.
1. На экране Управление приложениями выберите **Подготовка** на панели слева.
1. В меню **Режим подготовки к работе** выберите **Автоматический**.    
1. В поле **URL-адрес клиента** введите URL-адрес конечной точки SCIM приложения. Пример: https://api.contoso.com/scim/

1. Если конечной точке SCIM требуется токен носителя OAuth от издателя, отличного от Azure AD, скопируйте необходимый токен носителя OAuth в необязательное для заполнения поле **Секретный токен**. Если это поле оставлено пустым, Azure AD включает маркер носителя OAuth, выданный в Azure AD с каждым запросом. Приложения, использующие Azure AD в качестве поставщика удостоверений, могут проверить этот выданный Azure AD токен.
1. Выберите **проверить подключение** , чтобы Azure Active Directory попытаться подключиться к КОНЕЧНОЙ точке scim. Если попытка не удалась, выводится информация об ошибке.  

    > [!NOTE]
    > **Проверка подключения** запрашивает конечную точку SCIM для пользователя, который не существует, используя случайный идентификатор GUID в качестве свойства сопоставления, выбранного в конфигурации Azure AD. Ожидаемый правильный ответ — HTTP 200 OK с пустым сообщением ListResponse SCIM.

1. Если попытка подключения к приложению завершилась удачно, нажмите кнопку **сохранить** , чтобы сохранить учетные данные администратора.
1. В разделе **Сопоставления** доступны на выбор два набора сопоставлений атрибутов: для объектов-пользователей и для объектов групп. Выберите каждый из них, чтобы просмотреть атрибуты, которые синхронизируются из Azure Active Directory с вашим приложением. Атрибуты, выбранные как свойства **Matching**, используются для сопоставления пользователей и групп в вашем приложении для операций обновления. Чтобы зафиксировать изменения, щелкните **Сохранить**.
1. Поле **Область** в разделе **Параметры** определяет, какие пользователи или группы синхронизируются. Установите флажок **"синхронизировать только назначенные пользователи и группы** (рекомендуется) только для синхронизации пользователей и групп, назначенных на вкладке" **Пользователи и группы** ".
1. После завершения настройки задайте для параметра **состояние подготовки** значение **вкл**.
1. Нажмите кнопку **сохранить** , чтобы запустить службу подготовки Azure AD.
1. Если выполняется синхронизация только назначенных пользователей и групп (рекомендуется), обязательно перейдите на вкладку **Пользователи и группы** и назначьте пользователей или группы, которые требуется синхронизировать.

После запуска начального цикла можно выбрать **журналы аудита** на панели слева, чтобы отслеживать ход выполнения, в котором отображаются все действия, выполняемые службой подготовки в приложении. Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](check-status-user-account-provisioning.md).

Наконец, последний этап проверки примера. Откройте файл TargetFile.csv в папке \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug на компьютере Windows. Когда процесс подготовки будет запущен, в этом файле отобразятся сведения обо всех пользователях и группах, которые были назначены и подготовлены.

### <a name="development-libraries"></a>Библиотеки для разработчика

Чтобы разработать свою веб-службу, которая соответствует спецификации SCIM, ознакомьтесь с этими библиотеками Майкрософт, которые помогут ускорить процесс разработки.

* Библиотеки Common Language Infrastructure (CLI) для использования с языками, основанными на этой инфраструктуре, например C#. Одна из этих библиотек, Microsoft. SystemForCrossDomainIdentityManagement. Service, объявляет интерфейс Microsoft. SystemForCrossDomainIdentityManagement. IProvider, как показано на следующем рисунке. Разработчик, использующий эти библиотеки, может реализовать класс с этим интерфейсом и обращаться к нему как к поставщику службы. Библиотеки позволяют разработчику развернуть веб-службу, которая соответствует спецификации SCIM. Веб-служба может размещаться либо в службы IIS, либо в любой исполняемой сборке CLI. Запрос преобразовывается в вызовы методов поставщика, которые программирует разработчик для управления хранилищем удостоверений.
  
   ![Структуры Запрос, преобразованный в вызовы методов поставщика][3]
  
* [Обработчики ExpressRoute](https://expressjs.com/guide/routing.html) используются для анализа объектов запроса Node.js, которые представляют собой вызовы (как определено в спецификации SCIM), направленные к веб-службе Node.js.

### <a name="building-a-custom-scim-endpoint"></a>Создание пользовательской конечной точки SCIM

Разработчики, использующие библиотеки CLI, могут размещать свои службы в любой исполняемой сборке CLI или в службы IIS. Ниже приведен пример кода для размещения службы в исполняемой сборке по адресу http://localhost:9000: 

   ```csharp
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
   ```

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

Чтобы разместить службу в службы IIS, разработчик должен создать сборку библиотеки кода CLI с классом Startup в пространстве имен по умолчанию для сборки.  Ниже приведен пример такого класса. 

   ```csharp
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
   ```

### <a name="handling-endpoint-authentication"></a>Обработка аутентификации на конечной точке

Запросы от Azure Active Directory содержат токен носителя OAuth 2.0.   Любая служба, получающая запрос, должна проверять подлинность издателя как Azure Active Directory ожидаемого Azure Active Directory клиента для доступа к веб-службе Azure Active Directory Graph.  В токене издатель обозначается утверждением iss, например «iss»:"https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/".  В этом примере базовый адрес значения утверждения, https://sts.windows.net, определяет Azure Active Directory в качестве издателя, а сегмент относительных адресов, (cbb1a5ac-f33b-45fa-9bf5-f37db0fed422), является уникальным идентификатором Azure Active Directory клиента, для которого маркер был выдан. Аудитория для маркера будет ИДЕНТИФИКАТОРом шаблона приложения для приложения в коллекции. Идентификатор шаблона приложения для всех пользовательских приложений — 8adf8e6e-67b2-4cf2-A259-e3dc5476c621. Идентификатор шаблона приложения для каждого приложения в коллекции изменяется. Обратитесь ProvisioningFeedback@microsoft.com для получения вопросов по ИДЕНТИФИКАТОРу шаблона приложения для приложения из коллекции. Каждое приложение, зарегистрированное в одном клиенте, может получить одно и то же утверждение `iss` с запросами SCIM.

Разработчики, использующие библиотеки CLI, предоставляемые корпорацией Майкрософт для создания службы SCIM, могут проверять подлинность запросов от Azure Active Directory с помощью пакета Microsoft. Owin. Security. ActiveDirectory. для этого выполните следующие действия. 

1. Реализуйте в поставщике свойство Microsoft.SystemForCrossDomainIdentityManagement.IProvider.StartupBehavior, которое будет возвращать метод, вызываемый при каждом запуске службы. 

   ```csharp
     public override Action<Owin.IAppBuilder, System.Web.Http.HttpConfiguration.HttpConfiguration> StartupBehavior
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

1. Добавьте следующий код в этот метод, чтобы любой запрос к любой из конечных точек службы прошел проверку подлинности в соответствии с маркером, выданным Azure Active Directory для указанного клиента, для доступа к веб-службе Azure AD Graph. 

   ```csharp
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
           ValidAudience = "8adf8e6e-67b2-4cf2-a259-e3dc5476c621"
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

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>Обработка подготовки и отмены подготовки пользователей

1. Azure Active Directory запрашивает службу пользователя, у которого значение атрибута externalId совпадает со значением атрибута mailNickname пользователя Azure AD. Запрос выражается в виде HTTP-запроса (например, в данном примере), где jyoung является примером выражения mailNickname пользователя в Azure Active Directory.

    >[!NOTE]
    > Это только пример. Не все пользователи будут иметь атрибут mailNickname, а значение, которое пользователь может не быть уникальным в каталоге. Кроме того, атрибут, используемый для сопоставления (в данном случае это externalId), можно настроить в [сопоставлениях атрибутов Azure AD](customize-application-attributes.md).

   ```
    GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
    Authorization: Bearer ...
   ```
   Если служба была создана с помощью библиотек CLI, предоставляемых корпорацией Майкрософт для реализации SCIM Services, запрос преобразуется в вызов метода запроса поставщика службы.  Подпись этого метода будет выглядеть так: 
   ```csharp
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]> Query(
      Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
      string correlationIdentifier);
   ```
   Ниже приводится определение интерфейса Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters. 
   ```csharp
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

   ```
     GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
     Authorization: Bearer ...
   ```

   Если служба создана с использованием библиотек Common Language Infrastructure корпорации Майкрософт для реализации служб SCIM, то такой запрос будет преобразован в вызов метода Query поставщика службы.  Подпись этого метода будет выглядеть так: 

   ```csharp
     // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
     // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
     // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  
 
     System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]>  Query(
       Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
       string correlationIdentifier);
   ```

   Ниже приводится определение интерфейса Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters. 

   ```csharp
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

   В вышеприведенном примере запроса пользователя с определенным значением атрибута externalId в метод Query передаются следующие значения аргументов. 
   * parameters.AlternateFilters.Count: 1
   * parameters.AlternateFilters.ElementAt(0).AttributePath: "externalId"
   * parameters.AlternateFilters.ElementAt(0).ComparisonOperator: ComparisonOperator.Equals
   * parameters.AlternateFilter.ElementAt(0).ComparisonValue: "jyoung"
   * correlationIdentifier: System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin.RequestId"] 

1. Если ответ на запрос к веб-службе для пользователя со значением атрибута externalId, совпадающим со значением атрибута mailNickname пользователя, не возвращает никаких пользователей, то Azure Active Directory запрашивает у службы предоставление пользователю соответствующей в Azure Active Directory.  Ниже приведен пример такого запроса. 

   ```
    POST https://.../scim/Users HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/scim+json
    {
      "schemas":
      [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
      "externalId":"jyoung",
      "userName":"jyoung",
      "active":true,
      "addresses":null,
      "displayName":"Joy Young",
      "emails": [
        {
          "type":"work",
          "value":"jyoung@Contoso.com",
          "primary":true}],
      "meta": {
        "resourceType":"User"},
       "name":{
        "familyName":"Young",
        "givenName":"Joy"},
      "phoneNumbers":null,
      "preferredLanguage":null,
      "title":null,
      "department":null,
      "manager":null}
   ```
   Библиотеки CLI, предоставляемые корпорацией Майкрософт для реализации SCIM Services, преобразуют этот запрос в вызов метода Create поставщика службы.  Подпись метода Create будет выглядеть так: 
   ```csharp
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> Create(
      Microsoft.SystemForCrossDomainIdentityManagement.Resource resource, 
      string correlationIdentifier);
   ```
   В запросе на подготовку пользователя в аргументе resource передается экземпляр класса Microsoft.SystemForCrossDomainIdentityManagement. Core2EnterpriseUser, определенного в библиотеке Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  Если запрос на подготовку пользователя завершается успешно, реализованный в службе метод должен вернуть экземпляр класса Microsoft.SystemForCrossDomainIdentityManagement. Core2EnterpriseUser, в котором свойство Identifier имеет значение уникального идентификатора подготовленного пользователя.  

1. Для обновления пользователя, уже существующего в хранилище удостоверений с интерфейсом SCIM, Azure Active Directory запросит у службы текущее состояние этого пользователя, отправив запрос следующего вида. 
   ```
    GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
   ```
   В службе, созданной с помощью библиотек CLI, предоставляемых корпорацией Майкрософт для реализации SCIM Services, запрос преобразуется в вызов метода получения поставщика службы.  Подпись метода Retrieve будет выглядеть так: 
   ```csharp
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource and 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
    // are defined in Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> 
       Retrieve(
         Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
           parameters, 
           string correlationIdentifier);

    public interface 
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters:   
        IRetrievalParameters
        {
          Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
            ResourceIdentifier 
              { get; }
    }
    public interface Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier
    {
        string Identifier 
          { get; set; }
        string Microsoft.SystemForCrossDomainIdentityManagement.SchemaIdentifier 
          { get; set; }
    }
   ```
   В приведенном выше примере запроса на получение текущего состояния пользователя в качестве значения аргумента parameters будет передан объект со следующими значениями свойств. 
  
   * Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

1. Если необходимо обновить атрибут ссылки, Azure Active Directory запрашивает службу, чтобы определить, соответствует ли текущее значение атрибута Reference в хранилище удостоверений, которое находится перед службой, в Azure Active Каталоги. Для пользователей таким способом запрашивается только значение атрибута manager. Ниже приведен пример запроса на проверку значения атрибута manager для определенного пользователя. 

   Если служба была создана с помощью библиотек CLI, предоставляемых корпорацией Майкрософт для реализации SCIM Services, запрос преобразуется в вызов метода запроса поставщика службы. В нем передается аргумент parameters, значением которого будет объект со следующими свойствами. 
  
   * parameters.AlternateFilters.Count: 2
   * parameters.AlternateFilters.ElementAt(x).AttributePath: ID
   * parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
   * parameters.AlternateFilter.ElementAt(x).ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
   * parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
   * parameters.AlternateFilter.ElementAt(y).ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
   * parameters.RequestedAttributePaths.ElementAt(0): ID
   * parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

   Здесь значение индекса x может быть равно 0, а значение индекса y может быть равно 1, либо значение x может быть равно 1, а значение y может быть равно 0, в зависимости от порядка выражений в параметре фильтра запроса.   

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
   Библиотеки Microsoft CLI для реализации SCIM Services преобразуют запрос в вызов метода Update поставщика службы. Подпись метода Update выглядит следующим образом. 
   ```csharp
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
   ```

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

  Здесь значение индекса x может быть равно 0, а значение индекса y может быть равно 1, либо значение x может быть равно 1, а значение y может быть равно 0, в зависимости от порядка выражений в параметре фильтра запроса.   

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

   ```csharp
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

   ```csharp
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
   Если служба была создана с помощью библиотек CLI, предоставляемых корпорацией Майкрософт для реализации SCIM Services, запрос преобразуется в вызов метода Delete поставщика службы.   Подпись метода Delete будет выглядеть так: 
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

Пользовательские ресурсы идентифицируются по идентификатору схемы, `urn:ietf:params:scim:schemas:extension:enterprise:2.0:User`, включенному в спецификацию протокола: https://tools.ietf.org/html/rfc7643.  Сопоставление по умолчанию атрибутов пользователей в Azure Active Directory с атрибутами пользовательских ресурсов приведено в таблице 1.  

Ресурсы группы определяются с помощью идентификатора схемы `urn:ietf:params:scim:schemas:core:2.0:Group`. В таблице 2 показано сопоставление по умолчанию атрибутов групп в Azure Active Directory атрибутам групповых ресурсов.  

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
| objectId |id |
| postalCode |addresses[type eq "work"].postalCode |
| proxy-Addresses |emails[type eq "other"].Value |
| physical-Delivery-OfficeName |addresses[type eq "other"].Formatted |
| streetAddress |addresses[type eq "work"].streetAddress |
| surname |name.familyName |
| telephone-Number |phoneNumbers[type eq "work"].value |
| user-PrincipalName |userName |

### <a name="table-2-default-group-attribute-mapping"></a>Таблица 2. Сопоставление атрибутов групп по умолчанию

| Группа Azure Active Directory | urn: IETF: params: scim: схемы: Core: 2.0: Group |
| --- | --- |
| displayName |externalId |
| почта |emails[type eq "work"].value |
| mailNickname |displayName |
| члены |члены |
| objectId |id |
| proxyAddresses |emails[type eq "other"].Value |

## <a name="allow-ip-addresses-used-by-the-azure-ad-provisioning-service-to-make-scim-requests"></a>Разрешить IP-адреса, используемые службой подготовки Azure AD для выполнения запросов SCIM

Некоторые приложения разрешают входящий трафик в свое приложение. Чтобы служба подготовки Azure AD функционировала должным образом, необходимо разрешить использование IP-адресов. Список IP-адресов для каждого тега или региона службы см. в файле JSON — [диапазоны IP-адресов Azure и теги службы — общедоступное облако](https://www.microsoft.com/download/details.aspx?id=56519). При необходимости вы можете скачать и программировать эти IP-адреса в брандмауэре. Зарезервированные диапазоны IP-адресов для подготовки Azure AD можно найти в разделе "Азуреактиведиректоридомаинсервицес".

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
