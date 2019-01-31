---
title: ContentDefinitions — Azure Active Directory B2C | Документация Майкрософт
description: Сведения об указании элемента ContentDefinitions настраиваемой политики в Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: e36eb3816d6f465552c4db740508d5e7f5fa1331
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55161670"
---
# <a name="contentdefinitions"></a>ContentDefinitions

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Вы можете настроить внешний вид любого [самоподтвержденного технического профиля](self-asserted-technical-profile.md). Служба Azure Active Directory B2C выполняет код в браузере пользователя и использует современный подход — общий доступ к ресурсам независимо от источника (CORS). 

Для настройки пользовательского интерфейса необходимо указать URL-адрес в элементе **ContentDefinition** с настраиваемым содержимым HTML. В самоподтвержденном техническом профиле или **OrchestrationStep** нужно указать этот идентификатор определения содержимого. Определение содержимого может содержать элемент **LocalizedResourcesReferences**, задающий список локализованных ресурсов для загрузки. Azure AD B2C объединяет элементы пользовательского интерфейса с содержимым HTML, загруженным по указанному URL-адресу, и отображает страницу для пользователя.

Элемент **ContentDefinitions** содержит URL-адреса шаблонов HTML5, которые могут использоваться в пути взаимодействия пользователя. URI страницы HTML5 используется для указанного шага пользовательского интерфейса, например для входа или регистрации, сброса пароля или страниц ошибок. Внешний вид можно изменить с помощью переопределения LoadUri для файла HTML5. Можно создать новые определения содержимого в соответствии с потребностями. Этот элемент может содержать ссылку на локализованные ресурсы — идентификатор локализации, заданный в элементе [Localization](localization.md).

В следующем примере показаны идентификатор определения содержимого и определение локализованных ресурсов:

```XML
<ContentDefinition Id="api.localaccountsignup">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Local account sign up page</Item>
  </Metadata>
  <LoalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
    ...
```

Метаданные самоподтвержденного технического профиля **LocalAccountSignUpWithLogonEmail** содержат идентификатор определения содержимого **ContentDefinitionReferenceId**, для которого задано значение `api.localaccountsignup`

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    ...
  </Metadata>
  ...
```


## <a name="contentdefinition"></a>ContentDefinition

Элемент **ContentDefinition** содержит следующий атрибут:

| Атрибут | Обязательно | ОПИСАНИЕ |
| --------- | -------- | ----------- |
| Идентификатор | Yes | Идентификатор определения содержимого. Значение указано в разделе **Идентификаторы определения содержимого** далее на этой странице. |

Элемент **ContentDefinition** содержит следующие элементы:

| Элемент | Вхождения | ОПИСАНИЕ |
| ------- | ----------- | ----------- |
| LoadUri | 1:1 | Строка, содержащая URL-адрес страницы HTML5 для определения содержимого. |
| RecoveryUri | 0:1 | Строка, содержащая URL-адрес страницы HTML для отображения ошибки, связанной с определением содержимого. | 
| DataUri | 1:1 | Строка, содержащая относительный URL-адрес HTML-файла, который предоставляет пользовательский интерфейс для вызова соответствующего шага. |  
| Метаданные | 1:1 | Коллекция пар "ключ — значение", содержащая метаданные, используемые в определении содержимого. | 
| LocalizedResourcesReferences | 0:1 | Коллекция ссылок на локализованные ресурсы. Этот элемент позволяет настроить локализацию пользовательского интерфейса и атрибута утверждений (claims). |

### <a name="datauri"></a>DataUri

Элемент **DataUri** используется для указания идентификатора страницы. В Azure AD B2C идентификатор страницы используется для загрузки и запуска элементов пользовательского интерфейса и JavaScript на стороне клиента. Формат значения — `urn:com:microsoft:aad:b2c:elements:page-name:version`.  В следующей таблице перечислены идентификаторы страниц, которые можно использовать.

| Значение |   ОПИСАНИЕ |
| ----- | ----------- |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | Отображает страницу ошибки при обнаружении исключения или ошибки. |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | Отображает список поставщиков удостоверений, которые пользователи могут выбирать во время входа. | 
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | Отображает форму для входа в локальную учетную запись (на основе адреса электронной почты или имени пользователя). Это значение также предоставляет функцию "Оставаться в системе" и ссылку "Забыли пароль?" . | 
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | Отображает форму для входа в локальную учетную запись (на основе адреса электронной почты или имени пользователя). |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | Проверяет номера телефонов с помощью SMS-сообщения или голосового вызова во время регистрации или входа. |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | Отображает форму, которая позволяет пользователям создать или обновить свой профиль. | 


### <a name="localizedresourcesreferences"></a>LocalizedResourcesReferences

Элемент **LocalizedResourcesReferences** содержит следующие элементы:

| Элемент | Вхождения | ОПИСАНИЕ |
| ------- | ----------- | ----------- |
| LocalizedResourcesReference | 1:n | Список ссылок на локализованные ресурсы для определения содержимого. | 

Элемент **LocalizedResourcesReferences** содержит следующие атрибуты:

| Атрибут | Обязательно | ОПИСАНИЕ |
| --------- | -------- | ----------- |
| Язык | Yes | Строка, содержащая поддерживаемый язык для политики согласно стандарту RFC 5646 "Tags for Identifying Languages" (Теги для идентификации языков). |
| LocalizedResourcesReferenceId | Yes | Идентификатор элемента **LocalizedResources**. |

В следующем примере показано определение содержимого для регистрации или входа в систему:

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Signin and Signup</Item>
  </Metadata>
</ContentDefinition>
```

Следующий пример показывает определение содержимого регистрации или входа в систему со ссылкой на локализацию для английского, французского и испанского языков:

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Signin and Signup</Item>
  </Metadata>
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.signuporsignin.en" />
    <LocalizedResourcesReference Language="fr" LocalizedResourcesReferenceId="api.signuporsignin.rf" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.signuporsignin.es" />
</LocalizedResourcesReferences>
</ContentDefinition>
```

Сведения о том, как добавить поддержку локализации в определения содержимого, см. в описании элемента [Localization](localization.md).

## <a name="content-definition-ids"></a>Идентификаторы определения содержимого

Атрибут ID (идентификатор) элемента **ContentDefinition** указывает тип страницы, относящейся к определению содержимого. Этот элемент определяет контекст, который будет применяться в пользовательском шаблоне HTML5 или CSS. В следующей таблице описан набор идентификаторов определений содержимого, распознаваемых Identity Experience Framework, и типы страниц, относящиеся к ним. Вы можете создать собственные определения содержимого с произвольным идентификатором.

| ИД | Шаблон по умолчанию | ОПИСАНИЕ | 
| -- | ---------------- | ----------- |
| **api.error** | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Страница ошибки** — отображает страницу ошибки при обнаружении исключения или ошибки. |
| **api.idpselections** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Страница выбора поставщика удостоверений** — на этой странице содержится список поставщиков удостоверений, которые пользователи могут выбирать во время входа. Обычно это поставщики удостоверений организаций, социальных сетей, включая Facebook и Google+, или локальных учетных записей. |
| **api.idpselections.signup** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Выбор поставщика удостоверений для регистрации** — на этой странице содержится список поставщиков удостоверений, которые пользователи могут выбирать во время регистрации. Обычно это поставщики удостоверений организаций, социальных сетей, включая Facebook и Google+, или локальных учетных записей. |
| **api.localaccountpasswordreset** | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Страница "Забыли пароль?"** — отображает форму, которую пользователи должны заполнить для сброса пароля. |
| **api.localaccountsignin** | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Страница входа в локальную учетную запись** — отображает форму для входа в локальную учетную запись (на основе адреса электронной почты или имени пользователя). Форма может содержать текстовое поле ввода и окно ввода пароля. |
| **api.localaccountsignup** | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Страница регистрации локальной учетной записи** — отображает форму для регистрации локальной учетной записи на основе адреса электронной почты или имени пользователя. Форма может содержать разные элементы управления для ввода текста, например: текстовое поле, поле ввода пароля, переключатель, раскрывающийся список с единственным выбором или флажки множественного выбора. |
| **api.phonefactor** | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Страница многофакторной проверки подлинности** — проверяет номера телефонов с помощью SMS-сообщения или голосового вызова во время регистрации или входа. |
| **api.selfasserted** | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Страница регистрации учетной записи социальной сети** — отображает форму, которую нужно заполнить при регистрации с использованием существующей учетной записи от поставщика удостоверений в социальных сетях. Эта страница аналогична странице регистрации локальных учетных записей, за исключением полей для ввода пароля. |
| **api.selfasserted.profileupdate** | [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Страница обновления профиля** — отображает форму для обновления профиля пользователя. Эта страница аналогична странице регистрации локальных учетных записей, за исключением полей для ввода пароля. |
| **api.signuporsignin** | [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Унифицированная страница регистрации и входа** — обрабатывает процесс регистрации и входа пользователей. Пользователи могут использовать поставщики удостоверений организаций, социальных сетей, включая Facebook и Google+, или локальные учетные записи. |
 
