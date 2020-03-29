---
title: Пользовательские проверки электронной почты
titleSuffix: Azure AD B2C
description: Узнайте, как настроить электронную почту проверки, отправленную вашим клиентам, когда они зарегистриваются, чтобы использовать приложения с поддержкой Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6cc0508a63f26b955ac5e0ebf3ef58a184a35997
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671632"
---
# <a name="custom-email-verification-in-azure-active-directory-b2c"></a>Пользовательская проверка электронной почты в Azure Active Directory B2C

Используйте пользовательскую электронную почту в Azure Active Directory B2C (Azure AD B2C) для отправки настраиваемых электронных писем пользователям, которые зарегистрируйтесь для использования ваших приложений. Используя [DisplayControls](display-controls.md) (в настоящее время находится в предварительном просмотре) и стороннего поставщика электронной почты, вы можете использовать свой собственный шаблон электронной почты и *from:* адрес и объект, а также поддерживать локализацию и пользовательские одноразовые настройки паролей (OTP).

Пользовательская проверка электронной почты требует использования стороннего поставщика электронной почты, такого как [SendGrid](https://sendgrid.com) или [SparkPost,](https://sparkpost.com)пользовательского REST API, или любого поставщика электронной почты на основе HTTP (включая ваш собственный). В этой статье описывается настройка решения, используюгое SendGrid.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-sendgrid-account"></a>Создание учетной записи SendGrid

Если у вас его еще нет, начните с создания учетной записи SendGrid (клиенты Azure могут разблокировать 25 000 бесплатных писем каждый месяц). Для инструкций по настройке смотрите раздел [«Создание учетной записи SendGrid»](../sendgrid-dotnet-how-to-send-email.md#create-a-sendgrid-account) о том, [как отправлять электронную почту с помощью SendGrid с помощью Azure.](../sendgrid-dotnet-how-to-send-email.md)

Обязательно заполните раздел, в котором вы [создаете ключ API SendGrid.](../sendgrid-dotnet-how-to-send-email.md#to-find-your-sendgrid-api-key) Запись ключа API для использования на более позднем этапе.

## <a name="create-azure-ad-b2c-policy-key"></a>Создание ключа политики Azure AD B2C

Затем храните ключ API SendGrid в ключе политики Azure AD B2C для ссылки на политику.

1. Войдите на [портал Azure](https://portal.azure.com/).
1. Убедитесь, что вы используете каталог, содержащий ваш арендатор Azure AD B2C. Выберите фильтр **подписки каталога** в верхнем меню и выберите каталог Azure AD B2C.
1. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Azure AD B2C**.
1. На странице "Обзор" выберите **Identity Experience Framework**.
1. Выберите **Ключи политики**, а затем щелкните **Добавить**.
1. Для пункта **Параметры** выберите `Manual`.
1. Введите **имя** ключа политики. Например, `SendGridSecret`. Префикс `B2C_1A_` будет автоматически добавлен к имени ключа.
1. В поле **Секрет** введите ранее записанный секрет клиента.
1. Для параметра **Использование ключа** выберите `Signature`.
1. Выберите **Создать**.

## <a name="create-sendgrid-template"></a>Создание шаблона SendGrid

Создавая учетную запись SendGrid и хранится ключ SendGrid API в ключе политики Azure AD B2C, создайте [динамический транзакционный шаблон](https://sendgrid.com/docs/ui/sending-email/how-to-send-an-email-with-dynamic-transactional-templates/)SendGrid.

1. На сайте SendGrid откройте страницу [транзакционных шаблонов](https://sendgrid.com/dynamic_templates) и выберите **Create Template.**
1. Введите уникальное `Verification email` имя шаблона, как, а затем выберите **Сохранить**.
1. Чтобы начать редактирование нового шаблона, выберите **Добавить версию.**
1. Выберите **редактор кода,** а затем **продолжить**.
1. В HTML-редакторе вставьте следующий html-шаблон или используйте свой собственный. Параметры `{{otp}}` `{{email}}` и параметры будут заменены динамически на одноразовую стоимость пароля и адрес электронной почты пользователя.

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

1. Расширьте **настройки** слева, а для `{{subject}}`email **Subject**введите .
1. Выберите **сохранить шаблон**.
1. Вернитесь на страницу **транзакционных шаблонов,** выбрав заднюю стрелку.
1. Запись **идентификатора** шаблона, созданного для использования на более позднем этапе. Например, `d-989077fbba9746e89f3f6411f596fb96`. Вы указываете этот идентификатор при [добавлении преобразования претензий.](#add-the-claims-transformation)

## <a name="add-azure-ad-b2c-claim-types"></a>Добавление типов претензий Azure AD B2C

В свою политику добавьте следующие `<ClaimsSchema>` типы претензий в элемент внутри `<BuildingBlocks>`.

Эти типы утверждений необходимы для генерации и проверки адреса электронной почты с помощью одноразового пароля (OTP).

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

## <a name="add-the-claims-transformation"></a>Добавление преобразования претензий

Далее необходимо преобразование требований для вывода строки JSON, которая будет частью запроса, отправленного в SendGrid.

Структура объекта JSON определяется идентифицироваться в dot-нотировании параметров ввода и TransformationClaimTypes InputClaims. Числа в обозначении точки подразумевают массивы. Значения происходят из значений InputClaims и свойств "Значение" Ввода Параметров. Для получения дополнительной информации о [JSON claims transformations](json-transformations.md)преобразованиях претензий JSON см.

Добавьте следующие преобразования `<ClaimsTransformations>` претензий в элемент внутри. `<BuildingBlocks>` Сделайте следующие обновления преобразования претензий XML:

* Обновление `template_id` значения ввода паразамеля с идентификатором транзакционного шаблона SendGrid, созданным ранее в [шаблоне Create SendGrid.](#create-sendgrid-template)
* Обновление `from.email` значения адреса. Используйте действительный адрес электронной почты, чтобы предотвратить проверку электронной почты от помечены как спам.
* Обновление значения ввода параметра строки `personalizations.0.dynamic_template_data.subject` темы с помощью строки темы, подходящей для вашей организации.

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

## <a name="add-datauri-content-definition"></a>Добавление определения содержимого DataUri

Ниже преобразования претензий `<BuildingBlocks>`в пределах, добавить следующее [ContentDefinition](contentdefinitions.md) ссылку версии 2.0.0 данных URI:

```XML
<ContentDefinitions>
 <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.0.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

## <a name="create-a-displaycontrol"></a>Создание DisplayControl

Контроль отсеиваний проверки используется для проверки адреса электронной почты с кодом проверки, который отправляется пользователю.

Этот пример управления дисплеем настроен на:

1. Соберите `email` тип требования адреса от пользователя.
1. Подождите, пока пользователь `verificationCode` предоставит тип претензии с кодом, отправленным пользователю.
1. Верните `email` назад в самоутверждаемый технический профиль, который имеет ссылку на этот элемент управления дисплеем.
1. Используя `SendCode` действие, создайте OTP-код и отправьте пользователю электронное письмо с кодом OTP.

![Отправка действия по электронной почте код проверки](media/custom-email/display-control-verification-email-action-01.png)

В соответствии с `<BuildingBlocks>`определениями содержания, по-прежнему в рамках , добавить следующий [DisplayControl](display-controls.md) типа [VerificationControl](display-control-verification.md) к вашей политике.

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

Технический `GenerateOtp` профиль генерирует код для адреса электронной почты. Технический `VerifyOtp` профиль проверяет код, связанный с адресом электронной почты. Можно изменить конфигурацию формата и истечение срока действия одноразового пароля. Для получения дополнительной информации о технических профилях OTP [см.](one-time-password-technical-profile.md)

Добавьте в `<ClaimsProviders>` элемент следующие технические профили.

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
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
        <InputClaim ClaimTypeReferenceId="verificationCode" PartnerClaimType="otpToVerify" />
      </InputClaims>
    </TechnicalProfile>
   </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="add-a-rest-api-technical-profile"></a>Добавление технического профиля REST API API

Этот технический профиль REST API генерирует содержимое электронной почты (с использованием формата SendGrid). Для получения дополнительной информации о технических профилях RESTful [см.](restful-technical-profile.md)

Как и в технических профилях OTP, добавьте в `<ClaimsProviders>` элемент следующие технические профили.

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

## <a name="make-a-reference-to-the-displaycontrol"></a>Сделайте ссылку на DisplayControl

На заключительном этапе добавьте ссылку на созданный вами DisplayControl. Замените `LocalAccountSignUpWithLogonEmail` существующий самоутверждаемый технический профиль следующим, если вы использовали более раннюю версию политики Azure AD B2C. Этот технический `DisplayClaims` профиль использует со ссылкой на DisplayControl.

Для получения дополнительной информации см. [DisplayControl](display-controls.md) [Self-asserted technical profile](restful-technical-profile.md)

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
        
        <!--OTP validation error messages-->
        <Item Key="UserMessageIfSessionDoesNotExist">You have exceed the maximum time allowed.</Item>
        <Item Key="UserMessageIfMaxRetryAttempted">You have exceed the number of retries allowed.</Item>
        <Item Key="UserMessageIfInvalidCode">You have entered the wrong code.</Item>
        <Item Key="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</Item>
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

## <a name="optional-localize-your-email"></a>(Необязательно) Локализовать электронную почту

Чтобы локализовать электронную почту, необходимо отправить локализованные строки в SendGrid или ввашйло поставщика услуг электронной почты. Например, для локализации объекта электронной почты, тела, кода или подписи. Для этого можно использовать преобразование требований [GetLocalizedStringsTransformation](string-transformations.md) для копирования локализованных строк в типы претензий. В `GenerateSendGridRequestBody` преобразовании утверждений, генерируемом полезной нагрузкой JSON, используются входные претензии, содержащие локализованные строки.

1. В вашей политике определяются следующие строки претензий: тема, сообщение, codeIntro и подпись.
1. Определите преобразование [GetLocalizedStringsTransformation](string-transformations.md) для замены локализованных значений строкви в требования с шага 1.
1. Измените `GenerateSendGridRequestBody` преобразование претензий для использования входно-претензий со следующим фрагментом XML.
1. Обновите шаблон SendGrind, чтобы использовать динамические параметры вместо всех строк, которые будут локализованы Azure AD B2C.

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

Вы можете найти пример пользовательской политики проверки электронной почты на GitHub:

[Пользовательская проверка электронной почты - DisplayControls](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-email-verifcation-displaycontrol)

Для получения информации об использовании пользовательского REST API или любого поставщика электронной почты SMTP на основе HTTP [см.](restful-technical-profile.md)
