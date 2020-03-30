---
title: Передайте токен доступа через пользовательскую политику в ваше приложение
titleSuffix: Azure AD B2C
description: Узнайте, как можно передать токен доступа для поставщиков идентификационных данных OAuth 2.0 в качестве претензии через пользовательскую политику для вашего приложения в Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ff5ef8f742914129d868152814d84d2112267c09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187801"
---
# <a name="pass-an-access-token-through-a-custom-policy-to-your-application-in-azure-active-directory-b2c"></a>Передача маркера доступа с помощью пользовательской политики в приложение в Azure Active Directory B2C

[Пользовательская политика](custom-policy-get-started.md) в Azure Active Directory B2C (Azure AD B2C) предоставляет пользователям приложения возможность зарегистрироваться или зарегистрироваться у поставщика идентификационных данных. При этом Azure AD B2C получает [маркер доступа](tokens-overview.md) от поставщика удостоверений. Azure AD B2C использует этот маркер для извлечения сведений о пользователе. Вы добавляете тип утверждения и исходящее утверждение в пользовательскую политику для передачи маркера приложениям, зарегистрированным в Azure AD B2C.

Azure AD B2C поддерживает передачу маркеров доступа от поставщиков удостоверений по протоколу [OAuth 2.0](authorization-code-flow.md) и стандарту [Подключение OpenID](openid-connect.md). Для остальных поставщиков удостоверений утверждение возвращается пустым.

## <a name="prerequisites"></a>Предварительные требования

* Ваша пользовательская политика поддерживает поставщиков удостоверений OAuth 2.0 или Подключение OpenID.

## <a name="add-the-claim-elements"></a>Добавление элементов утверждения

1. Откройте файл *TrustframeworkExtensions.xml* и добавьте элемент **ClaimType** с идентификатором `identityProviderAccessToken` в элемент **ClaimsSchema**:

    ```XML
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="identityProviderAccessToken">
          <DisplayName>Identity Provider Access Token</DisplayName>
          <DataType>string</DataType>
          <AdminHelpText>Stores the access token of the identity provider.</AdminHelpText>
        </ClaimType>
        ...
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. Добавьте элемент **OutputClaim** в элемент **TechnicalProfile** для каждого поставщика удостоверений OAuth 2.0, для которого нужно использовать маркер доступа. В следующем примере показано добавление элемента в технический профиль Facebook:

    ```XML
    <ClaimsProvider>
      <DisplayName>Facebook</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Facebook-OAUTH">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="{oauth2:access_token}" />
          </OutputClaims>
          ...
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

3. Сохраните файл *TrustframeworkExtensions.xml*.
4. Откройте файл политики проверяющей стороны, например *SignUpOrSignIn.xml*, и добавьте элемент **OutputClaim** в **TechnicalProfile**:

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <TechnicalProfile Id="PolicyProfile">
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="idp_access_token"/>
        </OutputClaims>
        ...
      </TechnicalProfile>
    </RelyingParty>
    ```

5. Сохраните файл политики.

## <a name="test-your-policy"></a>Тестирование политики

При тестировании приложений в Azure AD B2C может потребоваться вернуть маркер Azure AD B2C в `https://jwt.ms` для просмотра в нем утверждений.

### <a name="upload-the-files"></a>Передача файлов

1. Войдите на [портал Azure](https://portal.azure.com/).
2. Убедитесь, что вы используете каталог, содержащий ваш клиент Azure AD B2C, нажав на фильтр **подписки каталога** и выбрав каталог, содержащий ваш арендатор.
3. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Azure AD B2C**.
4. Выберите **Инфраструктура процедур идентификации**.
5. На странице "Настраиваемые политики" щелкните **Отправить политику**.
6. Выберите **Перезаписать политику, если она существует**, а затем найдите и выберите файл *TrustFrameworkExtensions.xml*.
7. Выберите команду **Отправить**.
8. Повторите шаги с 5 по 7 для файла проверяющей стороны, например *SignUpOrSignIn.xml*.

### <a name="run-the-policy"></a>Запуск политики

1. Откройте измененную политику. Например, *B2C_1A_signup_signin*.
2. В разделе **Приложение** выберите зарегистрированное ранее приложение. Чтобы маркер отображался, как в приведенном ниже примере, **URL-адрес ответа** должен быть следующим: `https://jwt.ms`.
3. Выберите **Запустить сейчас**.

    Должен отобразиться результат, аналогичный следующему примеру:

    ![Расшифрованный токен в jwt.ms с выделенным idp_access_token блоком](./media/idp-pass-through-custom/idp-pass-through-custom-token.PNG)

## <a name="next-steps"></a>Дальнейшие действия

Подробнее о токенах читайте в [справочнике AAzure Active Directory B2C.](tokens-overview.md)
