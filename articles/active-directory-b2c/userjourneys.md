---
title: Элемент UserJourneys | Документация Майкрософт
description: Сведения об указании элемента UserJourneys настраиваемой политики в Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/04/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 189343888d2856a6945723c030485e58394c912f
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82559598"
---
# <a name="userjourneys"></a>UserJourneys

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В путях взаимодействия пользователя указываются явные пути, через которые политика позволяет приложению, основанному на утверждениях, предоставлять пользователю требуемые утверждения. Пользователь проходит через эти пути, чтобы извлечь утверждения, которые необходимо предоставить проверяющей стороне. Другими словами, пути взаимодействия пользователя определяют бизнес-логику, в соответствии с которой пользователь выполняет действия, когда инфраструктура процедур идентификации Azure Active Directory B2C обрабатывает запрос.

Эти пути взаимодействия пользователя можно рассматривать как шаблоны, доступные для удовлетворения основных потребностей различных проверяющих сторон сообщества. Пути взаимодействия пользователя упрощают определение части политики, которая является проверяющей стороной. Политика может определять несколько путей взаимодействия пользователя. Каждый путь взаимодействия пользователя представляет собой последовательность шагов оркестрации.

Чтобы определить пути взаимодействия пользователя, поддерживаемые этой политикой, необходимо добавить элемент **UserJourneys** в элемент верхнего уровня файла политики.

Элемент **UserJourneys** содержит следующий элемент:

| Элемент | Вхождения | Описание |
| ------- | ----------- | ----------- |
| UserJourney | 1:n | Путь взаимодействия пользователя, определяющий все конструкции, необходимые для полного потока пользователя. |

Элемент **UserJourney** содержит следующий атрибут.

| Атрибут | Обязательный | Описание |
| --------- | -------- | ----------- |
| Идентификатор | Да | Идентификатор пути взаимодействия пользователя, который можно использовать для ссылки на этот путь из других элементов в политике. Элемент **DefaultUserJourney**[политики проверяющей стороны](relyingparty.md) указывает на этот атрибут. |

Элемент **UserJourney** содержит следующие элементы.

| Элемент | Вхождения | Описание |
| ------- | ----------- | ----------- |
| OrchestrationSteps | 1:n | Последовательность оркестрации, которая должна соблюдаться для успешного выполнения транзакции. Каждый путь взаимодействия пользователя состоит из упорядоченного списка шагов оркестрации, которые должны выполняться в соответствующей последовательности. Если какой-либо шаг завершится ошибкой, выполнение транзакции завершается сбоем. |

## <a name="orchestrationsteps"></a>OrchestrationSteps

Путь взаимодействия пользователя представляется в виде последовательности оркестрации, которая должна быть соблюдена для успешного выполнения транзакции. Если какой-либо шаг завершится ошибкой, выполнение транзакции завершается сбоем. Эти шаги оркестрации касаются и стандартных блоков, и поставщиков утверждений, разрешенных в файле политики. В любом шаге оркестрации, отвечающем за отображение и преобразование для просмотра в пользовательском интерфейсе, также содержится ссылка на соответствующий идентификатор определения содержимого.

Шаги оркестрации можно условно выполнять на основе предусловий, определенных в элементе orchestration Step. Например, можно проверить выполнение шага оркестрации только в том случае, если существуют определенные утверждения или если утверждение равно или не соответствует указанному значению.

Чтобы указать упорядоченный список шагов оркестрации, необходимо добавить элемент **OrchestrationSteps** как часть политики. Этот элемент является обязательным.

Элемент **OrchestrationSteps** содержит следующий элемент:

| Элемент | Вхождения | Описание |
| ------- | ----------- | ----------- |
| OrchestrationStep | 1:n | Упорядоченный шаг оркестрации. |

Элемент **OrchestrationStep** содержит следующие атрибуты.

| Атрибут | Обязательный | Описание |
| --------- | -------- | ----------- |
| `Order` | Да | Порядок шагов оркестрации. |
| `Type` | Да | Тип шага оркестрации. Возможные значения: <ul><li>**ClaimsProviderSelection** — указывает, что шаг оркестрации предоставляет пользователю различные варианты поставщиков утверждений, из которых нужно выбрать одного.</li><li>**CombinedSignInAndSignUp** — указывает, что на шаге оркестрации отображается общая страница для входа поставщика социальных сетей и для регистрации локальной учетной записи.</li><li>**ClaimsExchange** — указывает, что на шаге оркестрации выполняется обмен утверждениями с поставщиком утверждений.</li><li>Запросы **на выработку — указывает** , что этап оркестрации должен обрабатывать данные утверждений, отправленные Azure AD B2C от проверяющей стороны `InputClaims` через его конфигурацию.</li><li>**SendClaims** — указывает, что на данном шаге оркестрации проверяющей стороне отправляется утверждение с маркером, выданным издателем утверждений.</li></ul> |
| ContentDefinitionReferenceId | нет | Идентификатор [определения содержимого](contentdefinitions.md), связанного с этим шагом оркестрации. Обычно идентификатор ссылки на определение содержимого определяется в техническом профиле с самостоятельным подтверждением. Однако иногда в Azure AD B2C требуется показать некоторые данные, не используя технический профиль. Существует два примера. Если тип шага оркестрации является одним из следующих: `ClaimsProviderSelection` или `CombinedSignInAndSignUp`, Azure AD B2C необходимо отобразить Выбор поставщика удостоверений без технического профиля. |
| CpimIssuerTechnicalProfileReferenceId | нет | Тип шага этапа оркестрации — `SendClaims`. Это свойство определяет идентификатор технического профиля поставщика утверждений, выдающего маркер для проверяющей стороны.  Если это значение не указано, маркер проверяющей стороны не создается. |


Элемент **OrchestrationStep** может содержать следующие элементы.

| Элемент | Вхождения | Описание |
| ------- | ----------- | ----------- |
| Preconditions | 0:n | Список предварительных условий, которые необходимо соблюдать для выполнения шага оркестрации. |
| ClaimsProviderSelections | 0:n | Список возможных поставщиков утверждений для шага оркестрации. |
| ClaimsExchanges | 0:n | Список обмена утверждениями для шага оркестрации. |

### <a name="preconditions"></a>Preconditions

Элемент **Preconditions** содержит следующий элемент:

| Элемент | Вхождения | Описание |
| ------- | ----------- | ----------- |
| Precondition | 1:n | В зависимости от используемого технического профиля, этот элемент перенаправляет клиента к выбранному поставщику утверждений либо выполняет вызов сервера для обмена утверждениями. |


#### <a name="precondition"></a>Precondition

Элемент **предусловия** содержит следующие атрибуты:

| Атрибут | Обязательный | Описание |
| --------- | -------- | ----------- |
| `Type` | Да | Тип выполняемой проверки или запроса для данного предварительного условия. Для этого элемента можно установить значение **ClaimsExist**, которое указывает, что действия должны выполняться, если указанные утверждения существуют в текущем наборе утверждений пользователя, или значение **ClaimEquals**, указывающее на то, что действия следует выполнить, если указанное утверждение существует и его значение соответствует указанному. |
| `ExecuteActionsIf` | Да | Выполнение теста true or false (true или false) с целью понять, следует ли выполнять предварительное условие. |

Элементы **Precondition** содержат следующие элементы:

| Элемент | Вхождения | Описание |
| ------- | ----------- | ----------- |
| Значение | 1:n | Атрибут ClaimTypeReferenceId, для которого необходимо выполнить запрос. Другой элемент значения содержит значение, для которого необходимо выполнить проверку.</li></ul>|
| Действие | 1:1 | Действие, которое требуется выполнить, если в результате проверки предварительного условия в рамках шага оркестрации получено значение true. Если для `Action` присвоено значение `SkipThisOrchestrationStep`, не нужно выполнять сопоставленный элемент `OrchestrationStep`. |

#### <a name="preconditions-examples"></a>Примеры предварительных условий

Следующие предварительные условия позволяют проверить, существует ли элемент objectId пользователя. В пути взаимодействия пользователь выбрал способ входа с использованием локальной учетной записи. При наличии элемента objectId можно пропустить этот шаг оркестрации.

```XML
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Следующие предварительные условия позволяют проверить, выполнил ли пользователь вход с помощью учетной записи социальной сети. Предпринята попытка найти учетную запись пользователя в каталоге. Если пользователь входит в систему или регистрируется с локальной учетной записью, пропустите этот этап оркестрации.

```XML
<OrchestrationStep Order="3" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
      <Value>authenticationSource</Value>
      <Value>localAccountAuthentication</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="AADUserReadUsingAlternativeSecurityId" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId-NoError" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Элемент Preconditions позволяет проверить несколько предварительных условий. В следующем примере проверяется, существуют ли элементы objectId или email. Если первое условие истинно, путешествие переходит к следующему этапу оркестрации.

```XML
<OrchestrationStep Order="4" Type="ClaimsExchange">
  <Preconditions>
  <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>email</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="SelfAsserted-SocialEmail" TechnicalProfileReferenceId="SelfAsserted-SocialEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claimsproviderselection"></a>ClaimsProviderSelection

Этап оркестрации типа `ClaimsProviderSelection` или `CombinedSignInAndSignUp` может содержать список поставщиков утверждений, с помощью которых пользователь может войти в систему. Порядок внутри элементов `ClaimsProviderSelections` определяет порядок, в котором поставщики удостоверений представляются пользователю.

Элемент **клаимспровидерселектионс** содержит следующий элемент:

| Элемент | Вхождения | Описание |
| ------- | ----------- | ----------- |
| ClaimsProviderSelection | 1:n | Предоставляет список доступных для выбора поставщиков утверждений.|

Элемент **клаимспровидерселектионс** содержит следующие атрибуты:

| Атрибут | Обязательный | Описание |
| --------- | -------- | ----------- |
| дисплайоптион| нет | Управляет поведением случая, когда доступен один из вариантов выбора поставщика утверждений. Возможные значения: `DoNotShowSingleProvider` (по умолчанию) пользователь сразу же перенаправляется в федеративный поставщик удостоверений. Или `ShowSingleProvider` Azure AD B2C представляет страницу входа с выбором поставщика удостоверений. Чтобы использовать этот атрибут, [версия определения содержимого](page-layout.md) должна быть `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0` и выше.|

Элемент **ClaimsProviderSelection** содержит следующие атрибуты.

| Атрибут | Обязательный | Описание |
| --------- | -------- | ----------- |
| TargetClaimsExchangeId | нет | Идентификатор обмена утверждениями, выполняемого на следующем шаге оркестрации выбора поставщика утверждений. Можно задать только данный атрибут или атрибут ValidationClaimsExchangeId, но не оба. |
| ValidationClaimsExchangeId | нет | Идентификатор обмена утверждениями, выполняемого на текущем шаге оркестрации для проверки выбора поставщика утверждений. Можно задать только данный атрибут или атрибут TargetClaimsExchangeId, но не оба. |

### <a name="claimsproviderselection-example"></a>Пример ClaimsProviderSelection

На следующем этапе оркестрации пользователь может выбрать вход с помощью Facebook, LinkedIn, Twitter, Google или локальной учетной записи. Если пользователь выбирает один из поставщиков удостоверений социальных сетей, второй шаг оркестрации выполняется с выбранным обменом утверждениями, указанном в атрибуте `TargetClaimsExchangeId`. Второй шаг оркестрации перенаправляет пользователя к поставщику удостоверений социальных сетей для завершения процесса входа в систему. Если пользователь решит выполнить вход с использованием локальной учетной записи, Azure AD B2C остается на том же шаге оркестрации (той же странице регистрации или входа в систему) и пропускает второй этап оркестрации.

```XML
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
    <ClaimsProviderSelections>
    <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
    <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />
    </ClaimsProviderSelections>
    <ClaimsExchanges>
    <ClaimsExchange Id="LocalAccountSigninEmailExchange"
                    TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
    </ClaimsExchanges>
</OrchestrationStep>


<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claimsexchanges"></a>ClaimsExchanges

Элемент **ClaimsExchanges** содержит следующий элемент:

| Элемент | Вхождения | Описание |
| ------- | ----------- | ----------- |
| ClaimsExchange | 1:n | В зависимости от используемого технического профиля, этот элемент перенаправляет клиента в соответствии с выбранным элементом ClaimsProviderSelection либо выполняет вызов сервера для обмена утверждениями. |

Элемент **ClaimsExchange** содержит следующие атрибуты.

| Атрибут | Обязательный | Описание |
| --------- | -------- | ----------- |
| Идентификатор | Да | Идентификатор шага обмена утверждениями. Этот идентификатор используется для ссылки на обмен утверждениями из шага выбора поставщика утверждений в политике. |
| TechnicalProfileReferenceId | Да | Идентификатор технического профиля, который необходимо выполнить. |
