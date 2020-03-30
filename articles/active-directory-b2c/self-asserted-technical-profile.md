---
title: Определение самоутвержденного технического профиля в пользовательской политике
titleSuffix: Azure AD B2C
description: Определение самоподтвержденного технического профиля в настраиваемой политике в Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2b29b8b0975639e5c5315a55e1382794d7662665
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332511"
---
# <a name="define-a-self-asserted-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Определение самоподтвержденного технического профиля в настраиваемой политике Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Все взаимодействия в Azure Active Directory B2C (Azure AD B2C), где пользователь должен предоставлять входные данные, являются самоутверждаемыми техническими профилями. Например, страница регистрации, страница входа, страница сброса пароля.

## <a name="protocol"></a>Протокол

Атрибуту **Name** элемента **Protocol** необходимо присвоить значение `Proprietary`. Атрибут **handler** должен содержать полное имя сборки обработчика протокола, которое используется Azure AD B2C, для самоподтверждения: `Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

В следующем примере показан самоподтвержденный технический профиль для регистрации по электронной почте:

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
```

## <a name="input-claims"></a>Входящие утверждения

В самоутвержденном техническом профиле можно использовать элементы **InputClaims** и **InputClaimsTransformations** для предварительного заполнения значения претензий, которые отображаются на самоутверждается странице (претензии отображения). Например, в политике изменения профиля путь взаимодействия пользователя сначала считывает профиль пользователя из службы каталогов Azure AD B2C, а затем самоподтвержденный технический профиль задает входящие утверждения с помощью пользовательских данных, хранящихся в профиле пользователя. Эти утверждения собираются из профиля пользователя, а затем представляются пользователю, который после этого может изменить существующие данные.

```XML
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <InputClaim ClaimTypeReferenceId="userPrincipalName" />
    <InputClaim ClaimTypeReferenceId="givenName" />
    <InputClaim ClaimTypeReferenceId="surname" />
  </InputClaims>
```

## <a name="display-claims"></a>Отображение претензий

Функция претензий от отображения в настоящее время находится в **предварительном просмотре.**

Элемент **DisplayClaims** содержит список претензий, которые будут представлены на экране для сбора данных от пользователя. Для предварительного заполнения значений утверждений отображения используйте ранее описанные требования ввода. Элемент также может содержать значение по умолчанию.

Порядок претензий в **DisplayClaims** определяет порядок, в котором Azure AD B2C отображает претензии на экране. Чтобы заставить пользователя предоставить значение для конкретной претензии, установите `true` **обязательный** атрибут элемента **DisplayClaim.**

Элемент **ClaimType** в коллекции **DisplayClaims** должен настроить элемент **UserInputType** на любой тип ввода пользователя, поддерживаемый Azure AD B2C. Например, `TextBox` или `DropdownSingleSelect`.

### <a name="add-a-reference-to-a-displaycontrol"></a>Добавить ссылку на DisplayControl

В коллекцию претензий дисплея можно включить ссылку на созданный [вами DisplayControl.](display-controls.md) Элемент управления дисплеем — это элемент пользовательского интерфейса, который имеет специальную функциональность и взаимодействует с бэк-эндом Azure AD B2C. Это позволяет пользователю выполнять действия на странице, которые вызывают технический профиль проверки в задней части. Например, проверка адреса электронной почты, номера телефона или номера лояльности клиентов.

Следующий `TechnicalProfile` пример иллюстрирует использование претензий отображения с элементами управления дисплеем.

* Первая претензия дисплея `emailVerificationControl` делает ссылку на элемент управления дисплеем, который собирает и проверяет адрес электронной почты.
* Пятая претензия дисплея `phoneVerificationControl` делает ссылку на элемент управления дисплеем, который собирает и проверяет номер телефона.
* Другими претензиями отображения являются ClaimTypes, которые должны быть собраны у пользователя.

```XML
<TechnicalProfile Id="Id">
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim DisplayControlReferenceId="phoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
</TechnicalProfile>
```

Как уже упоминалось, претензия дисплея со ссылкой на элемент управления дисплеем может запустить свою собственную проверку, например проверку адреса электронной почты. Кроме того, самоутвержденная страница поддерживает использование технического профиля проверки для проверки всей страницы, включая любой пользовательский вход (типы претензий или элементы управления дисплеем), прежде чем перейти к следующему этапу оркестровки.

### <a name="combine-usage-of-display-claims-and-output-claims-carefully"></a>Тщательно комбинируйте использование претензий отображения и выходных данных

Если вы указали один или несколько элементов **DisplayClaim** в самоутвержденном техническом профиле, необходимо использовать DisplayClaim для *каждого* утверждения, которое вы хотите отобразить на экране и собрать у пользователя. Претензии вывода не отображаются самоутверждаемым техническим профилем, содержащим по крайней мере одну претензию дисплея.

Рассмотрим следующий пример, в котором претензия `age` определяется как претензия на **вывод** ные в базовой политике. Перед добавлением каких-либо претензий от `age` отображения в самоутверждаемый технический профиль претензия отображается на экране для сбора данных от пользователя:

```XML
<TechnicalProfile Id="id">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="age" />
  </OutputClaims>
</TechnicalProfile>
```

Если политика листа, которая наследует эту `officeNumber` базу, впоследствии определяется как **претензия дисплея:**

```XML
<TechnicalProfile Id="id">
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="officeNumber" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="officeNumber" />
  </OutputClaims>
</TechnicalProfile>
```

Претензия `age` в базовой политике больше не представлена на экране пользователю - она фактически "скрыта". Чтобы отобразить претензию `age` и получить от пользователя `age` возрастное значение, необходимо добавить **DisplayClaim.**

## <a name="output-claims"></a>Исходящие утверждения

Элемент **OutputClaims** содержит список претензий, которые должны быть возвращены на следующий этап оркестровки. Атрибут **DefaultValue** вступает в силу только в том случае, если претензия никогда не была установлена. Если он был установлен на предыдущем этапе оркестровки, значение по умолчанию не вступит в силу, даже если пользователь оставляет значение пустым. Чтобы принудительно использовать значение по умолчанию, задайте атрибуту **AlwaysUseDefaultValue** значение `true`.

По соображениям безопасности значение`UserInputType` требования `Password`пароля (установленное на) доступно только для технических профилей проверки самоутверждения технического профиля. Вы не можете использовать претензию пароля в следующих шагах оркестровки. 

> [!NOTE]
> В предыдущих версиях Рамочной системы опыта идентификации (IEF) претензии на выводные данные использовались для сбора данных от пользователя. Для сбора данных от пользователя используйте коллекцию **DisplayClaims.**

Элемент **OutputClaimsTransformations** может содержать коллекцию элементов **OutputClaimsTransformation**, которые используются для изменения исходящих утверждений или создания новых.

### <a name="when-you-should-use-output-claims"></a>Когда следует использовать выходные претензии

В самоутвержденном техническом профиле сбор претензий вывода возвращает претензии следующему этапу оркестровки.

Используйте выходные претензии, когда:

- **Претензии вывесываются по преобразованию выходных требований.**
- **Установка значения по умолчанию в выходной претензии** без сбора данных от пользователя или возврата данных из технического профиля проверки. Самоподтвержденный технический профиль `LocalAccountSignUpWithLogonEmail` задает утверждению **executed-SelfAsserted-Input** значение `true`.
- **Технический профиль проверки возвращает исходящие утверждения**. Технический профиль может вызывать технический профиль проверки, который возвращает некоторые утверждения. Вы можете перенаправить утверждения и вернуть их на дальнейшие шаги оркестрации на пути взаимодействия пользователя. Например, при входе в систему с помощью локальной учетной записи самоподтвержденный технический профиль с именем `SelfAsserted-LocalAccountSignin-Email` вызывает технический профиль проверки с именем `login-NonInteractive`. Этот технический профиль проверяет учетные данные пользователя и возвращает профиль пользователя. Например, userPrincipalName, displayName, givenName и surName.
- **Контроль отображения возвращает выходные претензии** - Ваш технический профиль может иметь ссылку на [элемент управления дисплеем.](display-controls.md) Контроль отображения возвращает некоторые претензии, такие как проверенный адрес электронной почты. Вы можете перенаправить утверждения и вернуть их на дальнейшие шаги оркестрации на пути взаимодействия пользователя. Функция управления дисплеем в настоящее время находится в **предварительном просмотре.**

Следующий пример демонстрирует использование самоутвержденного технического профиля, в который используются как претензии отображения, так и претензии вывода.

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    <Item Key="language.button_continue">Create</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" />
  </InputClaims>
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="SecondaryEmailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" Required="true" />
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" />
    <OutputClaim ClaimTypeReferenceId="newUser" />
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
  </ValidationTechnicalProfiles>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>
```

## <a name="persist-claims"></a>Сохранение утверждений

Элемент PersistedClaims не используется. Самоутверждаемый технический профиль не упорствует в данных Azure AD B2C. Вместо этого выполняется вызов к техническому профилю проверки, который отвечает за сохранение данных. Например, политика регистрации использует самоподтвержденный технический профиль `LocalAccountSignUpWithLogonEmail` для сбора нового пользовательского профиля. Технический профиль `LocalAccountSignUpWithLogonEmail` вызывает технический профиль проверки для создания учетной записи в Azure AD B2C.

## <a name="validation-technical-profiles"></a>Технические профили проверки

Технический профиль проверки используется для проверки некоторых или всех исходящих утверждений эталонного технического профиля. Входящие утверждения технического профиля проверки должны появиться в исходящих утверждениях самоподтвержденного технического профиля. Технический профиль проверки проверяет входные данные пользователя и возвращает ошибку пользователю.

Техническим профилем проверки может быть любой технический профиль в политике, например технические профили [Azure Active Directory](active-directory-technical-profile.md) или [REST API](restful-technical-profile.md). В предыдущем примере технический профиль `LocalAccountSignUpWithLogonEmail` проверяет отсутствие имени signinName в каталоге. Если оно отсутствует, технический профиль проверки создает локальную учетную запись и возвращает objectId, authenticationSource и newUser. Технический профиль `SelfAsserted-LocalAccountSignin-Email` вызывает технический профиль проверки `login-NonInteractive` для проверки учетных данных пользователя.

Можно также вызывать технический профиль REST API с бизнес-логикой, перезаписывать входящие утверждения или дополнять пользовательские данные за счет дальнейшей интеграции с бизнес-приложением корпоративного уровня. Дополнительные сведения см. в статье [о техническом профиле проверки](validation-technical-profile.md).

## <a name="metadata"></a>Метаданные

| Атрибут | Обязательно | Описание |
| --------- | -------- | ----------- |
| setting.operatingMode <sup>1</sup>| нет | Для страницы входа в систему это свойство управляет поведением поля имени пользователя, таким как проверка входных данных и вывод сообщений об ошибках. Ожидаемые значения: `Username` или `Email`.  |
| РазрешитьПоколениеПретензииСНуллВДовые| нет| Разрешить генерировать претензию с нулевой стоимостью. Например, в случае, если пользователь не выбирает флажок.|
| ContentDefinitionReferenceId | Да | Идентификатор [определения содержимого](contentdefinitions.md), связанного с этим техническим профилем. |
| EnforceEmailVerification | нет | При регистрации или изменении профиля применяет проверку по электронной почте. Возможные значения: `true` (по умолчанию) или `false`. |
| setting.retryLimit | нет | Контролирует количество раз, когда пользователь может попытаться предоставить данные, которые проверяются на техническом профиле проверки. Например, пользователь пытается зарегистрироваться с помощью учетной записи, которая уже существует, и продолжает попытки до достижения предела.
| SignUpTarget <sup>1</sup>| нет | Идентификатор обмена целевого объекта регистрации. Когда пользователь нажимает кнопку "Регистрация", Azure AD B2C выполняет указанный идентификатор обмена. |
| setting.showCancelButton | нет | Отображает кнопку "Отмена". Возможные значения: `true` (по умолчанию) или `false`. |
| setting.showContinueButton | нет | Отображает кнопку "Продолжить". Возможные значения: `true` (по умолчанию) или `false`. |
| setting.showSignupLink <sup>2</sup>| нет | Отображает кнопку "Регистрация". Возможные значения: `true` (по умолчанию) или `false`. |
| setting.forgotPasswordLinkLocation <sup>2</sup>| нет| Отображает запозываемую ссылку на пароль. Возможные значения: `AfterInput` (по умолчанию) ссылка отображается в `None` нижней части страницы, или удаляет запоздалую ссылку пароля.|
| setting.enableRememberMe <sup>2</sup>| нет| Отображает [Держите меня подписали в](custom-policy-keep-me-signed-in.md) флажке. Возможные значения: `true` `false` или (по умолчанию). |
| IncludeclaimРазрешениеОбработкапретензий  | нет | Для претензий на ввод и вывод ы указывается, включено ли [разрешение претензий](claim-resolver-overview.md) в технический профиль. Возможные значения: `true` `false`  или (по умолчанию). Если вы хотите использовать решатели претензий в `true`техническом профиле, установите это на . |

Примечания.
1. Доступно для определения содержания `unifiedssp` [DataUri](contentdefinitions.md#datauri) типа , или `unifiedssd`.
1. Доступно для определения содержания `unifiedssp` [DataUri](contentdefinitions.md#datauri) типа , или `unifiedssd`. [Версия макета страницы](page-layout.md) 1.1.0 и выше.

## <a name="cryptographic-keys"></a>Криптографические ключи

Элемент **CryptographicKeys** не используется.
