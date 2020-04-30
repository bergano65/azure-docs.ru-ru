---
title: Разработка конечной точки SCIM для подготовки пользователей к приложениям из Azure AD
description: Система для управления междоменными удостоверениями (SCIM) стандартизации автоматической подготовки пользователей. Научитесь разрабатывать конечную точку SCIM, интегрировать API SCIM с Azure Active Directory и начать автоматизировать подготовку пользователей и групп в облачных приложениях.
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
ms.openlocfilehash: a54bc3cfa67330fb0056ccd1898d9ab3de2b0ab2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82229924"
---
# <a name="build-a-scim-endpoint-and-configure-user-provisioning-with-azure-active-directory-azure-ad"></a>Создание конечной точки SCIM и Настройка подготовки пользователей с помощью Azure Active Directory (Azure AD)

Как разработчик приложения вы можете использовать систему для API управления пользовательским удостоверением (SCIM), чтобы включить автоматическую подготовку пользователей и групп между приложением и Azure AD. В этой статье описывается, как создать конечную точку SCIM и интегрировать ее со службой подготовки Azure AD. Спецификация SCIM предоставляет общую схему пользователя для подготовки. При использовании в сочетании со стандартами Федерации, такими как SAML или OpenID Connect Connect, SCIM предоставляет администраторам комплексное решение на основе стандартов для управления доступом.

SCIM — это стандартизированное определение двух конечных точек: конечная точка/Users и конечная точка/Граупс. Он использует общие команды RESTFUL для создания, обновления и удаления объектов, а также предварительно определенную схему для общих атрибутов, таких как имя группы, имя пользователя, имя, фамилия и адрес электронной почты. Приложения, которые предлагают SCIM 2,0 REST API, могут сократить или устранить трудности при работе с пользовательским API управления пользователями. Например, любой совместимый клиент SCIM знает, как выполнить HTTP-запись объекта JSON в конечную точку/Users для создания новой записи пользователя. Вместо того чтобы использовать несколько других API для одних и тех же основных действий, приложения, которые соответствуют стандарту SCIM, могут мгновенно воспользоваться преимуществами существующих клиентов, средств и кода. 

![Подготовка из Azure AD в приложение с помощью SCIM](media/use-scim-to-provision-users-and-groups/scim-provisioning-overview.png)

Схема объектов стандартного пользователя и интерфейсы API для управления, определенные в SCIM 2,0 (RFC [7642](https://tools.ietf.org/html/rfc7642), [7643](https://tools.ietf.org/html/rfc7643), [7644](https://tools.ietf.org/html/rfc7644)), позволяют более легко интегрировать поставщики удостоверений и приложения. Разработчики приложений, которые создают конечную точку SCIM, могут интегрироваться с любым клиентом, совместимым с SCIM, без необходимости выполнять пользовательскую работу.

Для автоматизации подготовки приложения требуется создание и интеграция конечной точки SCIM с клиентом Azure AD SCIM. Выполните следующие действия, чтобы начать подготовку пользователей и групп в приложении. 
    
  * **[Шаг 1. Проектирование схемы пользователя и группы.](#step-1-design-your-user-and-group-schema)** Определите объекты и атрибуты, необходимые для приложения, и определите, как они сопоставляются с схемой пользователя и группы, поддерживаемой реализацией Azure AD SCIM.

  * **[Шаг 2. изучение реализации Azure AD SCIM.](#step-2-understand-the-azure-ad-scim-implementation)** Ознакомьтесь с реализацией клиента Azure AD SCIM и моделированием обработки запросов и ответов протокола SCIM.

  * **[Шаг 3. Создание конечной точки SCIM.](#step-3-build-a-scim-endpoint)** Для интеграции со службой подготовки Azure AD конечная точка должна быть совместима с SCIM 2,0. В качестве варианта можно использовать библиотеки Microsoft Common Language Infrastructure (CLI) и примеры кода для создания конечной точки. Эти примеры предназначены только для справки и тестирования. Мы советуем программировать приложение, чтобы получить зависимость от них.

  * **[Шаг 4. Интеграция конечной точки SCIM с клиентом Azure AD SCIM.](#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client)** Если ваша организация использует стороннее приложение, которое реализует профиль SCIM 2,0, поддерживаемый Azure AD, вы можете сразу же начать автоматизировать подготовку и отмену подготовки пользователей и групп.

  * **[Шаг 5. Опубликуйте приложение в коллекции приложений Azure AD.](#step-5-publish-your-application-to-the-azure-ad-application-gallery)** Упростите клиентам Поиск приложения и легко настраивайте подготовку. 

![Действия по интеграции конечной точки SCIM с Azure AD](media/use-scim-to-provision-users-and-groups/process.png)

## <a name="step-1-design-your-user-and-group-schema"></a>Шаг 1. Проектирование схемы пользователя и группы

Для создания пользователя или группы в каждом приложении требуются разные атрибуты. Начните интеграцию, определив объекты (пользователей, группы) и атрибуты (имя, руководителя, должность и т. д.), которые требуются вашему приложению. Стандарт SCIM определяет схему для управления пользователями и группами. Основная схема пользователя требует только трех атрибутов: **ID** (определенный идентификатор поставщика службы), **externalId** (идентификатор, определенный клиентом) и **meta** (метаданные только для чтения, поддерживаемые поставщиком услуг). Все остальные атрибуты являются необязательными. Помимо основной схемы пользователя, стандарт SCIM определяет расширение пользователя предприятия и модель для расширения схемы пользователя в соответствии с потребностями вашего приложения. Если, например, приложению требуется менеджер пользователя, можно использовать схему "пользователь предприятия" для получения сведений о руководителе пользователя и основной схеме для получения электронной почты пользователя. Чтобы создать схему, выполните следующие действия:
  1. Список атрибутов, которые требуются вашему приложению. Можно приостановить требования к атрибутам, необходимым для проверки подлинности (например, loginName и электронной почте), атрибутам, необходимым для управления жизненным циклом пользователя (например, Status/Active), и другими атрибутами, необходимыми для работы конкретного приложения (например, руководителя, тега).
  2. Проверьте, определены ли эти атрибуты в схеме основного пользователя или в схеме пользователей предприятия. Если какие бы то ни было необходимые атрибуты и не включены в схемы ядра или пользователя предприятия, необходимо определить расширение для пользовательской схемы, охватывающей необходимые атрибуты. В приведенном ниже примере мы добавили расширение для пользователя, чтобы разрешить подготовку "тега" для пользователя. Лучше всего начать с основных схем и схемы пользователей предприятия и развернуть дополнительные пользовательские схемы позже.  
  3. Сопоставьте атрибуты SCIM с атрибутами пользователя в Azure AD. Если один из атрибутов, определенных в конечной точке SCIM, не имеет четкого аналога со схемой пользователя Azure AD, то существует хороший шанс, что данные не будут храниться в объекте пользователя вообще в большинстве клиентов. Определите, может ли этот атрибут быть необязательным для создания пользователя. Если атрибут является критически важным для работы приложения, попросите администратора клиента расширить свою схему или использовать атрибут расширения, как показано ниже для свойства "Tags".

### <a name="table-1-outline-the-attributes-that-you-need"></a>Таблица 1. структурирование необходимых атрибутов 
| Шаг 1. Определение атрибутов, необходимых для приложения| Шаг 2. Сопоставьте требования приложения с SCIM Standard| Шаг 3. Сопоставьте атрибуты SCIM с атрибутами Azure AD|
|--|--|--|
|loginName|userName|userPrincipalName|
|firstName|name.givenName|givenName|
|lastName|Name. lastName|lastName|
|воркмаил|Сообщения электронной почты [Type EQ "Рабочая"]. Value|Mail|
|manager|manager|manager|
|tag|urn: IETF: params: scim: схемы: расширение: 2.0: Кустомекстенсион: TAG|extensionAttribute1|
|status|active|Иссофтделетед (вычисленное значение не хранится на пользователе)|

Схема, определенная выше, будет представлена с помощью полезных данных JSON ниже. Обратите внимание, что в дополнение к атрибутам, необходимым для приложения, представление JSON содержит обязательные атрибуты "ID", "externalId" и "META".

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

### <a name="table-2-default-user-attribute-mapping"></a>Таблица 2. Сопоставление пользовательских атрибутов по умолчанию
Затем вы можете использовать приведенную ниже таблицу, чтобы понять, как атрибуты, необходимые вашему приложению, можно сопоставлять с атрибутом в Azure AD и RFC SCIM. Вы можете [настроить](customize-application-attributes.md) сопоставление атрибутов между Azure AD и КОНЕЧНОЙ точкой scim. Обратите внимание, что не требуется поддержка как пользователей, так и групп или всех атрибутов, показанных ниже. Они представляют собой справочные сведения о том, как атрибуты в Azure AD часто сопоставляются со свойствами в протоколе SCIM. 

| Пользователь Azure Active Directory | "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted |active |
|department|urn: IETF: params: scim: схемы: расширение: Enterprise: 2.0: пользователь: Отдел|
| displayName |displayName |
|employeeId|urn: IETF: params: scim: схемы: расширение: Enterprise: 2.0: пользователь: Емплойинумбер|
| Facsimile-TelephoneNumber |phoneNumbers[type eq "fax"].value |
| givenName |name.givenName |
| jobTitle |title |
| почта |emails[type eq "work"].value |
| mailNickname |externalId |
| manager |urn: IETF: params: scim: схемы: расширение: Enterprise: 2.0: пользователь: менеджер |
| mobile |phoneNumbers[type eq "mobile"].value |
| postalCode |addresses[type eq "work"].postalCode |
| proxy-Addresses |emails[type eq "other"].Value |
| physical-Delivery-OfficeName |addresses[type eq "other"].Formatted |
| streetAddress |addresses[type eq "work"].streetAddress |
| surname |name.familyName |
| telephone-Number |phoneNumbers[type eq "work"].value |
| user-PrincipalName |userName |


### <a name="table-3-default-group-attribute-mapping"></a>Таблица 3. сопоставление атрибутов групп по умолчанию

| Группа Azure Active Directory | urn: IETF: params: scim: схемы: Core: 2.0: Group |
| --- | --- |
| displayName |displayName |
| почта |emails[type eq "work"].value |
| mailNickname |displayName |
| members |members |
| objectId |externalId |
| proxyAddresses |emails[type eq "other"].Value |

В SCIM RFC определено несколько конечных точек. Можно приступить к работе с конечной точкой/User, а затем развернуть отсюда. Конечная точка/Счемас полезна при использовании настраиваемых атрибутов или при частом изменении схемы. Он позволяет клиенту автоматически получать самую последнюю схему. Конечная точка/Булк особенно полезна при поддержке групп. В следующей таблице описаны различные конечные точки, определенные в стандарте SCIM. Конечная точка/Счемас полезна при использовании настраиваемых атрибутов или при частом изменении схемы. Он позволяет клиенту автоматически получать самую последнюю версию схемы. Конечная точка/Булк особенно полезна при поддержке групп. В следующей таблице описаны различные конечные точки, определенные в стандарте SCIM. 
 
### <a name="table-4-determine-the-endpoints-that-you-would-like-to-develop"></a>Таблица 4. Определение конечных точек, которые вы хотите разрабатывать
|ENDPOINT|DESCRIPTION|
|--|--|
|WMIC|Выполнение операций CRUD над объектом пользователя.|
|/Group|Выполнение операций CRUD над объектом группы.|
|/сервицепровидерконфиг|Содержит сведения о поддерживаемых функциях стандарта SCIM, например поддерживаемых ресурсах и методе проверки подлинности.|
|/ресаурцетипес|Указывает метаданные о каждом ресурсе|
|/счемас|Набор атрибутов, поддерживаемых каждым клиентом и поставщиком служб, может различаться. Хотя один поставщик услуг может включать "Name", "Title" и "emails", в то время как другой поставщик услуг использует "Name", "Title" и "Фоненумберс". Конечная точка схемы позволяет обнаруживать поддерживаемые атрибуты.|
|/булк|Групповые операции позволяют выполнять операции над большой коллекцией объектов ресурсов за одну операцию (например, обновление членства для большой группы).|


## <a name="step-2-understand-the-azure-ad-scim-implementation"></a>Шаг 2. изучение реализации Azure AD SCIM
> [!IMPORTANT]
> Поведение реализации SCIM Azure AD в последний раз обновлялось 18 декабря 2018 г. Дополнительные сведения об изменениях см. в разделе, [посвященном соответствию требованиям протокола SCIM 2.0 службы подготовки пользователей Azure AD](application-provisioning-config-problem-scim-compatibility.md).

Если вы создаете приложение, которое поддерживает API управления пользователями SCIM 2,0, в этом разделе подробно описано, как реализуется клиент Azure AD SCIM. Здесь также показано, как моделировать обработку запросов и ответов протокола SCIM. После реализации конечной точки SCIM ее можно протестировать, выполнив процедуру, описанную в предыдущем разделе.

В [спецификации протокола SCIM 2,0](http://www.simplecloud.info/#Specification)приложение должно соответствовать следующим требованиям:

* Поддерживает создание пользователей и, при необходимости, групп, в разделе [3,3 протокола scim](https://tools.ietf.org/html/rfc7644#section-3.3).  
* Поддерживает изменение пользователей или групп с запросами PATCH в соответствии с [разделом 3.5.2 протокола scim](https://tools.ietf.org/html/rfc7644#section-3.5.2).  
* Поддерживает получение известного ресурса для пользователя или группы, созданной ранее, в соответствии с [разделом 3.4.1 протокола scim](https://tools.ietf.org/html/rfc7644#section-3.4.1).  
* Поддерживает запросы пользователей или групп в соответствии с разделом [3.4.2 протокола scim](https://tools.ietf.org/html/rfc7644#section-3.4.2).  По умолчанию пользователи извлекаются по `id` `username` их учетным запросам `externalid`и, а группы запрашиваются по `displayName`.  
* Поддерживает запросы пользователей по ИДЕНТИФИКАТОРу и руководителю в соответствии с разделом 3.4.2 протокола SCIM.  
* Поддерживает запросы групп по ИДЕНТИФИКАТОРу и по члену, как в разделе 3.4.2 протокола SCIM.  
* Принимает один токен носителя для проверки подлинности и авторизации Azure AD в приложении.

При реализации конечной точки SCIM для обеспечения совместимости с Azure AD следуйте приведенным ниже общим рекомендациям.

* `id`является обязательным свойством для всех ресурсов. Каждый ответ, возвращающий ресурс, должен гарантировать, что каждый ресурс имеет это `ListResponse` свойство, за исключением элементов с нулевым числом членов.
* Ответ на запрос запроса или фильтра всегда должен иметь значение `ListResponse`.
* Группы являются необязательными, но поддерживаются только в том случае, если реализация SCIM поддерживает запросы на исправление.
* Не обязательно включать весь ресурс в ответ на исправление.
* В Microsoft Azure AD используются только следующие операторы:  
    - `eq`
    - `and`
* Не требуйте совпадения с учетом регистра для структурных элементов в SCIM в определенных `op` значениях операции исправления, как https://tools.ietf.org/html/rfc7644#section-3.5.2определено в. Azure AD выдает значения "op" как `Add`, `Replace`и. `Remove`
* Microsoft Azure AD выполняет запросы на выбор случайного пользователя и группы, чтобы убедиться, что конечная точка и учетные данные действительны. Это также делается как часть **тестового потока подключения** в [портал Azure](https://portal.azure.com). 
* Атрибут, на котором могут запрашиваться ресурсы, должен быть установлен в качестве совпадающего атрибута для приложения в [портал Azure](https://portal.azure.com). Дополнительные сведения см. в разделе [Настройка сопоставлений атрибутов подготовки пользователей](customize-application-attributes.md) .

### <a name="user-provisioning-and-deprovisioning"></a>Подготовка и отменяющая подготовка пользователей

На следующем рисунке показаны сообщения, которые Azure Active Directory отправляется в службу SCIM для управления жизненным циклом пользователя в хранилище удостоверений приложения.  

![Отображает последовательность подготовки и отмены подготовки пользователей.](media/use-scim-to-provision-users-and-groups/scim-figure-4.png)<br/>
*Последовательность подготовки и отмены подготовки пользователей*

### <a name="group-provisioning-and-deprovisioning"></a>Подготовка и деподготовка групп

Подготовка и деподготовка групп являются необязательными. При реализации и включении на следующем рисунке показаны сообщения, которые Azure AD отправляет в службу SCIM для управления жизненным циклом группы в хранилище удостоверений приложения.  Эти сообщения отличаются от сообщений о пользователях двумя способами:

* Запросы на получение групп указывают, что атрибут Members должен быть исключен из любого ресурса, предоставленного в ответ на запрос.  
* Запросы, которые должны определить, имеет ли ссылочный атрибут указанное значение, применяются в отношении атрибута members.  

![Отображение последовательности подготовки и отмены подготовки группы](media/use-scim-to-provision-users-and-groups/scim-figure-5.png)<br/>
*Последовательность подготовки и отмены подготовки группы*

### <a name="scim-protocol-requests-and-responses"></a>Запросы и ответы протокола SCIM
В этом разделе приведены примеры запросов SCIM, созданных клиентом Azure AD SCIM, и ожидаемых ответов. Для получения наилучших результатов следует закодировать приложение для обработки этих запросов в этом формате и выдать ожидаемые ответы.

> [!IMPORTANT]
> Чтобы понять, как и когда служба подготовки пользователей Azure AD выдает операции, описанные ниже, см. раздел [циклы подготовки: Начальная и добавочная](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) в разделе [Подготовка](how-provisioning-works.md)к работе.

[Операции пользователя](#user-operations)
  - [Создание пользователя](#create-user) ([запрос](#request) / [ответа](#response))
  - [Получение пользователя](#get-user) ([запрос](#request-1) / [ответа](#response-1))
  - [Получить пользователя по запросу](#get-user-by-query) ([Request](#request-2) / [ответ](#response-2)на запрос)
  - [Получить пользователя по запросу — 0 результатов](#get-user-by-query---zero-results) ([Request](#request-3)
/ [ответ](#response-3)запроса)
  - [Обновление пользователя [многозначные свойства]](#update-user-multi-valued-properties) ([Request](#request-4) /  [ответ](#response-4)на запрос)
  - [Обновление пользователя [свойства с одним значением]](#update-user-single-valued-properties) ([Request](#request-5)
/ [ответ](#response-5)на запрос) 
  - [Отключить пользователя](#disable-user) ([запрос](#request-14) / 
[ответа](#response-14))
  - [Удаление пользователя](#delete-user) ([запрос](#request-6) / 
[ответа](#response-6))


[Операции с группами](#group-operations)
  - [Создание группы](#create-group) ( [Request](#request-7) / [ответ](#response-7)на запрос)
  - [Получить группу](#get-group) ( [Request](#request-8) / [ответ](#response-8)на запрос)
  - [Получить Group By DisplayName](#get-group-by-displayname) ([Request](#request-9) / [ответ](#response-9)на запрос)
  - [Update Group [атрибуты, не являющиеся членами]](#update-group-non-member-attributes) ( [ответ](#response-10)на[запрос](#request-10) /
 )
  - [Update Group [Добавление участников]](#update-group-add-members) ( [Request](#request-11) /
[ответ](#response-11)на запрос)
  - [Обновление группы [удаление участников]](#update-group-remove-members) ( [Request](#request-12) /
[ответ](#response-12)на запрос)
  - [Удалить группу](#delete-group) ([Request](#request-13) /
[ответ](#response-13)на запрос)

### <a name="user-operations"></a>Операции пользователя

* Пользователи могут запрашиваться `userName` с `email[type eq "work"]` помощью атрибутов или.  

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

##### <a name="response"></a>Ответ

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

###### <a name="request"></a><a name="request-1"></a>Запрос
*ПОЛУЧИТЬ/Users/5d48a0a8e9f04aa38008* 

###### <a name="response-user-found"></a><a name="response-1"></a>Ответ (найден пользователь)
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
*ПОЛУЧИТЬ/Users/5171a35d82074e068ce2* 

###### <a name="response-user-not-found-note-that-the-detail-is-not-required-only-status"></a>Ответ (пользователь не найден. Обратите внимание, что данные не являются обязательными, только состояние.)

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

*ПОЛУЧИТЬ/Users? Filter = userName EQ "Test_User_dfeef4c5-5681 -4387-B016-bdf221e82081"*

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

#### <a name="get-user-by-query---zero-results"></a>Получить пользователя по запросу — 0 результатов

##### <a name="request"></a><a name="request-3"></a>Запрос

*ПОЛУЧИТЬ/Users? Filter = имя_пользователя EQ "несуществующий пользователь"*

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

#### <a name="update-user-multi-valued-properties"></a>Обновление пользователя [многозначные свойства]

##### <a name="request"></a><a name="request-4"></a>Запрос

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

#### <a name="update-user-single-valued-properties"></a>Обновление пользователя [свойства с одним значением]

##### <a name="request"></a><a name="request-5"></a>Запрос

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

### <a name="disable-user"></a>Отключить пользователя

##### <a name="request"></a><a name="request-14"></a>Запрос

*PATCH/Users/5171a35d82074e068ce2 HTTP/1.1*
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

*Удаление/Users/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response"></a><a name="response-6"></a>Ответ

*HTTP/1.1 204 без содержимого*

### <a name="group-operations"></a>Операции с группами

* Группы всегда должны создаваться со списком пустых членов.
* К `displayName` группам можно обращаться по атрибуту.
* Обновление запроса исправления группы должно привести к возникновению в ответе *HTTP 204 без содержимого* . Не рекомендуется возвращать текст со списком всех членов.
* Не требуется поддерживать возврат всех членов группы.

#### <a name="create-group"></a>Создание группы

##### <a name="request"></a><a name="request-7"></a>Запрос

*POST/Граупс HTTP/1.1*
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

#### <a name="get-group"></a>Получение группы

##### <a name="request"></a><a name="request-8"></a>Запрос

*GET/Groups/40734ae655284ad3abcc? Ексклудедаттрибутес = Members HTTP/1.1*

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

#### <a name="get-group-by-displayname"></a>Получить Group By displayName

##### <a name="request"></a><a name="request-9"></a>Запрос
*GET/Граупс? Ексклудедаттрибутес = Members&Filter = displayName EQ "displayName" HTTP/1.1*

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

#### <a name="update-group-non-member-attributes"></a>Update Group [атрибуты, не являющиеся членами]

##### <a name="request"></a><a name="request-10"></a>Запрос

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

##### <a name="response"></a><a name="response-10"></a>Ответ

*HTTP/1.1 204 без содержимого*

### <a name="update-group-add-members"></a>Обновить группу [добавить участников]

##### <a name="request"></a><a name="request-11"></a>Запрос

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

##### <a name="response"></a><a name="response-11"></a>Ответ

*HTTP/1.1 204 без содержимого*

#### <a name="update-group-remove-members"></a>Обновление группы [удаление участников]

##### <a name="request"></a><a name="request-12"></a>Запрос

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

##### <a name="response"></a><a name="response-12"></a>Ответ

*HTTP/1.1 204 без содержимого*

#### <a name="delete-group"></a>Удалить группу

##### <a name="request"></a><a name="request-13"></a>Запрос

*Удаление/Groups/cdb1ce18f65944079d37 HTTP/1.1*

##### <a name="response"></a><a name="response-13"></a>Ответ

*HTTP/1.1 204 без содержимого*

### <a name="security-requirements"></a>Требования к безопасности
**Версии протокола TLS**

Единственные допустимые версии протокола TLS — TLS 1,2 и TLS 1,3. Другие версии TLS не разрешены. Ни одна из версий SSL не разрешена. 
- Ключи RSA должны составлять не менее 2 048 бит.
- Ключи ECC должны иметь по крайней мере 256 бит, созданных с помощью утвержденной эллиптической кривой.


**Длина ключей**

Все службы должны использовать сертификаты X. 509, созданные с использованием криптографических ключей достаточной длины, то есть:

**Комплекты шифров**

Все службы должны быть настроены для использования следующих комплектов шифров в указанном ниже порядке. Обратите внимание, что если у вас есть только сертификат RSA, установленные наборы шифров ECDSA не оказывают никакого влияния. </br>

Минимальная строка наборов шифров TLS 1,2:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384


## <a name="step-3-build-a-scim-endpoint"></a>Шаг 3. Создание конечной точки SCIM

Теперь, когда вы разработали схему и поняли реализацию Azure AD SCIM, вы можете приступить к разработке конечной точки SCIM. Вместо того чтобы начинать с нуля и самостоятельно создавать реализацию, вы можете полагаться на ряд библиотек SCIM с открытым исходным кодом, опубликованных сообществом SCIM.

Исходный [код](https://aka.ms/SCIMReferenceCode) .NET Core, опубликованный группой подготовки Azure AD, — это один из таких ресурсов, который позволяет начать разработку. После создания конечной точки SCIM ее нужно будет протестировать. Вы можете использовать коллекцию поступающих [тестов](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) , предоставленную как часть кода ссылки, или выполнить с помощью примеров запросов и ответов, приведенных [выше](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#user-operations).  

   > [!Note]
   > Справочный код предназначен для того, чтобы приступить к созданию конечной точки SCIM и предоставить "как есть". Вклады из сообщества помогут вам собрать и поддерживать код.

Решение состоит из двух проектов: _Microsoft. scim_ и _Microsoft. scim. вебхостсампле_.

Проект _Microsoft. scim_ — это библиотека, которая определяет компоненты веб-службы, которые соответствует спецификации scim. Он объявляет интерфейс _Microsoft. scim. IProvider_, запросы преобразуются в вызовы методов поставщика, которые были бы запрограммированы для работы с хранилищем удостоверений.

![Подразделение: запрос, преобразованный в вызовы методов поставщика](media/use-scim-to-provision-users-and-groups/scim-figure-3.png)

Проект _Microsoft. scim. вебхостсампле_ — это веб-приложение Visual Studio ASP.NET Core, основанное на _пустом_ шаблоне. Это позволяет развертывать пример кода как изолированный, размещенный в контейнерах или в службы IIS. Он также реализует интерфейс _Microsoft. scim. IProvider_ , который хранит классы в памяти в качестве примера хранилища удостоверений.

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

Служба SCIM должна иметь HTTP-адрес и сертификат проверки подлинности сервера, для которого корневым центром сертификации является одно из следующих имен:

* CNNIC;
* Comodo;
* CyberTrust;
* DigiCert
* GeoTrust;
* GlobalSign;
* Go Daddy;
* VeriSign;
* WoSign.

Пакет SDK для .NET Core включает сертификат разработки HTTPS, который можно использовать во время разработки, сертификат устанавливается в рамках первого запуска. В зависимости от способа запуска веб-приложения ASP.NET Core оно будет прослушивать другой порт:

* Microsoft. SCIM. Вебхостсампле:https://localhost:5001
* IIS Express:https://localhost:44359/

Дополнительные сведения о протоколе HTTPS в ASP.NET Core см. по следующей ссылке: [принудительное использование HTTPS в ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/enforcing-ssl)

### <a name="handling-endpoint-authentication"></a>Обработка аутентификации на конечной точке

Запросы от Azure Active Directory содержат токен носителя OAuth 2.0. Любая служба, получающая запрос, должна проверять подлинность издателя как Azure Active Directory ожидаемого клиента Azure Active Directory.

В токене издатель идентифицируется утверждением ISS, например `"iss":"https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/"`. В этом примере базовый адрес значения утверждения, `https://sts.windows.net`, определяет Azure Active Directory как издатель, тогда как сегмент относительных адресов, _(cbb1a5ac-f33b-45fa-9bf5-f37db0fed422)_, является уникальным идентификатором клиента Azure Active Directory, для которого был выдан маркер.

Аудитория для маркера будет ИДЕНТИФИКАТОРом шаблона приложения для приложения в коллекции. Каждое приложение, зарегистрированное в одном клиенте, может получать одно и то же `iss` утверждение с запросами scim. Идентификатор шаблона приложения для всех пользовательских приложений — _8adf8e6e-67b2-4cf2-A259-e3dc5476c621_. Токен, созданный службой подготовки Azure AD, следует использовать только для тестирования. Его не следует использовать в рабочих средах.

В примере кода запросы проходят проверку подлинности с помощью пакета Microsoft. AspNetCore. Authentication. JwtBearer. Следующий код обеспечивает проверку подлинности запросов к любой из конечных точек службы с помощью токена носителя, выданного Azure Active Directory для указанного клиента:

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

Токен носителя также необходим для использования предоставленных [почтовых тестов](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) и выполнения локальной отладки с помощью localhost. В примере кода используются среды ASP.NET Core для изменения параметров проверки подлинности на этапе разработки и включения использования самозаверяющего маркера.

Дополнительные сведения о нескольких окружениях в ASP.NET Core см. в следующих ссылках: [Использование нескольких сред в ASP.NET Core](
https://docs.microsoft.com/aspnet/core/fundamentals/environments)

Следующий код обеспечивает проверку подлинности запросов к любой из конечных точек службы с помощью токена носителя, подписанного пользовательским ключом:

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

Отправьте запрос GET на контроллер токена, чтобы получить действительный токен носителя, метод _женератежсонвебтокен_ отвечает за создание маркера, соответствующего параметрам, настроенным для разработки:

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

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>Обработка подготовки и отмены подготовки пользователей

***Пример 1. Запрос к службе для соответствующего пользователя***

Azure Active Directory запрашивает службу пользователя, у которого значение атрибута externalId совпадает со значением атрибута mailNickname пользователя Azure AD. Запрос выражается в виде HTTP-запроса (например, в данном примере), где jyoung является примером выражения mailNickname пользователя в Azure Active Directory.

>[!NOTE]
> Это только пример. Не все пользователи будут иметь атрибут mailNickname, а значение, которое пользователь может не быть уникальным в каталоге. Кроме того, атрибут, используемый для сопоставления (в данном случае это externalId), можно настроить в [сопоставлениях атрибутов Azure AD](customize-application-attributes.md).

```
GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
 Authorization: Bearer ...
```

В примере кода запрос преобразуется в вызов метода QueryAsync поставщика службы. Подпись этого метода будет выглядеть так: 

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

В образце запроса для пользователя с заданным значением атрибута externalId значения аргументов, переданных в метод QueryAsync:

* parameters.AlternateFilters.Count: 1
* parameters.AlternateFilters.ElementAt(0).AttributePath: "externalId"
* parameters.AlternateFilters.ElementAt(0).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(0).ComparisonValue: "jyoung"

***Пример 2. Подготавливает пользователя***

Если ответ на запрос к веб-службе для пользователя с значением атрибута externalId, совпадающим со значением атрибута mailNickname пользователя, не возвращает никаких пользователей, то Azure Active Directory запрашивает у этой службы пользователя, соответствующего Azure Active Directory.  Ниже приведен пример такого запроса. 

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

В примере кода запрос преобразуется в вызов метода CreateAsync поставщика службы. Подпись этого метода будет выглядеть так: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource is defined in 
 // Microsoft.SCIM.Schemas.  

 Task<Resource> CreateAsync(IRequest<Resource> request);
```

В запросе на предоставление пользователя значение аргумента Resource является экземпляром класса Microsoft. SCIM. Core2EnterpriseUser, определенного в библиотеке Microsoft. SCIM. schemas.  Если запрос на подготовку пользователя завершился удачно, реализация метода должна вернуть экземпляр класса Microsoft. SCIM. Core2EnterpriseUser со значением свойства identifier, равным уникальному идентификатору вновь подготовленного пользователя.  

***Пример 3. Запрос текущего состояния пользователя*** 

Для обновления пользователя, уже существующего в хранилище удостоверений с интерфейсом SCIM, Azure Active Directory запросит у службы текущее состояние этого пользователя, отправив запрос следующего вида. 

```
 GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
 Authorization: Bearer ...
```

В примере кода запрос преобразуется в вызов метода Ретриевеасинк поставщика службы. Подпись этого метода будет выглядеть так: 

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

***Пример 4. Запросите значение ссылочного атрибута для обновления*** 

Если необходимо обновить атрибут ссылки, Azure Active Directory запрашивает службу, чтобы определить, соответствует ли текущее значение ссылочного атрибута в хранилище удостоверений, которое находится перед службой, в Azure Active Directory. Для пользователей таким способом запрашивается только значение атрибута manager. Ниже приведен пример запроса для определения того, имеет ли атрибут Manager объекта пользователя определенное значение: в примере кода запрос преобразуется в вызов метода QueryAsync поставщика службы. В нем передается аргумент parameters, значением которого будет объект со следующими свойствами. 
  
* parameters.AlternateFilters.Count: 2
* parameters.AlternateFilters.ElementAt(x).AttributePath: "ID"
* parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(x).ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
* parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
* parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(y).ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
* parameters.RequestedAttributePaths.ElementAt(0): "ID"
* parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

Здесь значение индекса x может быть равно 0, а значение индекса y может быть равно 1, либо значение x может быть равно 1, а значение y может быть равно 0, в зависимости от порядка выражений в параметре фильтра запроса.   

***Пример 5. Запрос из Azure AD в службу SCIM для обновления пользователя*** 

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

В примере кода запрос преобразуется в вызов метода UpdateAsync поставщика службы. Подпись этого метода будет выглядеть так: 

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

***Пример 6. Отменить наполнение пользователя***

Чтобы отменить наполнение пользователя от хранилища удостоверений, посылаемого службой SCIM, Azure AD отправляет запрос, например:

```
  DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
  Authorization: Bearer ...
```

В примере кода запрос преобразуется в вызов метода DeleteAsync поставщика службы. Подпись этого метода будет выглядеть так: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.IResourceIdentifier, 
 // is defined in Microsoft.SCIM.Protocol. 

 Task DeleteAsync(IRequest<IResourceIdentifier> request);
```

Объект, указанный в качестве значения аргумента resourceIdentifier, содержит эти значения свойств в примере запроса для отмены наполнения пользователя: 

* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client"></a>Шаг 4. Интеграция конечной точки SCIM с клиентом Azure AD SCIM

Azure AD можно настроить на автоматическую подготавливать назначенных пользователей и группы к приложениям, реализующим конкретный профиль [протокола SCIM 2,0](https://tools.ietf.org/html/rfc7644). Особенности профиля описаны в [статье шаг 2. изучение реализации Azure AD scim](#step-2-understand-the-azure-ad-scim-implementation).

Рекомендуется связаться с поставщиком приложения или ознакомиться с документацией поставщика для получения подтверждения соответствия этим требованиям.

> [!IMPORTANT]
> Реализация Azure AD SCIM создана на основе службы подготовки пользователей Azure AD, которая обеспечивает постоянную синхронизацию пользователей между Azure AD и целевым приложением и реализует очень конкретный набор стандартных операций. Важно понимать эти поведения, чтобы понять поведение клиента Azure AD SCIM. Дополнительные сведения см. в разделе [циклы подготовки: Начальная и добавочная](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) в [процессе подготовки](how-provisioning-works.md).

### <a name="getting-started"></a>Начало работы

Описанные ранее в этой статье приложения, поддерживающие профиль SCIM, можно подключать к Azure Active Directory с помощью функции "Приложение не из коллекции", доступной в коллекции приложений Azure AD. После подключения Azure AD запускает процесс синхронизации каждые 40 минут, запрашивая конечную точку SCIM приложения для получения назначенных пользователей и групп, и создает или изменяет их согласно сведениям о назначении.

**Подключение приложения, поддерживающего SCIM:**

1. Войдите на [портал Azure Active Directory](https://aad.portal.azure.com). Обратите внимание, что вы можете получить доступ к бесплатной пробной версии Azure Active Directory с лицензиями P2, зарегистрировавшись в [программе разработчика](https://developer.microsoft.com/office/dev-program) .
2. В области слева выберите **корпоративные приложения** . Отображается список всех настроенных приложений, включая приложения, добавленные из коллекции.
3. Выберите **+ создать приложение** > **все** > **приложения не из коллекции**.
4. Введите имя приложения и нажмите кнопку **Добавить** , чтобы создать объект приложения. Новое приложение будет добавлено в список корпоративных приложений и откроется на экране управления приложениями.

   ![На снимке экрана показана коллекция приложений Azure AD](media/use-scim-to-provision-users-and-groups/scim-figure-2a.png)<br/>
   *Коллекция приложений Azure AD*

5. На экране Управление приложениями выберите **Подготовка** на панели слева.
6. В меню **Режим подготовки к работе** выберите **Автоматический**.

   ![Пример: страница подготовки приложения в портал Azure](media/use-scim-to-provision-users-and-groups/scim-figure-2b.png)<br/>
   *Настройка подготовки в портал Azure*

7. В поле **URL-адрес клиента** введите URL-адрес конечной точки SCIM приложения. Пример: `https://api.contoso.com/scim/`
8. Если конечной точке SCIM требуется токен носителя OAuth от издателя, отличного от Azure AD, скопируйте необходимый токен носителя OAuth в необязательное для заполнения поле **Секретный токен**. Если это поле оставлено пустым, Azure AD включает маркер носителя OAuth, выданный в Azure AD с каждым запросом. Приложения, использующие Azure AD в качестве поставщика удостоверений, могут проверить этот выданный Azure AD токен. 
   > [!NOTE]
   > ***Не*** рекомендуется оставлять это поле пустым и использовать маркер, СОЗДАННЫЙ Azure AD. Этот параметр в основном доступен для целей тестирования.
9. Выберите **проверить подключение** , чтобы Azure Active Directory попытаться подключиться к КОНЕЧНОЙ точке scim. Если попытка не удалась, выводится информация об ошибке.  

    > [!NOTE]
    > **Проверка подключения** запрашивает конечную точку SCIM для пользователя, который не существует, используя случайный идентификатор GUID в качестве свойства сопоставления, выбранного в конфигурации Azure AD. Ожидаемый правильный ответ — HTTP 200 OK с пустым сообщением ListResponse SCIM.

10. Если попытка подключения к приложению завершилась удачно, нажмите кнопку **сохранить** , чтобы сохранить учетные данные администратора.
11. В разделе " **сопоставления** " есть два выбираемых набора [сопоставлений атрибутов](customize-application-attributes.md): один для объектов User и один для объектов группы. Выберите каждый из них, чтобы просмотреть атрибуты, которые синхронизируются из Azure Active Directory с вашим приложением. Атрибуты, выбранные как свойства **Matching**, используются для сопоставления пользователей и групп в вашем приложении для операций обновления. Чтобы зафиксировать изменения, щелкните **Сохранить**.

    > [!NOTE]
    > При необходимости можно отключить синхронизацию объектов групп, отключив сопоставление групп.

12. Поле **Область** в разделе **Параметры** определяет, какие пользователи и группы синхронизируются. Выберите **синхронизировать только назначенные пользователи и группы** (рекомендуется), чтобы синхронизировать только пользователей и группы, назначенные на вкладке **Пользователи и группы** .
13. После завершения настройки задайте для параметра **состояние подготовки** значение **вкл**.
14. Нажмите кнопку **сохранить** , чтобы запустить службу подготовки Azure AD.
15. Если выполняется синхронизация только назначенных пользователей и групп (рекомендуется), обязательно перейдите на вкладку **Пользователи и группы** и назначьте пользователей или группы, которые требуется синхронизировать.

После запуска начального цикла можно выбрать **журналы подготовки** на панели слева, чтобы отслеживать ход выполнения, в котором отображаются все действия, выполняемые службой подготовки в приложении. Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](check-status-user-account-provisioning.md).

> [!NOTE]
> Начальный цикл занимает больше времени, чем последующие операции синхронизации, что происходит примерно каждые 40 минут при условии, что служба запущена.

## <a name="step-5-publish-your-application-to-the-azure-ad-application-gallery"></a>Шаг 5. Публикация приложения в коллекции приложений Azure AD

Если вы создаете приложение, которое будет использоваться более чем одним клиентом, его можно сделать доступным в коллекции приложений Azure AD. Это упростит организациям Поиск приложения и настройку подготовки. Публикация приложения в коллекции Azure AD и обеспечение доступности подготовки для других пользователей — это просто. Узнайте [здесь](../develop/howto-app-gallery-listing.md), как это сделать. Корпорация Майкрософт будет работать с вами, чтобы интегрировать приложение в нашу галерею, тестировать конечную точку и выпустить [документацию](../saas-apps/tutorial-list.md) по адаптации клиентов. 

### <a name="gallery-onboarding-checklist"></a>Контрольный список для адаптации коллекции
Следуйте приведенному ниже контрольному списку, чтобы обеспечить быструю работу приложения, и у клиентов есть возможность беспрепятственного развертывания. Сведения будут собраны при подключении к коллекции. 
> [!div class="checklist"]
> * Поддержка конечной точки пользователя и группы [SCIM 2,0](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#step-2-understand-the-azure-ad-scim-implementation) (рекомендуется использовать только один из них)
> * Поддержка не менее 25 запросов в секунду для каждого клиента (требуется)
> * Создание контактов по проектированию и технической поддержке для поддержки клиентов при адаптации коллекции (обязательно)
> * 3. Проверка учетных данных без истечения срока действия приложения (обязательно)
> * Поддержка предоставления кода авторизации OAuth или токена с длительным временем существования, как описано ниже (обязательно)
> * Создание точки обслуживания и поддержки контактов для поддержки клиентов после адаптации коллекции (обязательно)
> * Поддержка обновления нескольких членств в группах с одним ИСПРАВЛЕНИЕм (рекомендуется) 
> * Задокументируйте общедоступную конечную точку SCIM (рекомендуется) 
> * [Поддержка обнаружения схемы](https://tools.ietf.org/html/rfc7643#section-6) (рекомендуется)


### <a name="authorization-for-provisioning-connectors-in-the-application-gallery"></a>Авторизация для соединителей подготовки в коллекции приложений
Спецификация SCIM не определяет схему для проверки подлинности и авторизации, характерную для SCIM. Он основан на использовании существующих отраслевых стандартов. Клиент подготовки Azure AD поддерживает два метода авторизации для приложений в коллекции. 

|Метод авторизации|Плюсы|Минусы|Поддержка|
|--|--|--|--|
|Имя пользователя и пароль (не рекомендуется или не поддерживаются в Azure AD)|Простота реализации|Небезопасное — [ваш PA $ $Word не имеет значения](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)|Поддерживается для каждого случая для приложений коллекции. Не поддерживается для приложений, не относящихся к коллекции.|
|Токен носителя с длительным временем существования|Для долгосрочных маркеров не требуется наличие пользователя. Администраторы могут легко использовать их при настройке подготовки.|Долгосрочные токены могут быть трудно использовать совместно с администратором без использования небезопасных методов, таких как электронная почта. |Поддерживается для приложений из коллекции и не из коллекции. |
|Предоставление кода авторизации OAuth|Маркеры доступа намного короче, чем пароли, и имеют механизм автоматического обновления, который не имеет долгосрочных токенов носителя.  Реальный пользователь должен присутствовать во время начальной авторизации, добавляя уровень ответственности. |Требуется наличие пользователя. Если пользователь покидает организацию, маркер является недопустимым, и авторизация должна быть выполнена снова.|Поддерживается для приложений из коллекции. Выполняется поддержка приложений, не относящихся к коллекции.|
|Предоставление учетных данных клиента OAuth|Маркеры доступа намного короче, чем пароли, и имеют механизм автоматического обновления, который не имеет долгосрочных токенов носителя. Как предоставление кода авторизации, так и предоставление учетных данных клиента создают одинаковый тип маркера доступа, поэтому перемещение между этими методами прозрачно для API.  Подготовка может быть полностью автоматизирована, а новые токены могут запрашиваться без вмешательства пользователя. ||Не поддерживается для приложений из коллекции и не из коллекции. Поддержка находится в невыполненной работе.|

[!NOTE] Не рекомендуется оставлять поле маркера пустым в пользовательском интерфейсе настраиваемого приложения конфигурации подготовки Azure AD. Создаваемый маркер в основном доступен для целей тестирования.

**Поток предоставления кода авторизации OAuth:** Служба подготовки поддерживает [предоставление кода авторизации](https://tools.ietf.org/html/rfc6749#page-24). После отправки запроса на публикацию приложения в коллекции наша команда будет работать с вами, чтобы получить следующие сведения:
*  URL-адрес авторизации. Клиент получает URL-адрес для получения авторизации от владельца ресурса через перенаправление агента пользователя. Пользователь перенаправляется на этот URL-адрес для авторизации доступа. 
*  URL-адрес обмена маркерами. Клиент получает URL-адрес для обмена данными авторизации для маркера доступа, обычно с проверкой подлинности клиента.
*  Идентификатор клиента. сервер авторизации выдает зарегистрированному клиенту идентификатор клиента, который представляет собой уникальную строку, представляющую сведения о регистрации, предоставленные клиентом.  Идентификатор клиента не является секретом; Он предоставляется владельцу ресурса и **не должен** использоваться отдельно для проверки подлинности клиента.  
*  Секрет клиента. секрет клиента — это секрет, созданный сервером авторизации. Это должно быть уникальное значение, известное только серверу авторизации. 

Обратите внимание, что OAuth v1 не поддерживается из-за уязвимости секрета клиента. Поддерживается протокол OAuth v2.  

Рекомендации (рекомендуется, но не обязательно):
* Поддержка нескольких URL-адресов перенаправления. Администраторы могут настроить подготовку как с "portal.azure.com", так и с "aad.portal.azure.com". Поддержка нескольких URL-адресов перенаправления гарантирует, что пользователи смогут авторизовать доступ с любого портала.
* Поддержка нескольких секретов для обеспечения беспрепятственного продления секрета без простоев. 

**Длительное время существования токенов носителя OAuth:** Если приложение не поддерживает поток предоставления кода авторизации OAuth, можно также создать долгосрочный токен носителя OAuth, который может использоваться администратором для настройки интеграции подготовки. Токен должен быть бессрочным, иначе задание подготовки будет [помещено в карантин](application-provisioning-quarantine-status.md) по истечении срока действия маркера. Размер этого токена должен быть ниже 1 КБ.  

Чтобы получить дополнительные методы проверки подлинности и авторизации, сообщите нам о [UserVoice](https://aka.ms/appprovisioningfeaturerequest).

### <a name="gallery-go-to-market-launch-check-list"></a>Список проверки запуска коллекции для перехода на рынок
Чтобы обеспечить осведомленность и спрос на совместную интеграцию, мы рекомендуем обновить существующую документацию и повысить эффективность интеграцию в маркетинговых каналах.  Ниже приведен набор действий контрольного списка, которые рекомендуется выполнить для поддержки запуска.

* **Готовность к продажам и поддержка клиентов.** Убедитесь, что ваши группы по продажам и поддержке знают и могут говорить о возможностях интеграции. Попросите специалистов по продажам и поддержке, предоставьте им ответы на часто задаваемые вопросы и включите интеграцию в свои материалы по продажам. 
* **Запись блога и (или) пресс-релиз.** Создание записи блога или пресс-релиза, описывающей совместную интеграцию, преимущества и способы приступить к работе. [Пример: Импривата и Azure Active Directory нажмите клавишу Release](https://www.imprivata.com/company/press/imprivata-introduces-iam-cloud-platform-healthcare-supported-microsoft) 
* **Социальные сети.** Используйте социальные сети, такие как Twitter, Facebook или LinkedIn, чтобы повысить уровень интеграции с клиентами. Не забудьте включить @AzureAD , чтобы мы могли ретвитовать сообщение. [Пример: Импривата Twitter POST](https://twitter.com/azuread/status/1123964502909779968)
* **Веб-сайт маркетинга.** Создайте или обновите маркетинговые страницы (например, страницу интеграции, страницу партнера, страницу цен и т. д.), чтобы включить доступность Объединенной интеграции. [Пример: страница интеграции Pingboard](https://pingboard.com/org-chart-for), [Страница интеграции Smartsheet](https://www.smartsheet.com/marketplace/apps/microsoft-azure-ad), [страница цен Monday.com](https://monday.com/pricing/) 
* **Техническая документация.** Создайте статью центра справки или техническую документацию о том, как можно приступить к работе с клиентами. [Пример: интеграция делегата + Microsoft Azure Active Directory.](https://envoy.help/en/articles/3453335-microsoft-azure-active-directory-integration/
) 
* **Взаимодействие с клиентами.** Оповещать клиентов о новой интеграции через взаимодействие с клиентами (ежемесячные информационные бюллетени, кампании по электронной почте, заметки о выпуске продукта). 

### <a name="allow-ip-addresses-used-by-the-azure-ad-provisioning-service-to-make-scim-requests"></a>Разрешить IP-адреса, используемые службой подготовки Azure AD для выполнения запросов SCIM

Некоторые приложения разрешают входящий трафик в свое приложение. Чтобы служба подготовки Azure AD функционировала должным образом, необходимо разрешить использование IP-адресов. Список IP-адресов для каждого тега или региона службы см. в файле JSON [Azure IP Ranges and Service Tags – Public Cloud](https://www.microsoft.com/download/details.aspx?id=56519) (Диапазоны IP-адресов и теги служб Azure — общедоступное облако). При необходимости вы можете скачать и программировать эти IP-адреса в брандмауэре. Зарезервированные диапазоны IP-адресов для подготовки Azure AD можно найти в разделе "Азуреактиведиректоридомаинсервицес".

## <a name="related-articles"></a>Похожие статьи

* [Автоматизация подготовки пользователей и ее отмены в приложениях SaaS](user-provisioning.md)
* [Настройка сопоставлений атрибутов для подготовки пользователей для приложений SaaS в Azure Active Directory](customize-application-attributes.md)
* [Написание выражений для сопоставлений атрибутов](functions-for-customizing-application-data.md)
* [Фильтры области для подготовки пользователей](define-conditional-rules-for-provisioning-user-accounts.md)
* [Уведомления о подготовке учетных записей](user-provisioning.md)
* [Список руководств по интеграции приложений SaaS](../saas-apps/tutorial-list.md)
