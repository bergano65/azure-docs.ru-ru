---
title: TechnicalProfiles | Документация Майкрософт
description: Использование элемента UserJourneys в настраиваемой политике для Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f712634c83fa290ab24d5e8437a82d5f93af0b7f
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66512283"
---
# <a name="technicalprofiles"></a>TechnicalProfiles

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Элемент **TechnicalProfiles** содержит набор технических профилей, поддерживаемых поставщиком утверждений. Каждый поставщик утверждений должен иметь один или несколько технических профилей, которые определяют конечные точки и протоколы для связи с этим поставщиком утверждений. Поставщик утверждений может иметь несколько технических профилей.

```XML
<ClaimsProvider>
  <DisplayName>Display name</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Technical profile identifier">
      <DisplayName>Display name of technical profile</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">URL of service</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
      </Metadata>
      <InputTokenFormat>JWT</InputTokenFormat>
      <OutputTokenFormat>JWT</OutputTokenFormat>
      <CryptographicKeys>
        <Key ID="Key identifier" StorageReferenceId="Storage key container identifier"/>
        ...
      </CryptographicKeys>
      <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="Claims transformation identifier" />
        ...
      <InputClaimsTransformations>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" DefaultValue="givenName" PartnerClaimType="firstName" />
        ...
      </InputClaims>
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="givenName" DefaultValue="givenName" PartnerClaimType="firstName" />
        ...
      </PersistedClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" DefaultValue="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
      </OutputClaims>
      <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="Claims transformation identifier" />
        ...
      <OutputClaimsTransformations>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="Technical profile identifier" />
        ...
      </ValidationTechnicalProfiles>
      <SubjectNamingInfo ClaimType="Claim type identifier" />
      <IncludeTechnicalProfile ReferenceId="Technical profile identifier" />
      <UseTechnicalProfileForSessionManagement ReferenceId="Technical profile identifier" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

Элемент **TechnicalProfile** содержит следующий атрибут.

| Атрибут | Обязательно для заполнения | Описание |
|---------|---------|---------|
| Идентификатор | Да | Уникальный идентификатор технического профиля. Этот идентификатор позволяет указать технический профиль в других элементах в файле политики. Например, в элементах **OrchestrationSteps** и **ValidationTechnicalProfile**. |

Элемент **TechnicalProfile** содержит следующие элементы:

| Элемент | Вхождения | Описание |
| ------- | ----------- | ----------- |
| Домен | 0:1 | Доменное имя технического профиля. Например, если в техническом профиле указан поставщик удостоверений Facebook, доменное имя будет иметь значение Facebook.com. |
| DisplayName | 0:1 | Имя технического профиля, отображаемое для пользователей. |
| Описание | 0:1 | Описание технического профиля, отображаемое для пользователей. |
| Протокол | 0:1 | Протокол, используемый для взаимодействия с другой стороной. |
| Метаданные | 0:1 | Коллекция пар "ключ-значение", которые используются в протоколе взаимодействия с конечной точкой во время транзакции. |
| InputTokenFormat | 0:1 | Формат входного маркера. Возможные значения: `JSON`, `JWT`, `SAML11` или `SAML2`. Значение `JWT` обозначает JSON Web Token по спецификации IETF. Значение `SAML11` обозначает веб-маркер SAML 1.1 по спецификации OASIS.  Значение `SAML2` обозначает веб-маркер SAML 2.0 по спецификации OASIS. |
| OutputTokenFormat | 0:1 | Формат выходного маркера. Возможные значения: `JSON`, `JWT`, `SAML11` или `SAML2`. |
| CryptographicKeys | 0:1 | Список криптографических ключей, используемых в техническом профиле. |
| InputClaimsTransformations | 0:1 | Список ссылок на предварительно определенные преобразования утверждений, которые должны быть выполнены перед отправкой утверждений поставщику утверждений или проверяющей стороне. |
| InputClaims | 0:1 | Список ссылок на предварительно определенные типы утверждений, которые принимаются как входные данные для технического профиля. |
| PersistedClaims | 0:1 | Список ссылок на предварительно определенные типы утверждений, которые сохраняются поставщиком утверждений для технического профиля. |
| OutputClaims | 0:1 | Список ссылок на предварительно определенные типы утверждений, которые используются как выходные данные для технического профиля. |
| OutputClaimsTransformations | 0:1 | Список ссылок на предварительно определенные преобразования утверждений, которые должны быть выполнены после получения утверждений от поставщика утверждений. |
| ValidationTechnicalProfiles | 0:n | Список ссылок на другие технические профили, которые этот технический профиль использует для проверки. Дополнительные сведения см. в [ описании технического профиля проверки](validation-technical-profile.md).|
| SubjectNamingInfo | 0:1 | Управляет созданием имени субъекта, для маркеров, в которых имя субъекта указывается отдельно от утверждений. Например, OAuth или SAML.  |
| IncludeClaimsFromTechnicalProfile | 0:1 | Идентификатор технического профиля, все входные и выходные утверждения из которого нужно добавить в текущий технический профиль. Технический профиль, указанный в этой ссылке, должен быть определен в том же файле политики. |
| IncludeTechnicalProfile |0:1 | Идентификатор технического профиля, все данные из которого нужно добавить в текущий технический профиль. Технический профиль, указанный в этой ссылке, должен существовать в том же файле политики. |
| UseTechnicalProfileForSessionManagement | 0:1 | Другой технический профиль, который нужно использовать для управления сеансами. |
|EnabledForUserJourneys| 0:1 |Указывает, выполняется ли технический профиль в пути взаимодействия пользователя.  |

### <a name="protocol"></a>Протокол

Элемент **Protocol** содержит следующие атрибуты.

| Атрибут | Обязательно для заполнения | ОПИСАНИЕ |
| --------- | -------- | ----------- |
| Name | Да | Имя допустимого протокола, поддерживаемого в Azure AD B2C и используемого в составе технического профиля. Возможные значения: `OAuth1`, `OAuth2`, `SAML2`, `OpenIdConnect`, `WsFed`, `WsTrust`, `Proprietary`, `session management`, `self-asserted` или `None`. |
| Обработчик | Нет | Если имя протокола имеет значение `Proprietary`, здесь должно быть указано полное имя сборки, которую Azure AD B2C использует для определения обработчика протокола. |

### <a name="metadata"></a>Метаданные

Элемент **Metadata** содержит следующие элементы.

| Элемент | Вхождения | Описание |
| ------- | ----------- | ----------- |
| Элемент | 0:n | Метаданные, относящиеся к техническому профилю. Каждый тип технического профиля имеет свой набор элементов метаданных. Дополнительные сведения см. в разделе о типах технических профилей. |

#### <a name="item"></a>Элемент

Элемент **Item** из элемента **Metadata** содержит следующие атрибуты:

| Атрибут | Обязательно для заполнения | Описание |
| --------- | -------- | ----------- |
| Ключ | Да | Ключ метаданных. Список элементов метаданных см. в описании каждого типа технических профилей. |

### <a name="cryptographickeys"></a>CryptographicKeys

Элемент **CryptographicKeys** содержит следующие элементы:

| Элемент | Вхождения | Описание |
| ------- | ----------- | ----------- |
| Ключ | 1:n | Криптографический ключ, используемый в этом техническом профиле. |

#### <a name="key"></a>Ключ

Элемент **Key** содержит следующие атрибуты:

| Атрибут | Обязательно для заполнения | Описание |
| --------- | -------- | ----------- |
| Идентификатор | Нет | Уникальный идентификатор конкретной пары ключей, который можно указывать в других элементах файла политики. |
| StorageReferenceId | Да | Идентификатор контейнера хранения ключей, который можно указывать в других элементах файла политики. |

### <a name="inputclaimstransformations"></a>InputClaimsTransformations

Элемент **InputClaimsTransformations** содержит следующий элемент:

| Элемент | Вхождения | Описание |
| ------- | ----------- | ----------- |
| InputClaimsTransformation | 1:n | Идентификатор преобразования утверждений, который должен быть выполнен перед отправкой утверждений поставщику утверждений или проверяющей стороне. Преобразование утверждений позволяет изменить существующие утверждения ClaimsSchema или создать новые. |

#### <a name="inputclaimstransformation"></a>InputClaimsTransformation

Элемент **InputClaimsTransformation** содержит следующий атрибут:

| Атрибут | Обязательно для заполнения | Описание |
| --------- | -------- | ----------- |
| ReferenceId | Да | Идентификатор преобразования утверждений, который уже определен в файле политики или файле родительской политики. |

### <a name="inputclaims"></a>InputClaims

Элемент **InputClaims** содержит следующий элемент:

| Элемент | Вхождения | Описание |
| ------- | ----------- | ----------- |
| InputClaim | 1:n | Ожидаемый тип входящего утверждения. |

#### <a name="inputclaim"></a>InputClaim

Элемент **InputClaim** содержит следующие атрибуты:

| Атрибут | Обязательно для заполнения | Описание |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Да | Идентификатор типа утверждения, который уже определен в разделе ClaimsSchema файла политики или файла родительской политики. |
| DefaultValue | Нет | Значение по умолчанию, которое будет использоваться для создания утверждения, если указанное в ClaimTypeReferenceId утверждение не существует, чтобы технический профиль мог использовать созданное утверждение в качестве InputClaim. |
| PartnerClaimType | Нет | Идентификатор типа утверждения внешнего партнера, с которым сопоставляется указанный тип утверждения политики. Если атрибут PartnerClaimType не указан, тип утверждения указанной политики сопоставляется с одноименным типом утверждения партнера. Это свойство используется, если стороны используют разные имена типов утверждения. Например, вы используете утверждение с именем givenName, а ваш партнер — утверждение с именем first_name. |

### <a name="persistedclaims"></a>PersistedClaims

Элемент **PersistedClaims** содержит следующие элементы:

| Элемент | Вхождения | Описание |
| ------- | ----------- | ----------- |
| PersistedClaim | 1:n | Тип утверждения, который нужно сохранять. |

#### <a name="persistedclaim"></a>PersistedClaim

Элемент **PersistedClaim** содержит следующие атрибуты:

| Атрибут | Обязательно для заполнения | Описание |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Да | Идентификатор типа утверждения, который уже определен в разделе ClaimsSchema файла политики или файла родительской политики. |
| DefaultValue | Нет | Значение по умолчанию, которое будет использоваться для создания утверждения, если указанное в ClaimTypeReferenceId утверждение не существует, чтобы технический профиль мог использовать созданное утверждение в качестве InputClaim. |
| PartnerClaimType | Нет | Идентификатор типа утверждения внешнего партнера, с которым сопоставляется указанный тип утверждения политики. Если атрибут PartnerClaimType не указан, тип утверждения указанной политики сопоставляется с одноименным типом утверждения партнера. Это свойство используется, если стороны используют разные имена типов утверждения. Например, вы используете утверждение с именем givenName, а ваш партнер — утверждение с именем first_name. |

### <a name="outputclaims"></a>OutputClaims

Элемент **PersistedClaim** содержит следующие элементы:

| Элемент | Вхождения | Описание |
| ------- | ----------- | ----------- |
| outputClaim | 1:n | Ожидаемый тип исходящего утверждения. |

#### <a name="outputclaim"></a>OutputClaim

Элемент **OutputClaim** содержит следующие атрибуты:

| Атрибут | Обязательно для заполнения | Описание |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Да | Идентификатор типа утверждения, который уже определен в разделе ClaimsSchema файла политики или файла родительской политики. |
| DefaultValue | Нет | Значение по умолчанию, которое будет использоваться для создания утверждения, если указанное в ClaimTypeReferenceId утверждение не существует, чтобы технический профиль мог использовать созданное утверждение в качестве InputClaim. |
|AlwaysUseDefaultValue |Нет |Принудительное использование значения по умолчанию.  |
| PartnerClaimType | Нет | Идентификатор типа утверждения внешнего партнера, с которым сопоставляется указанный тип утверждения политики. Если атрибут PartnerClaimType не указан, тип утверждения указанной политики сопоставляется с одноименным типом утверждения партнера. Это свойство используется, если стороны используют разные имена типов утверждения. Например, вы используете утверждение с именем givenName, а ваш партнер — утверждение с именем first_name. |

### <a name="outputclaimstransformations"></a>OutputClaimsTransformations

Элемент **OutputClaimsTransformations** содержит следующий элемент:

| Элемент | Вхождения | Описание |
| ------- | ----------- | ----------- |
| OutputClaimsTransformation | 1:n | Идентификаторы преобразований утверждений, которые должны быть выполнены перед отправкой утверждений поставщику утверждений или проверяющей стороне. Преобразование утверждений позволяет изменить существующие утверждения ClaimsSchema или создать новые. |

#### <a name="outputclaimstransformation"></a>OutputClaimsTransformation

Элемент **OutputClaimsTransformations** содержит следующий атрибут:

| Атрибут | Обязательно для заполнения | Описание |
| --------- | -------- | ----------- |
| ReferenceId | Да | Идентификатор преобразования утверждений, который уже определен в файле политики или файле родительской политики. |

### <a name="validationtechnicalprofiles"></a>ValidationTechnicalProfiles

Элемент **ValidationTechnicalProfiles** содержит следующий элемент:

| Элемент | Вхождения | Описание |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1:n | Идентификаторы технических профилей, которые используются для проверки некоторых или всех выходных утверждений технического профиля, в который включен этот элемент. Все входящие утверждения указанного технического профиля должны присутствовать в списке выходных утверждений технического профиля, в который включен этот элемент. |

#### <a name="validationtechnicalprofile"></a>ValidationTechnicalProfile

Элемент **ValidationTechnicalProfile** содержит следующий атрибут:

| Атрибут | Обязательно для заполнения | Описание |
| --------- | -------- | ----------- |
| ReferenceId | Да | Идентификатор технического профиля, который уже определен в файле политики или файле родительской политики. |

###  <a name="subjectnaminginfo"></a>SubjectNamingInfo

Элемент **SubjectNamingInfo** содержит следующий атрибут:

| Атрибут | Обязательно для заполнения | Описание |
| --------- | -------- | ----------- |
| ClaimType | Да | Идентификатор типа утверждения, который уже определен в разделе ClaimsSchema файла политики. |

### <a name="includetechnicalprofile"></a>IncludeTechnicalProfile

Элемент **IncludeTechnicalProfile** содержит следующий атрибут:

| Атрибут | Обязательно для заполнения | Описание |
| --------- | -------- | ----------- |
| ReferenceId | Да | Идентификатор технического профиля, который уже определен в файле политики или файле родительской политики. |

### <a name="usetechnicalprofileforsessionmanagement"></a>UseTechnicalProfileForSessionManagement

Элемент **UseTechnicalProfileForSessionManagement** содержит следующий атрибут:

| Атрибут | Обязательно для заполнения | Описание |
| --------- | -------- | ----------- |
| ReferenceId | Да | Идентификатор технического профиля, который уже определен в файле политики или файле родительской политики. |

### <a name="enabledforuserjourneys"></a>EnabledForUserJourneys
**ClaimsProviderSelections** в пути взаимодействия пользователя определяет список и порядок расположения вариантов для выбора поставщика утверждений. Элемент **EnabledForUserJourneys** позволяет отфильтровать доступных для пользователя поставщиков утверждений. Элемент **EnabledForUserJourneys** содержит одно из следующих значений:

- **Always** обозначает, что технический профиль выполняется.
- **Never** обозначает, что технический профиль пропускается.
- **OnClaimsExistence** указывает, что выполнение происходит только при наличии в техническом профиле определенного утверждения.
- **OnItemExistenceInStringCollectionClaim** указывает, что выполнение происходит только при наличии элемента в утверждении коллекции строк.
- **OnItemAbsenceInStringCollectionClaim** указывает, что выполнение происходит только при отсутствии элемента в утверждении коллекции строк.

С помощью **OnClaimsExistence**, **OnItemExistenceInStringCollectionClaim** или **OnItemAbsenceInStringCollectionClaim** вы должны указать следующие метаданные: **ClaimTypeOnWhichToEnable** указывает тип утверждения, который будет вычисляться, **ClaimValueOnWhichToEnable** определяет значение для сравнения.

Следующий технический профиль выполняется, только когда коллекция строк **identityProviders** содержит значение `facebook.com`:

```XML
<TechnicalProfile Id="UnLink-Facebook-OAUTH">
  <DisplayName>Unlink Facebook</DisplayName>
...
    <Metadata>
      <Item Key="ClaimTypeOnWhichToEnable">identityProviders</Item>
      <Item Key="ClaimValueOnWhichToEnable">facebook.com</Item>
    </Metadata>
...
  <EnabledForUserJourneys>OnItemExistenceInStringCollectionClaim</EnabledForUserJourneys>
</TechnicalProfile>
```
