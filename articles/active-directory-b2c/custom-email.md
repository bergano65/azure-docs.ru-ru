---
title: Пользовательские проверки электронной почты
titleSuffix: Azure AD B2C
description: Узнайте, как настроить электронное письмо с проверкой подлинности, отправляемое клиентам при регистрации для использования приложений с поддержкой Azure AD B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/05/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 2bda00924015bf5abc616b7c346eacfeda53c2ed
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/06/2020
ms.locfileid: "77045942"
---
# <a name="custom-email-verification-in-azure-active-directory-b2c"></a>Настраиваемая проверка электронной почты в Azure Active Directory B2C

Используйте настраиваемый адрес электронной почты в Azure Active Directory B2C (Azure AD B2C) для отправки настраиваемых сообщений электронной почты пользователям, которые подписываются на использование ваших приложений. Используя [дисплайконтролс](display-controls.md) (в настоящее время в предварительной версии) и поставщик электронной почты стороннего поставщика, можно использовать собственный шаблон электронной почты *и адрес и* тему, а также поддерживать локализацию и настраиваемые одноразовые пароли (OTP).

Для пользовательской проверки электронной почты необходимо использовать сторонний поставщик электронной почты, например [SendGrid](https://sendgrid.com) или [SparkPost](https://sparkpost.com), настраиваемый REST API или любой поставщик электронной почты на основе HTTP (включая собственный). В этой статье описывается настройка решения, использующего SendGrid.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-sendgrid-account"></a>Создание учетной записи SendGrid

Если у вас его еще нет, начните с настройки учетной записи SendGrid (клиенты Azure могут разблокировать 25 000 бесплатных сообщений электронной почты каждый месяц). Инструкции по установке см. в разделе [Создание учетной записи SendGrid](../sendgrid-dotnet-how-to-send-email.md#create-a-sendgrid-account) статьи [Отправка электронной почты с помощью SendGrid в Azure](../sendgrid-dotnet-how-to-send-email.md).

Обязательно выполните действия, описанные в разделе [Создание ключа API SendGrid](../sendgrid-dotnet-how-to-send-email.md#to-find-your-sendgrid-api-key). Запишите ключ API для использования на следующем шаге.

## <a name="create-azure-ad-b2c-policy-key"></a>Создание ключа политики Azure AD B2C

Затем сохраните ключ API SendGrid в ключе политики Azure AD B2C, чтобы создать ссылку на них.

1. Войдите на [портал Azure](https://portal.azure.com/).
1. Убедитесь, что вы используете каталог, содержащий клиент Azure AD B2C. В верхнем меню выберите фильтр **каталог и подписка** , а затем выберите Azure AD B2C каталог.
1. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Azure AD B2C**.
1. На странице "Обзор" выберите **Identity Experience Framework**.
1. Выберите **Ключи политики**, а затем щелкните **Добавить**.
1. Для пункта **Параметры** выберите `Manual`.
1. Введите **имя** ключа политики. Например, `SendGridSecret`. Префикс `B2C_1A_` будет автоматически добавлен к имени ключа.
1. В поле **Секрет** введите ранее записанный секрет клиента.
1. Для параметра **Использование ключа** выберите `Signature`.
1. Нажмите кнопку **Создать**.

## <a name="create-sendgrid-template"></a>Создание шаблона SendGrid

После создания учетной записи SendGrid и ключа API SendGrid, хранящегося в ключе политики Azure AD B2C, создайте [динамический шаблон транзакций](https://sendgrid.com/docs/ui/sending-email/how-to-send-an-email-with-dynamic-transactional-templates/)SendGrid.

1. На сайте SendGrid откройте страницу [шаблоны транзакций](https://sendgrid.com/dynamic_templates) и выберите **создать шаблон**.
1. Введите уникальное имя шаблона, например `Verification email`, а затем нажмите кнопку **сохранить**.
1. Чтобы начать редактирование нового шаблона, выберите **Добавить версию**.
1. Выберите **Редактор кода** и **Продолжайте работу**.
1. В редакторе HTML вставьте следующий шаблон HTML или используйте собственный. Параметры `{{otp}}` и `{{email}}` будут динамически заменены значением одноразового пароля и адресом электронной почты пользователя.

    ```HTML
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

    <html xmlns="http://www.w3.org/1999/xhtml" dir="ltr" lang="en"><head id="Head1">
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8"><title>Contoso demo account email verification code</title><meta name="ROBOTS" content="NOINDEX, NOFOLLOW">
       <!-- Template B O365 -->
       <style>
           table td {border-collapse:collapse;margin:0;padding:0;}
       </style>
    </head>
    <body dir="ltr" lang="en">
       <table width="100%" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
            <tr>
               <td valign="top" width="50%"></td>
               <td valign="top">
                  <!-- Email Header -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en" style="border-left:1px solid #e3e3e3;border-right: 1px solid #e3e3e3;">
                   <tr style="background-color: #0072C6;">
                       <td width="1" style="background:#0072C6; border-top:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-top:1px solid #e3e3e3;border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="310" valign="middle" style="border-top:1px solid #e3e3e3; border-bottom:1px solid #e3e3e3;padding:12px 0;">
                           <h1 style="line-height:20pt;font-family:Segoe UI Light; font-size:18pt; color:#ffffff; font-weight:normal;">
                            <span id="HeaderPlaceholder_UserVerificationEmailHeader"><font color="#FFFFFF">Verify your email address</font></span>
                           </h1>
                       </td>
                       <td width="24" style="border-top: 1px solid #e3e3e3;border-bottom: 1px solid #e3e3e3;">&nbsp;</td>
                   </tr>
                  </table>
                  <!-- Email Content -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
                   <tr>
                       <td width="1" style="background:#e3e3e3;"></td>
                       <td width="24">&nbsp;</td>
                       <td id="PageBody" width="640" valign="top" colspan="2" style="border-bottom:1px solid #e3e3e3;padding:10px 0 20px;border-bottom-style:hidden;">
                           <table cellpadding="0" cellspacing="0" border="0">
                               <tr>
                                   <td width="630" style="font-size:10pt; line-height:13pt; color:#000;">
                                       <table cellpadding="0" cellspacing="0" border="0" width="100%" style="" dir="ltr" lang="en">
                                           <tr>
                                               <td>

       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence1">Thanks for verifying your {{email}} account!</span>
       </div>
       <br>
       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333; font-weight: bold">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence2">Your code is: {{otp}}</span>
       </div>
       <br>
       <br>

                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
                                                   Sincerely,
                                                   </div>
                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; font-style:italic; color:#333;">
                                                       Contoso
                                                   </div>
                                               </td>
                                           </tr>
                                       </table>
                                   </td>
                               </tr>
                           </table>

                       </td>

                       <td width="1">&nbsp;</td>
                       <td width="1"></td>
                       <td width="1">&nbsp;</td>
                       <td width="1" valign="top"></td>
                       <td width="29">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3;"></td>
                   </tr>
                   <tr>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td id="PageFooterContainer" width="585" valign="top" colspan="6" style="border-bottom:1px solid #e3e3e3;padding:0px;">


                       </td>

                       <td width="29" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                   </tr>
                  </table>

               </td>
               <td valign="top" width="50%"></td>
           </tr>
       </table>
    <img src="https://mucp.api.account.microsoft.com/m/v2/v?d=AIAACWEPFYXYIUTJIJVV4ST7XLBHVI5MLLYBKJAVXHBDTBHUM5VBSVVPTTVRWDFIXJ5JQTHYOH5TUYIPO4ZAFRFK52UAMIS3UNIPPI7ZJNDZPRXD5VEJBN4H6RO3SPTBS6AJEEAJOUYL4APQX5RJUJOWGPKUABY&amp;i=AIAACL23GD2PFRFEY5YVM2XQLM5YYWMHFDZOCDXUI2B4LM7ETZQO473CVF22PT6WPGR5IIE6TCS6VGEKO5OZIONJWCDMRKWQQVNP5VBYAINF3S7STKYOVDJ4JF2XEW4QQVNHMAPQNHFV3KMR3V3BA4I36B6BO7L4VQUHQOI64EOWPLMG5RB3SIMEDEHPILXTF73ZYD3JT6MYOLAZJG7PJJCAXCZCQOEFVH5VCW2KBQOKRYISWQLRWAT7IINZ3EFGQI2CY2EMK3FQOXM7UI3R7CZ6D73IKDI" width="1" height="1"></body>
    </html>
    ```

1. Разверните **Параметры** слева и для параметра **Тема сообщения электронной почты**введите `{{subject}}`.
1. Выберите **сохранить шаблон**.
1. Вернитесь на страницу **шаблонов транзакций** , щелкнув стрелку назад.
1. Запишите **идентификатор** шаблона, созданного для использования на более позднем этапе. Например, `d-989077fbba9746e89f3f6411f596fb96`. Этот идентификатор указывается при [добавлении преобразования «утверждения](#add-the-claims-transformation)».

## <a name="add-azure-ad-b2c-claim-types"></a>Добавление типов утверждений Azure AD B2C

В политике добавьте следующие типы утверждений в элемент `<ClaimsSchema>` в `<BuildingBlocks>`.

Эти типы утверждений необходимы для создания и проверки адреса электронной почты с помощью кода одноразового пароля (OTP).

```XML
<ClaimType Id="Otp">
  <DisplayName>Secondary One-time password</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="sendGridReqBody">
  <DisplayName>SendGrid request body</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="VerificationCode">
  <DisplayName>Secondary Verification Code</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Enter your email verification code</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

## <a name="add-the-claims-transformation"></a>Добавление преобразования «утверждения»

Далее необходимо преобразование заявок для вывода строкового утверждения JSON, которое будет основным текстом запроса, отправленного в SendGrid.

Структура объекта JSON определяется идентификаторами в точечной нотации InputParameters и Трансформатионклаимтипес Inputclaim. Числа в точечной нотации подразумевают массивы. Значения берутся из значений Inputclaim и свойств InputParameters "" value ". Дополнительные сведения о преобразованиях утверждений JSON см. в разделе [преобразования утверждений JSON](json-transformations.md).

Добавьте следующее преобразование утверждений в элемент `<ClaimsTransformations>` в `<BuildingBlocks>`. Внесите следующие изменения в XML-код преобразования утверждений.

* Обновите значение `template_id` InputParameter, указав идентификатор шаблона транзакций SendGrid, который вы создали ранее в [шаблоне Create SendGrid](#create-sendgrid-template).
* Обновите значение адреса `from.email`. Используйте допустимый адрес электронной почты, чтобы предотвратить пометку сообщения электронной почты как спама.
* Обновите значение параметра входной строки темы `personalizations.0.dynamic_template_data.subject` со строкой темы, подходящей для вашей организации.

```XML
<ClaimsTransformation Id="GenerateSendGridRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
    <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.dynamic_template_data.email" />
  </InputClaims>
  <InputParameters>
    <!-- Update the template_id value with the ID of your SendGrid template. -->
    <InputParameter Id="template_id" DataType="string" Value="d-989077fbba9746e89f3f6411f596fb96"/>
    <InputParameter Id="from.email" DataType="string" Value="my_email@mydomain.com"/>
    <!-- Update with a subject line appropriate for your organization. -->
    <InputParameter Id="personalizations.0.dynamic_template_data.subject" DataType="string" Value="Contoso account email verification code"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="sendGridReqBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

## <a name="add-datauri-content-definition"></a>Добавить определение содержимого DataUri

Под преобразованиями утверждений в `<BuildingBlocks>`добавьте следующий [контентдефинитион](contentdefinitions.md) для ссылки на URI данных версии 2.0.0:

```XML
<ContentDefinitions>
 <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.0.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

## <a name="create-a-displaycontrol"></a>Создать тип элемента

Элемент управления отображением проверки используется для проверки адреса электронной почты с помощью кода проверки, отправляемого пользователю.

Этот пример элемента управления отображением настраивается следующим образом:

1. Получение типа утверждения адреса `email` от пользователя.
1. Дождитесь, пока пользователь предоставит тип утверждения `verificationCode` с кодом, отправленным пользователю.
1. Возврат `email` в самостоятельно утвержденный технический профиль, который содержит ссылку на этот элемент управления отображения.
1. С помощью действия `SendCode` Создайте код OTP и отправьте пользователю сообщение электронной почты с кодом OTP.

![Действие отправки кода проверки по электронной почте](media/custom-email/display-control-verification-email-action-01.png)

В разделе определения содержимого по-прежнему в `<BuildingBlocks>`добавьте в [политику следующий элемент списка типа](display-controls.md) [верификатионконтрол](display-control-verification.md) .

```XML
<DisplayControls>
  <DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
    <DisplayClaims>
      <DisplayClaim ClaimTypeReferenceId="email" Required="true" />
      <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
    </DisplayClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="email" />
    </OutputClaims>
    <Actions>
      <Action Id="SendCode">
        <ValidationClaimsExchange>
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="GenerateOtp" />
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendGrid" />
        </ValidationClaimsExchange>
      </Action>
      <Action Id="VerifyCode">
        <ValidationClaimsExchange>
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="VerifyOtp" />
        </ValidationClaimsExchange>
      </Action>
    </Actions>
  </DisplayControl>
</DisplayControls>
```

## <a name="add-otp-technical-profiles"></a>Добавление технических профилей OTP

`GenerateOtp` технический профиль создает код для адреса электронной почты. `VerifyOtp` технический профиль проверяет код, связанный с адресом электронной почты. Можно изменить конфигурацию формата и срок действия одноразового пароля. Дополнительные сведения о технических профилях OTP см. [в статье Определение технического профиля одноразового пароля](one-time-password-technical-profile.md).

Добавьте следующие технические профили в элемент `<ClaimsProviders>`.

```XML
<ClaimsProvider>
  <DisplayName>One time password technical profiles</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="GenerateOtp">
      <DisplayName>Generate one time password</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="Operation">GenerateCode</Item>
        <Item Key="CodeExpirationInSeconds">1200</Item>
        <Item Key="CodeLength">6</Item>
        <Item Key="CharacterSet">0-9</Item>
        <Item Key="ReuseSameCode">true</Item>
        <Item Key="MaxNumAttempts">5</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="otp" PartnerClaimType="otpGenerated" />
      </OutputClaims>
    </TechnicalProfile>

    <TechnicalProfile Id="VerifyOtp">
      <DisplayName>Verify one time password</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="Operation">VerifyCode</Item>
        <Item Key="UserMessage.VerificationHasExpired">You have exceed the maximum time allowed.</Item>
        <Item Key="UserMessage.MaxRetryAttemped">You have exceed the number of retries allowed.</Item>
        <Item Key="UserMessage.InvalidCode">You have entered the wrong code.</Item>
        <Item Key="UserMessage.ServerError">Cannot verify the code, please try again later.</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
        <InputClaim ClaimTypeReferenceId="verificationCode" PartnerClaimType="otpToVerify" />
      </InputClaims>
    </TechnicalProfile>
   </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="add-a-rest-api-technical-profile"></a>Добавление REST API технического профиля

Этот REST API технический профиль создает содержимое электронной почты (с использованием формата SendGrid). Дополнительные сведения о технических профилях RESTFUL см. [в разделе Определение технического профиля RESTful](restful-technical-profile.md).

Как и в случае с техническими профилями OTP, добавьте следующие технические профили в элемент `<ClaimsProviders>`.

```XML
<ClaimsProvider>
  <DisplayName>RestfulProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SendGrid">
      <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="ClaimUsedForRequestPayload">sendGridReqBody</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_SendGridSecret" />
      </CryptographicKeys>
      <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="GenerateSendGridRequestBody" />
      </InputClaimsTransformations>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="sendGridReqBody" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="make-a-reference-to-the-displaycontrol"></a>Создание ссылки на тип элемента

На последнем шаге добавьте ссылку на созданный тип элемента. Замените существующий `LocalAccountSignUpWithLogonEmail` самостоятельно утвержденный технический профиль следующим, если вы использовали более раннюю версию политики Azure AD B2C. Этот технический профиль использует `DisplayClaims` со ссылкой на элемент конфигурации.

Дополнительные сведения см. в разделе [самостоятельно утвержденный технический профиль](restful-technical-profile.md) и [тип](display-controls.md)элемента.

```XML
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
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
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="optional-localize-your-email"></a>Используемых Локализация электронной почты

Чтобы локализовать сообщение электронной почты, необходимо отправить локализованные строки в SendGrid или ваш поставщик электронной почты. Например, чтобы локализовать тему сообщения электронной почты, текст, сообщение кода или подпись сообщения электронной почты. Для этого можно использовать преобразование утверждений [жетлокализедстрингстрансформатион](string-transformations.md) , чтобы скопировать локализованные строки в типы утверждений. В преобразовании «`GenerateSendGridRequestBody` утверждения», которая создает полезные данные JSON, использует входные утверждения, содержащие локализованные строки.

1. В политике определите следующие строковые утверждения: subject, Message, Кодеинтро и Signature.
1. Определите преобразование [жетлокализедстрингстрансформатион](string-transformations.md) Claims, чтобы заменить локализованные строковые значения на утверждения из шага 1.
1. Измените преобразование утверждений `GenerateSendGridRequestBody`, чтобы использовать входные утверждения со следующим фрагментом XML.
1. Обновите шаблон Сендгринд, чтобы использовать динамические параметры вместо всех строк, которые будут локализованы Azure AD B2C.

```XML
<ClaimsTransformation Id="GenerateSendGridRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
    <InputClaim ClaimTypeReferenceId="subject" TransformationClaimType="personalizations.0.dynamic_template_data.subject" />
    <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.dynamic_template_data.email" />
    <InputClaim ClaimTypeReferenceId="message" TransformationClaimType="personalizations.0.dynamic_template_data.message" />
    <InputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="personalizations.0.dynamic_template_data.codeIntro" />
    <InputClaim ClaimTypeReferenceId="signature" TransformationClaimType="personalizations.0.dynamic_template_data.signature" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="template_id" DataType="string" Value="d-1234567890" />
    <InputParameter Id="from.email" DataType="string" Value="my_email@mydomain.com" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="sendGridReqBody" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

## <a name="next-steps"></a>Дальнейшие действия

Пример пользовательской политики проверки электронной почты можно найти на сайте GitHub:

[Пользовательская проверка электронной почты — Дисплайконтролс](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-email-verifcation-displaycontrol)

Сведения об использовании настраиваемого REST API или поставщика электронной почты SMTP на основе HTTP см. [в разделе Определение технического профиля RESTful в Azure AD B2C настраиваемой политике](restful-technical-profile.md).
