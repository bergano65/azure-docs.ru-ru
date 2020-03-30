---
title: Разработка конечная точка SCIM для подготовки пользователей к приложениям из Azure AD
description: Система управления кросс-доменом identity management (SCIM) стандартизирует автоматическое обеспечение пользователей. Научитесь разрабатывать конечную точку SCIM, интегрировать SCIM API с Active Directory Azure и начать автоматизацию подготовки пользователей и групп в облачные приложения.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/07/2020
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0507989ec25db595a85b89f15d8ff7d056a970f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297678"
---
# <a name="build-a-scim-endpoint-and-configure-user-provisioning-with-azure-active-directory-azure-ad"></a>Создайте конечную точку SCIM и настройте настройку пользователя с помощью active-каталога Azure (Azure AD)

Как разработчик приложений, вы можете использовать API управления данными системы кросс-домена (SCIM) для автоматического обеспечения пользователей и групп между приложением и Azure AD. В этой статье описывается, как построить конечную точку SCIM и интегрироваться с службой обеспечения Azure AD. Спецификация SCIM обеспечивает общую схему подготовки пользователя. При использовании в сочетании со стандартами федерации, такими как SAML или OpenID Connect, SCIM предоставляет администраторам сквозное, основанное на стандартах решение для управления доступом.

SCIM является стандартизированным определением двух конечных точек: /пользователи конечной точки и /группы конечной точки. Для создания, обновления и удаления объектов используются общие глаголы REST, а также заранее определенная схема для общих атрибутов, таких как имя группы, имя пользователя, имя, фамилия, фамилия и электронная почта. Приложения, предлагающие SCIM 2.0 REST API, могут уменьшить или устранить боль, при меняющуюся с помощью aPI управления собственными пользователями. Например, любой совместимый клиент SCIM знает, как сделать HTTP POST объекта JSON в конечную точку /пользователей для создания новой записи пользователя. Вместо того, чтобы нуждаться в несколько ином API для тех же основных действий, приложения, соответствующие стандарту SCIM, могут мгновенно воспользоваться преимуществами уже существующих клиентов, инструментов и кода. 

![Подготовка из Azure AD в приложение с SCIM](media/use-scim-to-provision-users-and-groups/scim-provisioning-overview.png)

Стандартная схема пользовательских объектов и аДИ-покоя для управления, определенные в SCIM 2.0 (RFC [7642,](https://tools.ietf.org/html/rfc7642) [7643,](https://tools.ietf.org/html/rfc7643) [7644](https://tools.ietf.org/html/rfc7644)) позволяют поставщикам идентификационных данных и приложениям легче интегрироваться друг с другом. Разработчики приложений, создающие конечную точку SCIM, могут интегрироваться с любым клиентом, совместимым с SCIM, без необходимости выполнять пользовательские работы.

Автоматизация подготовки приложения требует создания и интеграции конечной точки SCIM с клиентом Azure AD SCIM. Выполните следующие шаги, чтобы начать подготовку пользователей и групп в ваше приложение. 
    
  * **[Шаг 1: Разработайте схему пользователя и группы.](#step-1-design-your-user-and-group-schema)** Определите объекты и атрибуты, необходимые приложению, и определите, как они отображают схему пользователя и группы, поддерживаемую реализацией Azure AD SCIM.

  * **[Шаг 2: Поймите реализацию Azure AD SCIM.](#step-2-understand-the-azure-ad-scim-implementation)** Поймите, как реализуется клиент Azure AD SCIM, и смоделируйте обработку запросов и ответы на запросы протокола SCIM.

  * **[Шаг 3: Создайте конечную точку SCIM.](#step-3-build-a-scim-endpoint)** Конечная точка должна быть совместима с SCIM 2.0 для интеграции с службой обеспечения Azure AD. В качестве опции можно использовать библиотеки общей языковой инфраструктуры (CLI) корпорации Майкрософт (CLI) и образцы кода для построения своей цели. Эти образцы только для справки и тестирования; мы рекомендуем не кодировать ваше производственное приложение, чтобы взять на себя зависимость от них.

  * **[Шаг 4: Интегрируйте конечную точку SCIM с клиентом Azure AD SCIM.](#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client)** Если ваша организация использует стороннее приложение, которое реализует профиль SCIM 2.0, который поддерживает Azure AD, вы можете сразу же начать автоматизацию как подготовки, так и деобеспечения пользователей и групп.

  * **[Шаг 5: Опубликуйте приложение в галерее приложений Azure AD.](#step-5-publish-your-application-to-the-azure-ad-application-gallery)** Упростите для клиентов открытие вашего приложения и легко настроить подготовку. 

![Шаги для интеграции конечной точки SCIM с Azure AD](media/use-scim-to-provision-users-and-groups/process.png)

## <a name="step-1-design-your-user-and-group-schema"></a>Шаг 1: Дизайн схемы пользователя и группы

Каждое приложение требует различных атрибутов для создания пользователя или группы. Начните интеграцию, идентифицируя объекты (пользователей, группы) и атрибуты (имя, менеджер, должность и т.д.), которые требуются приложению. Стандарт SCIM определяет схему управления пользователями и группами. Основная схема пользователя требует только трех атрибутов: **id** (поставщик услуг, определяемый идентификатор), **externalId** (идентификационный идентификатор клиента) и **мета** (метаданные только для чтения, поддерживаемые поставщиком услуг). Все остальные атрибуты не являются обязательными. В дополнение к основной схеме пользователя стандарт SCIM определяет расширение корпоративного пользователя и модель расширения схемы пользователя для удовлетворения потребностей приложения. Если, например, приложению требуется менеджер пользователя, можно использовать корпоративную схему пользователя для сбора менеджера пользователя и основной схемы для сбора электронной почты пользователя. Чтобы спроектировать схему, выполните ниже:
  1. Перечислите атрибуты, которые требуются приложению. Может быть полезно разбить ваши требования на атрибуты, необходимые для проверки подлинности (например, loginName и электронную почту), атрибуты, необходимые для управления жизненным циклом пользователя (например, статус / активный), и другие атрибуты, необходимые для работы вашего конкретного приложения (например, менеджер, тег).
  2. Проверьте, уже ли эти атрибуты определены в основной схеме пользователя или в схеме корпоративного пользователя. Если какие-либо атрибуты, которые вам нужны и не охватываются в основных или корпоративных схемах пользователя, необходимо определить расширение для схемы пользователя, которая охватывает атрибуты, которые вам нужны. В приведенном ниже примере мы добавили расширение для пользователя, чтобы разрешить подготовку "тег" на пользователя. Лучше всего начать только с основных и корпоративных схем пользователя и расширить дополнительные пользовательские схемы позже.  
  3. Карта атрибутов SCIM с атрибутами пользователя в Azure AD. Если один из атрибутов, определенных в конечной точке SCIM, не имеет четкого аналога в схеме пользователя Azure AD, существует большая вероятность того, что данные вообще не хранятся на объекте пользователя на большинстве арендаторов. Подумайте, может ли этот атрибут быть необязательным для создания пользователя. Если атрибут имеет решающее значение для работы приложения, направьте администратору-арендатору расширить свою схему или использовать атрибут расширения, показанный ниже для свойства "теги".

### <a name="table-1-outline-the-attributes-that-you-need"></a>Таблица 1: Наметьте необходимые атрибуты 
| Шаг 1: Определить атрибуты, необходимые приложению| Шаг 2: Требования к приложению карты к стандарту SCIM| Шаг 3: Карта атрибутов SCIM к атрибутам Azure AD|
|--|--|--|
|Loginname|userName|userPrincipalName|
|firstName|name.givenName|givenName|
|lastName|name.lastName|lastName|
|workMail|Электронные письма (тип eq "работа")|Mail|
|manager|manager|manager|
|tag|urn:ietf:params:scim:schemas:extension:2.0:CustomExtension:tag|extensionAttribute1|
|status|active|isSoftDeleted (вычисленое значение, не хранящееся на пользователе)|

Схема, определенная выше, будет представлена с помощью полезной нагрузки Json ниже. Обратите внимание, что в дополнение к атрибутам, необходимым для приложения, представление JSON включает в себя требуемые атрибуты "id", "externalId" и "meta".

```json
{
     "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User",
      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
      "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User"],
     "userName":"bjensen",
     "externalId":"bjensen",
     "name":{
       "familyName":"Jensen",
       "givenName":"Barbara"
     },
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
     "Manager": "123456"
   },
     "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:CustomAttribute:User": {
     "tag": "701984",
   },
   "meta": {
     "resourceType": "User",
     "created": "2010-01-23T04:56:22Z",
     "lastModified": "2011-05-13T04:42:34Z",
     "version": "W\/\"3694e05e9dff591\"",
     "location":
 "https://example.com/v2/Users/2819c223-7f76-453a-919d-413861904646"
   }
 ```

### <a name="table-2-default-user-attribute-mapping"></a>Таблица 2: Отображение атрибутов пользователя по умолчанию
Затем можно использовать таблицу ниже, чтобы понять, как атрибуты, необходимые приложению, могут отображаться с атрибутом в Azure AD и SCIM RFC. Можно [настроить способ](customize-application-attributes.md) отображения атрибутов между Azure AD и вашей конечной точкой SCIM. Обратите внимание, что вам не нужно поддерживать как пользователей и групп, так и все атрибуты, указанные ниже. Они являются отсылкой к тому, как атрибуты в Azure AD часто отображаются с свойствами в протоколе SCIM. 

| Пользователь Azure Active Directory | "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted |active |
|department|урна:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|
| displayName |displayName |
|employeeId|urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|
| Facsimile-TelephoneNumber |phoneNumbers[type eq "fax"].value |
| givenName |name.givenName |
| jobTitle |title |
| mail |emails[type eq "work"].value |
| mailNickname |externalId |
| manager |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager |
| mobile |phoneNumbers[type eq "mobile"].value |
| postalCode |addresses[type eq "work"].postalCode |
| proxy-Addresses |emails[type eq "other"].Value |
| physical-Delivery-OfficeName |addresses[type eq "other"].Formatted |
| streetAddress |addresses[type eq "work"].streetAddress |
| surname |name.familyName |
| telephone-Number |phoneNumbers[type eq "work"].value |
| user-PrincipalName |userName |


### <a name="table-3-default-group-attribute-mapping"></a>Таблица 3: Отображение атрибутов группы по умолчанию

| Группа Azure Active Directory | урна:ietf:params:scim:schemas:core:2.0:Group |
| --- | --- |
| displayName |displayName |
| mail |emails[type eq "work"].value |
| mailNickname |displayName |
| members |members |
| objectId |externalId |
| proxyAddresses |emails[type eq "other"].Value |

Есть несколько конечных точек, определенных в SCIM RFC. Вы можете начать работу с / Пользователь конечная точка, а затем расширить оттуда. Конечная точка /Schemas полезна при использовании пользовательских атрибутов или при частом изменении схемы. Это позволяет клиенту автоматически получить самую актуальную схему. Конечная точка /Bulk особенно полезна при поддержке групп. В приведенной ниже таблице описаны различные конечные точки, определенные в стандарте SCIM. Конечная точка /Schemas полезна при использовании пользовательских атрибутов или при частом изменении схемы. Это позволяет клиенту автоматически получить самую актуальную схему. Конечная точка /Bulk особенно полезна при поддержке групп. В приведенной ниже таблице описаны различные конечные точки, определенные в стандарте SCIM. 
 
### <a name="table-4-determine-the-endpoints-that-you-would-like-to-develop"></a>Таблица 4: Определите конечные точки, которые вы хотели бы разработать
|ENDPOINT|DESCRIPTION|
|--|--|
|/Пользователь|Выполняйте операции CRUD на объекте пользователя.|
|/Group|Выполняйте операции CRUD на групповом объекте.|
|/ServiceProviderConfig|Предоставляет подробную информацию об особенностях стандарта SCIM, которые поддерживаются, например, ресурсы, которые поддерживаются и метод проверки подлинности.|
|/РесурсТипы|Специфика метаданных о каждом ресурсе|
|/Схемы|Набор атрибутов, поддерживаемых каждым клиентом и поставщиком услуг, может варьироваться. В то время как один поставщик услуг может включать "имя", "название" и "электронные письма", в то время как другой поставщик услуг использует "имя", "название" и "phoneNumbers". Конечная точка схем позволяет обнаружить поддерживаемые атрибуты.|
|/Бульк|Массовые операции позволяют выполнять операции на большом наборе объектов ресурсов в одной операции (например, обновление членства для большой группы).|


## <a name="step-2-understand-the-azure-ad-scim-implementation"></a>Шаг 2: Поймите реализацию Azure AD SCIM
> [!IMPORTANT]
> Поведение реализации SCIM Azure AD в последний раз обновлялось 18 декабря 2018 г. Дополнительные сведения об изменениях см. в разделе, [посвященном соответствию требованиям протокола SCIM 2.0 службы подготовки пользователей Azure AD](application-provisioning-config-problem-scim-compatibility.md).

Если вы строите приложение, поддерживающее API управления пользователями SCIM 2.0, в этом разделе подробно описывается, как реализуется клиент Azure AD SCIM. Он также показывает, как моделировать обработку запроса протокола SCIM и ответы. После того как вы внедрили свою конечную точку SCIM, вы можете протестировать ее, следуя процедуре, описанной в предыдущем разделе.

В [спецификации протокола SCIM 2.0](http://www.simplecloud.info/#Specification)ваша заявка должна соответствовать этим требованиям:

* Поддерживает создание пользователей, а также дополнительно группы, в соответствии с разделом [3.3 протокола SCIM.](https://tools.ietf.org/html/rfc7644#section-3.3)  
* Поддерживает изменение пользователей или групп с запросами PATCH, в соответствии с [разделом 3.5.2 протокола SCIM.](https://tools.ietf.org/html/rfc7644#section-3.5.2)  
* Поддерживает получение известного ресурса для пользователя или группы, созданной ранее, в соответствии с [разделом 3.4.1 протокола SCIM.](https://tools.ietf.org/html/rfc7644#section-3.4.1)  
* Поддерживает запрос пользователей или групп в соответствии с [разделом 3.4.2 протокола SCIM.](https://tools.ietf.org/html/rfc7644#section-3.4.2)  По умолчанию пользователи извлекаются по `id` их и запрашиваются их `username` и `externalid`, и группы запрашиваются `displayName`.  
* Поддерживает запрос пользователя по идентификатору и менеджеру в соответствии с разделом 3.4.2 протокола SCIM.  
* Поддерживает группы запросов по идентификатору и члену, в соответствии с разделом 3.4.2 протокола SCIM.  
* Принимает токен одного носителя для проверки подлинности и авторизации Azure AD в вашем приложении.

Следуйте этим общим рекомендациям при реализации конечной точки SCIM для обеспечения совместимости с Azure AD:

* `id`является необходимым свойством для всех ресурсов. Каждый ответ, который возвращает ресурс должен гарантировать, `ListResponse` что каждый ресурс имеет это свойство, за исключением нулевого члена.
* Ответ на запрос/фильтр всегда должен `ListResponse`быть .
* Группы неявляются, но поддерживаются только в том случае, если реализация SCIM поддерживает запросы PATCH.
* Нет необходимости включать весь ресурс в ответ PATCH.
* Microsoft Azure AD использует только следующие операторы:  
    - `eq`
    - `and`
* Не требуйте соответствия структурных элементов в SCIM, `op` в частности значениям операции PATCH, как это определено в https://tools.ietf.org/html/rfc7644#section-3.5.2. Azure AD излучает значения 'op' `Replace`как `Remove` `Add`, и .
* Microsoft Azure AD делает запросы на получение случайного пользователя и группы, чтобы убедиться, что конечная точка и учетные данные являются действительными. Это также сделано как часть потока **тестового соединения** на [портале Azure.](https://portal.azure.com) 
* Атрибут, на который можно запрашивать ресурсы, должен быть установлен в качестве соответствующего атрибута на [портале Azure.](https://portal.azure.com) Для получения дополнительной информации [см.](customize-application-attributes.md)

### <a name="user-provisioning-and-deprovisioning"></a>Подготовка и деирование пользователей

На следующей иллюстрации показаны сообщения, которые Azure Active Directory отправляет в службу SCIM для управления жизненным циклом пользователя в хранилище идентификации приложения.  

![Показывает последовательность подготовки и декручения пользователя](media/use-scim-to-provision-users-and-groups/scim-figure-4.png)<br/>
*Последовательность подготовки и декручения пользователей*

### <a name="group-provisioning-and-deprovisioning"></a>Групповое обеспечение и деирование

Групповое обеспечение и деирование являются факультативными. При реализации и включении на следующей иллюстрации показаны сообщения, которые Azure AD отправляет службе SCIM для управления жизненным циклом группы в хранилище идентификации приложения.  Эти сообщения отличаются от сообщений о пользователях двумя способами:

* В запросах на получение групп указывается, что атрибут ы членов должен быть исключен из любого ресурса, предоставленного в ответ на запрос.  
* Запросы, которые должны определить, имеет ли ссылочный атрибут указанное значение, применяются в отношении атрибута members.  

![Показывает групповую последовательность подготовки и декручения](media/use-scim-to-provision-users-and-groups/scim-figure-5.png)<br/>
*Последовательность группового подготовки и декручения*

### <a name="scim-protocol-requests-and-responses"></a>Запросы и ответы на протоколы SCIM
В этом разделе приводится пример запросов SCIM, испускаемых клиентом Azure AD SCIM, и пример ожидаемых ответов. Для достижения наилучших результатов необходимо кодировать приложение для обработки этих запросов в этом формате и излучать ожидаемые ответы.

> [!IMPORTANT]
> Чтобы понять, как и когда служба предоставления услуг по предоставлению [Provisioning cycles: Initial and incremental](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) запроса пользователям Azure AD [How provisioning works](how-provisioning-works.md)излучает операции, описанные ниже, см.

[Операции пользователей](#user-operations)
  - [Создание пользователя](#create-user) [(ответ на](#response)[запрос)](#request) / 
  - [Получить пользователя](#get-user) [(ответ на](#response-1)[запрос](#request-1) / )
  - [Получить пользователя по запросу](#get-user-by-query)  / [(Ответ](#response-2)[запроса](#request-2))
  - [Получить пользователь по запросу - Ноль результатов](#get-user-by-query---zero-results) ([Ответ на](#response-3)[запрос](#request-3)
/ )
  - [Обновление Пользователя «Многоценные свойства»](#update-user-multi-valued-properties) [(Ответ на](#response-4)[запрос)](#request-4) /  
  - [Обновление Пользователя «Однозначные свойства»](#update-user-single-valued-properties) [(Ответ на](#response-5)[запрос)](#request-5)
/  
  - [Выбивайте пользователя](#disable-user) [(Ответ на](#response-14)[запрос)](#request-14) / 

  - [Удалить пользователя](#delete-user) [(ответ на](#response-6)[запрос](#request-6) / 
)


[Групповые операции](#group-operations)
  - [Создать группу](#create-group) [(Ответ на](#response-7) [запрос)](#request-7) / 
  - [Получить группу](#get-group) [(Ответ на](#response-8) [запрос](#request-8) / )
  - [Получить группу по displayName](#get-group-by-displayname) [(Ответ на](#response-9)[запрос](#request-9) / )
  - [Группа обновления «Атрибуты, не являемые членами»](#update-group-non-member-attributes) [(Ответ на](#response-10)[запрос)](#request-10) /
 
  - [Обновление группы «Добавить членов»](#update-group-add-members) [(Ответ на](#response-11) [запрос](#request-11) /
)
  - [Обновление группы «Удалить членов»](#update-group-remove-members) [(Ответ на](#response-12) [запрос](#request-12) /
)
  - [Удаленная группа](#delete-group) [(Ответ на](#response-13)[запрос)](#request-13) /


### <a name="user-operations"></a>Операции пользователей

* Пользователи могут быть `userName` `email[type eq "work"]` запрошены или атрибуты.  

#### <a name="create-user"></a>Создать пользователя

###### <a name="request"></a>Запрос

*POST /Пользователи*
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

##### <a name="response"></a>Ответ

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

###### <a name="request"></a><a name="request-1"></a>Запрос
*GET /Пользователи/5d48a0a8e9f04aa38008* 

###### <a name="response-user-found"></a><a name="response-1"></a>Ответ (Пользователь найден)
*HTTP/1.1 200 ОК*
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

###### <a name="request"></a>Запрос
*GET /Пользователи/5171a35d82074e068ce2* 

###### <a name="response-user-not-found-note-that-the-detail-is-not-required-only-status"></a>Ответ (Пользователь не найден. Обратите внимание, что деталь не требуется, только статус.)

```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:Error"
    ],
    "status": "404",
    "detail": "Resource 23B51B0E5D7AE9110A49411D@7cca31655d49f3640a494224 not found"
}
```

#### <a name="get-user-by-query"></a>Получить пользователя по запросу

##### <a name="request"></a><a name="request-2"></a>Запрос

*GET /Users?filter-userName eq "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081"*

##### <a name="response"></a><a name="response-2"></a>Ответ

*HTTP/1.1 200 ОК*
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

#### <a name="get-user-by-query---zero-results"></a>Получить пользователь по запросу - Нулевые результаты

##### <a name="request"></a><a name="request-3"></a>Запрос

*GET /Пользователи?filter-userName eq "несуществующий пользователь"*

##### <a name="response"></a><a name="response-3"></a>Ответ

*HTTP/1.1 200 ОК*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 0,
    "Resources": [],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="update-user-multi-valued-properties"></a>Обновление Пользователя «Многоценные свойства»

##### <a name="request"></a><a name="request-4"></a>Запрос

*PATCH /Пользователи/6764549bef60420686bc HTTP/1.1*
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

##### <a name="response"></a><a name="response-4"></a>Ответ

*HTTP/1.1 200 ОК*
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

#### <a name="update-user-single-valued-properties"></a>Обновление Пользователя «Однозначные свойства»

##### <a name="request"></a><a name="request-5"></a>Запрос

*PATCH /Пользователи/5171a35d82074e068ce2 HTTP/1.1*
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

##### <a name="response"></a><a name="response-5"></a>Ответ

*HTTP/1.1 200 ОК*
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

### <a name="disable-user"></a>Неуправляемый пользователь

##### <a name="request"></a><a name="request-14"></a>Запрос

*PATCH /Пользователи/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "Operations": [
        {
            "op": "Replace",
            "path": "active",
            "value": false
        }
    ],
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ]
}
```

##### <a name="response"></a><a name="response-14"></a>Ответ

```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User"
    ],
    "id": "CEC50F275D83C4530A495FCF@834d0e1e5d8235f90a495fda",
    "userName": "deanruiz@testuser.com",
    "name": {
        "familyName": "Harris",
        "givenName": "Larry"
    },
    "active": false,
    "emails": [
        {
            "value": "gloversuzanne@testuser.com",
            "type": "work",
            "primary": true
        }
    ],
    "addresses": [
        {
            "country": "ML",
            "type": "work",
            "primary": true
        }
    ],
    "meta": {
        "resourceType": "Users",
        "location": "/scim/5171a35d82074e068ce2/Users/CEC50F265D83B4530B495FCF@5171a35d82074e068ce2"
    }
}
```
#### <a name="delete-user"></a>Удаление пользователя

##### <a name="request"></a><a name="request-6"></a>Запрос

*DELETE /Пользователи/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response"></a><a name="response-6"></a>Ответ

*HTTP/1.1 204 Нет контента*

### <a name="group-operations"></a>Групповые операции

* Группы всегда должны создаваться с пустым списком участников.
* Группы могут быть `displayName` запрошены атрибутом.
* Обновление запроса группы PATCH должно дать *HTTP 204 Нет Контента* в ответе. Возвращение тела со списком всех членов не рекомендуется.
* Не обязательно поддерживать возвращение всех членов группы.

#### <a name="create-group"></a>Создание группы

##### <a name="request"></a><a name="request-7"></a>Запрос

*POST /Группы HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group", "http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/2.0/Group"],
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "displayName": "displayName",
    "meta": {
        "resourceType": "Group"
    }
}
```

##### <a name="response"></a><a name="response-7"></a>Ответ

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

#### <a name="get-group"></a>Получение группы

##### <a name="request"></a><a name="request-8"></a>Запрос

*GET /Группы/40734ae655284ad3abcc?excludedАтрибуты (http/1.1)*

##### <a name="response"></a><a name="response-8"></a>Ответ
*HTTP/1.1 200 ОК*
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

#### <a name="get-group-by-displayname"></a>Получить группу по displayName

##### <a name="request"></a><a name="request-9"></a>Запрос
*GET /Groups?excludedAttributes-members&filter-displayName eq "displayName" HTTP/1.1*

##### <a name="response"></a><a name="response-9"></a>Ответ

*HTTP/1.1 200 ОК*
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

#### <a name="update-group-non-member-attributes"></a>Группа обновления «Атрибуты, не являемые членами)

##### <a name="request"></a><a name="request-10"></a>Запрос

*PATCH /Группы/fa2ce26709934589afc5 HTTP/1.1*
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

##### <a name="response"></a><a name="response-10"></a>Ответ

*HTTP/1.1 204 Нет контента*

### <a name="update-group-add-members"></a>Обновление группы «Добавить членов»

##### <a name="request"></a><a name="request-11"></a>Запрос

*PATCH /Группы/a99962b9f99d4c4fac67 HTTP/1.1*
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

##### <a name="response"></a><a name="response-11"></a>Ответ

*HTTP/1.1 204 Нет контента*

#### <a name="update-group-remove-members"></a>Обновление группы «Удалите членов»

##### <a name="request"></a><a name="request-12"></a>Запрос

*PATCH /Группы/a99962b9f99d4c4fac67 HTTP/1.1*
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

##### <a name="response"></a><a name="response-12"></a>Ответ

*HTTP/1.1 204 Нет контента*

#### <a name="delete-group"></a>Удалить группу

##### <a name="request"></a><a name="request-13"></a>Запрос

*DELETE /Группы/cdb1ce18f655944079d37 HTTP/1.1*

##### <a name="response"></a><a name="response-13"></a>Ответ

*HTTP/1.1 204 Нет контента*

### <a name="security-requirements"></a>Требования к безопасности
**Версии протокола TLS**

Единственными приемлемыми версиями протокола TLS являются TLS 1.2 и TLS 1.3. Другие версии TLS не допускаются. Никакая версия SSL не допускается. 
- RsA ключи должны быть не менее 2048 бит.
- Клавиши ECC должны быть не менее 256 битов, генерируемых с помощью утвержденной эллиптической кривой


**Длина ключа**

Все службы должны использовать сертификаты X.509, созданные с помощью криптографических ключей достаточной длины, то есть:

**Шифр люксы**

Все службы должны быть настроены для использования следующих наборов шифров в точном порядке, указанном ниже. Обратите внимание, что если у вас есть только сертификат RSA, установленные пакеты шифров ECDSA не имеют никакого эффекта. </br>

TLS 1.2 Cipher Suites минимальная панель:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384


## <a name="step-3-build-a-scim-endpoint"></a>Шаг 3: Построить конечную точку SCIM

Теперь, когда вы разработали схему и поняли реализацию Azure AD SCIM, вы можете начать разработку своей цели SCIM. Вместо того, чтобы начинать с нуля и полностью строить реализацию самостоятельно, вы можете положиться на ряд библиотек SCIM с открытым исходным кодом, опубликованных сообществом SCIM.

[Справочный код](https://aka.ms/SCIMReferenceCode) с открытым исходным кодом .NET Core, опубликованный группой подготовки Azure AD, является одним из таких ресурсов, которые могут перейти к началу разработки. После того как вы построили свою конечную точку SCIM, вы хотите, чтобы проверить его. Вы можете использовать сбор [тестов почтальона,](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) предоставленных как часть справочного кода или запустить через образец запросов / ответов, представленных [выше](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#user-operations).  

   > [!Note]
   > Справочный код предназначен, чтобы помочь вам начать строить свою конечную точку SCIM и предоставляется "AS IS". Вклад сообщества можно только помочь построить и сохранить код.

Решение состоит из двух проектов, _Microsoft.SCIM_ и _Microsoft.SCI.WebHostSample_.

Проект _Microsoft.SCIM_ — это библиотека, которая определяет компоненты веб-службы, соответствующие спецификации SCIM. Он объявляет интерфейс _Microsoft.SCIM.IProvider_, запросы переводятся в вызовы к методам поставщика, которые будут запрограммированы для работы на магазине идентификации.

![Разбивка: запрос, переведенный на вызовы к методам поставщика](media/use-scim-to-provision-users-and-groups/scim-figure-3.png)

Проект _Microsoft.SCIM.WebHostSample_ представляет собой Visual Studio ASP.NET Core Web Application, основанный на _шаблоне Empty._ Это позволяет развертывать образец кода как автономный, размещенный в контейнерах или в информационных службах Интернета. Он также реализует интерфейс _Microsoft.SCIM.IProvider,_ сохраняя классы в памяти в качестве образца идентификации магазина.

```csharp
    public class Startup
    {
        ...
        public IMonitor MonitoringBehavior { get; set; }
        public IProvider ProviderBehavior { get; set; }

        public Startup(IWebHostEnvironment env, IConfiguration configuration)
        {
            ...
            this.MonitoringBehavior = new ConsoleMonitor();
            this.ProviderBehavior = new InMemoryProvider();
        }
        ...
```

### <a name="building-a-custom-scim-endpoint"></a>Создание пользовательской конечной точки SCIM

Служба SCIM должна иметь сертификат аутентификации HTTP и сервера, авторитет по сертификации которых является одним из следующих имен:

* CNNIC;
* Comodo;
* CyberTrust;
* DigiCert;
* GeoTrust;
* GlobalSign;
* Go Daddy;
* VeriSign;
* WoSign.

SDK .NET Core включает в себя сертификат разработки HTTPS, который может быть использован во время разработки, сертификат устанавливается как часть первого запуска. В зависимости от того, как вы запустите ASP.NET основное web-приложение, оно будет слушать другой порт:

* Microsoft.SCIM.WebHostSample:https://localhost:5001
* IIS Экспресс:https://localhost:44359/

Для получения дополнительной информации о HTTPS в ASP.NET Core используйте следующую ссылку: [Enforce HTTPS в ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/enforcing-ssl)

### <a name="handling-endpoint-authentication"></a>Обработка аутентификации на конечной точке

Запросы от Azure Active Directory содержат токен носителя OAuth 2.0. Любая служба, получающая запрос, должна аутентифицировать эмитента как активный каталог Azure для ожидаемого арендатора Active Directory Azure.

В токене эмитент идентифицируется по претензии `"iss":"https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/"`iss, например. В этом примере базовый адрес `https://sts.windows.net`значения претензии определяет Azure Active Directory в качестве эмитента, в то время как относительный сегмент адреса, _cbb1a5ac-f33b-45fa-9bf5-f37db0fed422_, является уникальным идентификатором арендатора Active Directory Azure, для которого был выдан токен.

Аудитория токена будет id шаблона приложения для приложения в галерее, каждое из приложений, зарегистрированных в одном арендаторе, может получить одну и ту же `iss` претензию с запросами SCIM. Идентификатор шаблона приложения для каждого [ProvisioningFeedback@microsoft.com](mailto:ProvisioningFeedback@microsoft.com) приложения в галерее варьируется, пожалуйста, свяжитесь с нами для вопросов, окружающих идентификатор шаблона приложения для приложения галереи. Идентификатор шаблона приложения для всех пользовательских приложений _8adf8e6e-67b2-4cf2-a259-e3dc5476c621_.

В примере кода запросы аутентифицируются с помощью пакета Microsoft.AspNetCore.Authentication.JwtBearer. Следующий код обеспечивает, что запросы на любую из конечных точек службы аутентифицируются с помощью маркера напредъявителя, выпущенного Azure Active Directory для указанного клиента:

```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            if (_env.IsDevelopment())
            {
                ...
            }
            else
            {
                services.AddAuthentication(options =>
                {
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
                })
                    .AddJwtBearer(options =>
                    {
                        options.Authority = " https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/";
                        options.Audience = "8adf8e6e-67b2-4cf2-a259-e3dc5476c621";
                        ...
                    });
            }
            ...
        }

        public void Configure(IApplicationBuilder app)
        {
            ...
            app.UseAuthentication();
            app.UseAuthorization();
            ...
       }
```

Токен предъявителя также требуется для использования предоставленных [тестов почтальона](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) и выполнения локальной отладки с помощью локального хоста. Пример кода использует ASP.NET среды Core для изменения параметров аутентификации на этапе разработки и включения в эксплуатацию токена, подписанного самостоятельно.

Для получения дополнительной информации о нескольких средах в ASP.NET Core используйте следующую ссылку: [Используйте несколько сред в ASP.NET Core](
https://docs.microsoft.com/aspnet/core/fundamentals/environments)

Следующий код обеспечивает, что запросы на любую из конечных точек службы аутентифицируются с помощью маркера носителя, подписанного пользовательским ключом:

```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            if (_env.IsDevelopment())
            {
                services.AddAuthentication(options =>
                {
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
                })
                    .AddJwtBearer(options =>
                    {
                        options.TokenValidationParameters =
                            new TokenValidationParameters
                            {
                                ValidateIssuer = false,
                                ValidateAudience = false,
                                ValidateLifetime = false,
                                ValidateIssuerSigningKey = false,
                                ValidIssuer = "Microsoft.Security.Bearer",
                                ValidAudience = "Microsoft.Security.Bearer",
                                IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes("A1B2C3D4E5F6A1B2C3D4E5F6"))
                            };
                    });
            }
        ...
```

Отправить запрос GET контроллеру Token, чтобы получить допустимый маркер носителя, метод _GenerateJSONWebToken_ отвечает за создание маркера, соответствующий параметрам, настроенным для разработки:

```csharp
        private string GenerateJSONWebToken()
        {
            // Create token key
            SymmetricSecurityKey securityKey =
                new SymmetricSecurityKey(Encoding.UTF8.GetBytes("A1B2C3D4E5F6A1B2C3D4E5F6"));
            SigningCredentials credentials =
                new SigningCredentials(securityKey, SecurityAlgorithms.HmacSha256);

            // Set token expiration
            DateTime startTime = DateTime.UtcNow;
            DateTime expiryTime = startTime.AddMinutes(120);

            // Generate the token
            JwtSecurityToken token =
                new JwtSecurityToken(
                    "Microsoft.Security.Bearer",
                    "Microsoft.Security.Bearer",
                    null,
                    notBefore: startTime,
                    expires: expiryTime,
                    signingCredentials: credentials);

            string result = new JwtSecurityTokenHandler().WriteToken(token);
            return result;
        }
```

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>Обработка подготовки и деобеспечения пользователей

***Пример 1. Запрос службы для соответствующего пользователя***

Azure Active Directory запрашивает службу пользователя, у которого значение атрибута externalId совпадает со значением атрибута mailNickname пользователя Azure AD. Запрос выражается как запрос протокола передачи Hypertext (HTTP), например, в этом примере, в котором jyoung является образцом почтового прозвища пользователя в Active Directory Azure.

>[!NOTE]
> Это только пример. Не все пользователи будут иметь атрибут mailNickname, и значение, которое пользователь имеет, может быть не уникальным в каталоге. Кроме того, атрибут, используемый для сопоставления (который в данном случае является внешнимId) настраивается в [отображении атрибутов Azure AD.](customize-application-attributes.md)

```
GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
 Authorization: Bearer ...
```

В примере кода запрос переводится в вызов к методу queryAsync поставщика услуг. Подпись этого метода будет выглядеть так: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource is defined in 
 // Microsoft.SCIM.Schemas.  
 // Microsoft.SCIM.IQueryParameters is defined in 
 // Microsoft.SCIM.Protocol.  

 Task<Resource[]> QueryAsync(IRequest<IQueryParameters> request);
```

В запросе образца для пользователя с заданным значением для атрибута externalId значения аргументов, переданных методу QueryAsync, являются:

* parameters.AlternateFilters.Count: 1
* parameters.AlternateFilters.ElementAt(0).AttributePath: "externalId"
* parameters.AlternateFilters.ElementAt(0).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(0).ComparisonValue: "jyoung"

***Пример 2. Предоставление пользователю***

Если ответ на запрос веб-службы для пользователя с внешним значением атрибута, которое соответствует значению атрибута mailNickname пользователя, не возвращает пользователей, то Azure Active Directory просит, чтобы сервис предоставить пользователю, соответствующему одному в Active Directory Azure.  Ниже приведен пример такого запроса. 

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

В примере кода запрос переводится в вызов к методу CreateAsync поставщика услуг. Подпись этого метода будет выглядеть так: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource is defined in 
 // Microsoft.SCIM.Schemas.  

 Task<Resource> CreateAsync(IRequest<Resource> request);
```

В запросе на предоставление пользователю значение аргумента ресурса является примером класса Microsoft.SCIM.Core2EnterpriseUser, определенным в библиотеке Microsoft.SCIM.Schemas.  Если запрос на предоставление пользователю удастся, то реализация метода, как ожидается, вернет экземпляр класса Microsoft.SCIM.Core2EnterpriseUser, со значением свойства идентификатора, установленного в уникальный идентификатор вновь подготовленного Пользователя.  

***Пример 3. Запрос текущего состояния пользователя*** 

Для обновления пользователя, уже существующего в хранилище удостоверений с интерфейсом SCIM, Azure Active Directory запросит у службы текущее состояние этого пользователя, отправив запрос следующего вида. 

```
 GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
 Authorization: Bearer ...
```

В примере кода запрос переводится в вызов в метод retrieveAsync поставщика услуг. Подпись этого метода будет выглядеть так: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource and 
 // Microsoft.SCIM.IResourceRetrievalParameters 
 // are defined in Microsoft.SCIM.Schemas 

 Task<Resource> RetrieveAsync(IRequest<IResourceRetrievalParameters> request);
```

В приведенном выше примере запроса на получение текущего состояния пользователя в качестве значения аргумента parameters будет передан объект со следующими значениями свойств. 
  
* Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

***Пример 4. Запрос значения эталонного атрибута, который будет обновлен*** 

Если атрибут ссылки обновляется, то Azure Active Directory запрашивает службу, чтобы определить, соответствует ли текущее значение эталонного атрибута в хранилище идентификации, с которым ссылается служба, значение этого атрибута в Azure Active Каталог. Для пользователей таким способом запрашивается только значение атрибута manager. Вот пример запроса на определение того, имеет ли атрибут менеджера объект пользователя в настоящее время определенное значение: в коде образца запрос переводится в вызов методу queryAsync поставщика услуг. В нем передается аргумент parameters, значением которого будет объект со следующими свойствами. 
  
* parameters.AlternateFilters.Count: 2
* parameters.AlternateFilters.ElementAt(x).AttributePath: "ID"
* parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(x).ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
* parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
* parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(y).ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
* parameters.RequestedAttributePaths.ElementAt(0): "ID"
* parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

Здесь значение индекса x может быть 0, а значение индекса y может быть 1, или значение x может быть 1, а значение y может быть 0, в зависимости от порядка выражений параметра запроса фильтра.   

***Пример 5. Запрос от Azure AD в службу SCIM для обновления пользователя*** 

Ниже приведен пример запроса от Azure Active Directory к службе SCIM для обновления пользователя. 

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

В примере кода запрос переводится в вызов к методу UpdateAsync поставщика услуг. Подпись этого метода будет выглядеть так: 

```csharp
 // System.Threading.Tasks.Tasks and 
 // System.Collections.Generic.IReadOnlyCollection<T>  // are defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in
 // Microsoft.SCIM.Service.
 // Microsoft.SCIM.IPatch, 
 // is defined in Microsoft.SCIM.Protocol. 

 Task UpdateAsync(IRequest<IPatch> request);
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

***Пример 6. Дедеусивать пользователя***

Для декидывании пользователя из магазина идентификации, возглавляемого службой SCIM, Azure AD отправляет запрос, например:

```
  DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
  Authorization: Bearer ...
```

В примере кода запрос переводится в вызов к методу DeleteAsync поставщика услуг. Подпись этого метода будет выглядеть так: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.IResourceIdentifier, 
 // is defined in Microsoft.SCIM.Protocol. 

 Task DeleteAsync(IRequest<IResourceIdentifier> request);
```

Объект, представленный в качестве значения аргумента resourceIdentifier, содержит эти значения свойств в примере запроса на дезавуацию пользователя: 

* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client"></a>Шаг 4: Интеграция вашей конечной точки SCIM с клиентом Azure AD SCIM

Azure AD может быть настроен атрантом для автоматического предоставления назначенных пользователей и групп приложениям, реализующих определенный профиль [протокола SCIM 2.0.](https://tools.ietf.org/html/rfc7644) Специфика профиля описана в [шаге 2: Понять реализацию Azure AD SCIM.](#step-2-understand-the-azure-ad-scim-implementation)

Рекомендуется связаться с поставщиком приложения или ознакомиться с документацией поставщика для получения подтверждения соответствия этим требованиям.

> [!IMPORTANT]
> Реализация Azure AD SCIM построена поверх службы обеспечения пользователей Azure AD, которая предназначена для постоянного синхронизации пользователей между Azure AD и целевым приложением, и реализует весьма определенный набор стандартных операций. Важно понимать эти поведения, чтобы понять поведение клиента Azure AD SCIM. Для получения дополнительной информации см. [Provisioning cycles: Initial and incremental](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) [How provisioning works](how-provisioning-works.md)

### <a name="getting-started"></a>Начало работы

Описанные ранее в этой статье приложения, поддерживающие профиль SCIM, можно подключать к Azure Active Directory с помощью функции "Приложение не из коллекции", доступной в коллекции приложений Azure AD. После подключения Azure AD запускает процесс синхронизации каждые 40 минут, запрашивая конечную точку SCIM приложения для получения назначенных пользователей и групп, и создает или изменяет их согласно сведениям о назначении.

**Подключение приложения, поддерживающего SCIM:**

1. Вопием на [портале Active Directory Azure](https://aad.portal.azure.com). Обратите внимание, что вы можете получить доступ к бесплатной пробной версии для Active Directory Azure с лицензиями P2, подписавшись на [программу разработчика](https://developer.microsoft.com/office/dev-program)
2. Выберите **приложения Enterprise** из левого стекла. Отображается список всех настроенных приложений, включая приложения, добавленные из галереи.
3. Выберите **новое приложение** > **Все** > **негалерейное приложение**.
4. Введите имя для приложения и **выберите Добавить** для создания объекта приложения. Новое приложение добавляется в список корпоративных приложений и открывается на экран управления приложениями.

   ![Скриншот показывает галерею приложений Azure AD](media/use-scim-to-provision-users-and-groups/scim-figure-2a.png)<br/>
   *Галерея приложений Azure AD*

5. На экране управления приложением выберите **«Обеспечение** в левой панели».
6. В меню **Режим подготовки к работе** выберите **Автоматический**.

   ![Пример: Страница подготовки приложения на портале Azure](media/use-scim-to-provision-users-and-groups/scim-figure-2b.png)<br/>
   *Настройка подготовки на портале Azure*

7. В поле **URL-адрес клиента** введите URL-адрес конечной точки SCIM приложения. Например, `https://api.contoso.com/scim/`.
8. Если конечной точке SCIM требуется токен носителя OAuth от издателя, отличного от Azure AD, скопируйте необходимый токен носителя OAuth в необязательное для заполнения поле **Секретный токен**. Если это поле остается пустым, Azure AD включает маркер носителя OAuth, выпущенный из Azure AD с каждым запросом. Приложения, использующие Azure AD в качестве поставщика удостоверений, могут проверить этот выданный Azure AD токен. 
   > [!NOTE]
   > ***Не*** рекомендуется оставлять это поле пустым и полагаться на маркер, генерируемый Azure AD. Эта опция доступна в первую очередь для целей тестирования.
9. Выберите **тестовое соединение,** чтобы иметь попытку активного каталога Azure подключиться к конечной точке SCIM. Если попытка не удается, отображается информация об ошибке.  

    > [!NOTE]
    > **Проверка подключения** запрашивает конечную точку SCIM для пользователя, который не существует, используя случайный идентификатор GUID в качестве свойства сопоставления, выбранного в конфигурации Azure AD. Ожидаемый правильный ответ — HTTP 200 OK с пустым сообщением ListResponse SCIM.

10. Если попытки подключения к приложению увенчались успехом, выберите **Сохранить,** чтобы сохранить учетные данные админ.
11. В разделе **Картпинги** есть два избранных набора [отображений атрибутов:](customize-application-attributes.md)один для объектов пользователя и один для групповых объектов. Выберите каждый из них, чтобы просмотреть атрибуты, которые синхронизируются из Azure Active Directory с вашим приложением. Атрибуты, выбранные как свойства **Matching**, используются для сопоставления пользователей и групп в вашем приложении для операций обновления. Чтобы зафиксировать изменения, щелкните **Сохранить**.

    > [!NOTE]
    > При необходимости можно отключить синхронизацию объектов групп, отключив сопоставление групп.

12. Поле **Область** в разделе **Параметры** определяет, какие пользователи и группы синхронизируются. Выберите **Sync только назначенные пользователи и группы** (рекомендуется) только для синхронизации пользователей и групп, назначенных во вкладке **пользователей и групп.**
13. Как только конфигурация будет завершена, установите **статус подготовки** **в.**
14. Выберите **Сохранить,** чтобы запустить службу обеспечения Azure AD.
15. При синхронизации только назначенных пользователей и групп (рекомендуется), не забудьте выбрать **вкладку пользователей и групп** и назначить пользователей или групп, которые вы хотите синхронизировать.

После начала начального цикла можно выбрать **журналы подготовки** в левой панели для мониторинга прогресса, который показывает все действия, выполненные службой обеспечения вашего приложения. Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](check-status-user-account-provisioning.md).

> [!NOTE]
> Первоначальный цикл занимает больше времени, чем более поздние синхронизации, которые происходят примерно каждые 40 минут при запуске службы.

## <a name="step-5-publish-your-application-to-the-azure-ad-application-gallery"></a>Шаг 5: Опубликуйте приложение в галерее приложений Azure AD

Если вы строите приложение, которое будет использоваться более чем одним арендатором, вы можете сделать его доступным в галерее приложений Azure AD. Это упростит для организаций обнаружение приложения и настройку подготовки. Опубликовать приложение в галерее Azure AD и сделать подготовку доступной для других легко. Узнайте [здесь](../develop/howto-app-gallery-listing.md), как это сделать. Корпорация Майкрософт будет работать с вами над интеграцией вашего приложения в нашу галерею, тестированием вашей конечной точки и [выпуском бортовой документации](../saas-apps/tutorial-list.md) для клиентов. 

### <a name="gallery-onboarding-checklist"></a>Контрольный список галереи по найму
Следуйте контрольному списку ниже, чтобы убедиться, что ваше приложение находится на борту быстро и клиенты имеют плавный опыт развертывания. Информация будет собрана от вас при посадке в галерею. 
> [!div class="checklist"]
> * Поддержка пользователя [SCIM 2.0](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#step-2-understand-the-azure-ad-scim-implementation) и конечная точка группы (требуется только одна, но обе рекомендованы)
> * Поддержка не менее 25 запросов в секунду на одного арендатора (обязательно)
> * Установить инженерные и вспомогательные контакты для руководства клиентами после галереи на борту (Обязательно)
> * 3 Неистечения тестовых учетных данных для вашего приложения (обязательно)
> * Поддержка гранта кода авторизации OAuth или долгоживущего маркера, описанного ниже (Обязательно)
> * Создание инженерно-опорного пункта связи для поддержки клиентов после размещения галереи на борту (Обязательно)
> * Поддержка обновления нескольких членов группы с одним PATCH (Рекомендуется) 
> * Документируйте свою конечную точку SCIM публично (Рекомендуется) 
> * [Обнаружение схемы поддержки](https://tools.ietf.org/html/rfc7643#section-6) (Рекомендуется)


### <a name="authorization-for-provisioning-connectors-in-the-application-gallery"></a>Разрешение на подготовку разъемов в галерее приложений
Спецификация SCIM не определяет специальную схему SCIM для проверки подлинности и авторизации. Она опирается на использование существующих отраслевых стандартов. Клиент-разработчик Azure AD поддерживает два метода авторизации приложений в галерее. 

|Метод авторизации|Плюсы|Минусы|Поддержка|
|--|--|--|--|
|Имя пользователя и пароль (не рекомендуется или не поддерживается Azure AD)|Легко реализовать|Небезопасный - [Ваш Pa$$word не имеет значения](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)|Поддерживается на индивидуальной основе для галерейных приложений. Не поддерживается для негалерей приложений.|
|Долгоживущий токен носителя|Долгосрочные токены не требуют присутствия пользователя. Они просты для админинов в использовании при настройке подготовки.|Долгоживущие токены может быть трудно поделиться с админ без использования небезопасных методов, таких как электронная почта. |Поддерживается для галерейных и негалерее приложений. |
|Грант кода авторизации OAuth|Токены доступа гораздо короче, чем пароли, и имеют автоматизированный механизм обновления, которого нет у долгоживущих токенов носителя.  Реальный пользователь должен присутствовать во время первоначальной авторизации, добавляя уровень подотчетности. |Требуется, чтобы пользователь присутствовал. Если пользователь покидает организацию, токен недействителен, и авторизация должна быть завершена повторно.|Поддерживается для галерейных приложений. Ведется поддержка негалерейных приложений.|
|Грант на учетные данные клиентов OAuth|Токены доступа гораздо короче, чем пароли, и имеют автоматизированный механизм обновления, которого нет у долгоживущих токенов носителя. Как грант кода авторизации, так и предоставление учетных данных клиента создают один и тот же тип токена доступа, поэтому перемещение между этими методами является прозрачным для API.  Подготовка может быть полностью автоматизирована, а новые токены можно запрашивать без взаимодействия с пользователем. ||Не поддерживается для галерейных и негалерейных приложений. Поддержка находится в нашем невыполненном заработке.|

[!NOTE] Не рекомендуется оставлять поле маркера пустым в пользовательском пользовательском пользовательском пользовательском пользовательском пользовательском пользовательском пользовательском пользовательском пользовательском пользовательском пользовательском пользовательском пользовательском пользовательском пользовательском пользовательском пользовательском пользовательском пользовательском пользовательском пользовательском пользовательском пользовательском пользовательском пользовательском пользовательском пользовательском пользовательском пользовательском пользовательском пользовательском пользовательском пользовательском пользовательском пользовательском за Созданный токен в основном доступен для целей тестирования.

**Поток гранта кода авторизации OAuth:** Служба предоставления поддерживает [грант кода авторизации.](https://tools.ietf.org/html/rfc6749#page-24) После отправки запроса на публикацию приложения в галерее наша команда будет работать с вами, чтобы собрать следующую информацию:
*  URL-адрес авторизации: URL-адрес клиента для получения разрешения от владельца ресурса через перенаправление агента пользователя. Пользователь перенаправляется на этот URL для авторизации доступа. 
*  URL-адрес обмена токенов: URL-адрес клиента для обмена разрешения на токен доступа, как правило, с проверкой подлинности клиента.
*  Идентификатор клиента: Сервер авторизации выдает зарегистрированному клиенту идентификатор клиента, который представляет собой уникальную строку, представляющую регистрационную информацию, предоставленную клиентом.  Идентификация клиента не является секретом; он подвергается воздействию владельца ресурса и **не должен** использоваться только для проверки подлинности клиента.  
*  Секрет клиента: Секрет клиента является секретом, генерируемым сервером авторизации. Это должно быть уникальное значение, известное только серверу авторизации. 

Обратите внимание, что OAuth v1 не поддерживается из-за разоблачения секрета клиента. OAuth v2 поддерживается.  

Рекомендации (рекомендуется, но не требуется):
* Поддержка нескольких перенаправить URL-адреса. Администраторы могут настроить подготовку как из "portal.azure.com", так и из "aad.portal.azure.com". Поддержка нескольких URL-адресов перенаправления гарантирует, что пользователи могут авторизовать доступ с любого портала.
* Поддержка нескольких секретов, чтобы обеспечить плавное обновление секрета, без простоев. 

**Долгоживущие токены-носители OAuth:** Если приложение не поддерживает поток грантов кода авторизации OAuth, можно также создать токен, который администратор может использовать для настройки интеграции подготовки. Токен должен быть вечным, иначе задание подготовки будет [помещено на карантин](application-provisioning-quarantine-status.md) по истечении срока действия токена. Этот маркер должен быть ниже 1KB в размере.  

Для получения дополнительных методов аутентификации и авторизации сообщите нам об этом в [UserVoice.](https://aka.ms/appprovisioningfeaturerequest)

### <a name="gallery-go-to-market-launch-check-list"></a>Галерея выйти на рынок запуска контрольный список
Чтобы повысить осведомленность и спрос на нашу совместную интеграцию, мы рекомендуем вам обновить существующую документацию и усилить интеграцию в ваши маркетинговые каналы.  Ниже приведен набор контрольных мероприятий, которые мы рекомендуем вам завершить для поддержки запуска

* **Готовность продаж и поддержки клиентов.** Убедитесь, что ваши отделы продаж и поддержки осведомлены о возможностях интеграции и могут говорить с ними. Кратко ваши продажи и поддержки команды, предоставить им часто задаваемые вопросы и включить интеграцию в свои материалы продаж. 
* **Сообщение в блоге и/или пресс-релиз.** Создайте сообщение в блоге или пресс-релиз, который описывает совместную интеграцию, преимущества и как начать работу. [Пример: Пресс-релиз Imprivata и Azure Active Directory](https://www.imprivata.com/company/press/imprivata-introduces-iam-cloud-platform-healthcare-supported-microsoft) 
* **Социальные сети.** Используйте свои социальные сети, такие как Twitter, Facebook или LinkedIn, чтобы содействовать интеграции ваших клиентов. Обязательно включите, @AzureAD чтобы мы могли ретвитнуть ваш пост. [Пример: Imprivata Щебетать пост](https://twitter.com/azuread/status/1123964502909779968)
* **Маркетинговый сайт.** Создавайте или обновляйте свои маркетинговые страницы (например, страницу интеграции, страницу партнера, страницу ценообразования и т.д.), чтобы включить доступность совместной интеграции. [Пример: Страница интеграции Pingboard,](https://pingboard.com/org-chart-for) [страница интеграции Smartsheet,](https://www.smartsheet.com/marketplace/apps/microsoft-azure-ad) [страница Monday.com цен](https://monday.com/pricing/) 
* **Техническая документация.** Создайте статью справочного центра или техническую документацию о том, как клиенты могут начать работу. [Пример: Envoy - интеграция активного каталога Microsoft Azure.](https://envoy.help/en/articles/3453335-microsoft-azure-active-directory-integration/
) 
* **Общение с клиентами.** Оповещение клиентов о новой интеграции через вашу коммуникацию с клиентами (ежемесячные информационные бюллетени, кампании по электронной почте, примечания к выпуску продукта). 

### <a name="allow-ip-addresses-used-by-the-azure-ad-provisioning-service-to-make-scim-requests"></a>Разрешить IP-адреса, используемые службой предоставления AD Azure AD, делать запросы SCIM

Некоторые приложения позволяют входящий трафик в их приложение. Для того, чтобы служба обеспечения Azure AD функционировала должным образом, необходимо разрешить использовать IP-адреса. Список IP-адресов для каждого тега или региона службы см. в файле JSON [Azure IP Ranges and Service Tags – Public Cloud](https://www.microsoft.com/download/details.aspx?id=56519) (Диапазоны IP-адресов и теги служб Azure — общедоступное облако). Вы можете загрузить и запрограммировать эти IPs в брандмауэр по мере необходимости. Зарезервированные диапазоны IP для подготовки Azure AD можно найти в рамках "AzureActiveDirectoryDomainServices".

## <a name="related-articles"></a>Связанные статьи

* [Автоматизация подготовки пользователей и деобеспечения приложений SaaS](user-provisioning.md)
* [Настройка сопоставлений атрибутов для подготовки пользователей для приложений SaaS в Azure Active Directory](customize-application-attributes.md)
* [Написание выражений для отображения атрибутов](functions-for-customizing-application-data.md)
* [Фильтры для подготовки пользователей](define-conditional-rules-for-provisioning-user-accounts.md)
* [Уведомления о подготовке учетных записей](user-provisioning.md)
* [Список учебников по интеграции приложений SaaS](../saas-apps/tutorial-list.md)
