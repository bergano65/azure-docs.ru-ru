---
title: Настройка сложности пароля в настраиваемых политиках в Azure Active Directory B2C | Документация Майкрософт
description: Узнайте, как настроить требования к сложности паролей с помощью настраиваемой политики в Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 04a37e6faf51787457d7ca4ab8434fd253deb2ed
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66509158"
---
# <a name="configure-password-complexity-using-custom-policies-in-azure-active-directory-b2c"></a>Настройка сложности пароля в настраиваемых политиках в Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В Azure Active Directory B2C (Azure AD) можно настроить требования к сложности паролей, которые пользователи указывают при создании учетной записи. По умолчанию Azure AD B2C использует **надежные** пароли. В этой статье рассказывается о настройке сложности пароля в [настраиваемых политиках](active-directory-b2c-overview-custom.md). Вы также можете настроить сложность пароля в [пользовательских потоках](active-directory-b2c-reference-password-complexity.md).

## <a name="prerequisites"></a>Технические условия

Выполните шаги, описанные в статье [Начало работы с настраиваемыми политиками в Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="add-the-elements"></a>Добавление элементов

1. Скопируйте файл *SignUpOrSignIn.xml*, загруженный с начальным пакетом, и назовите его *SingUpOrSignInPasswordComplexity.xml*.
2. Откройте файл *SingUpOrSignInPasswordComplexity.xml* и измените **PolicyId** и **PublicPolicyUri** на новое имя политики. Например, *B2C_1A_signup_signin_password_complexity*.
3. Добавьте следующие элементы **ClaimType** с идентификаторами `newPassword` и `reenterPassword`.

    ```XML
    <ClaimsSchema>
      <ClaimType Id="newPassword">
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
      <ClaimType Id="reenterPassword">
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
    </ClaimsSchema>
    ```

4. [Предикаты](predicates.md) имеют типы метода `IsLengthRange` или `MatchesRegex`. Тип `MatchesRegex` используется для сопоставления регулярного выражения. Тип `IsLengthRange` принимает минимальную и максимальную длину строки. Добавьте элемент **Predicates** в элемент **BuildingBlocks**, если в нем нет следующих элементов **Predicate**.

    ```XML
    <Predicates>
      <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be a pin.">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Length" Method="IsLengthRange" HelpText="The password must be between 8 and 16 characters.">
        <Parameters>
          <Parameter Id="Minimum">8</Parameter>
          <Parameter Id="Maximum">16</Parameter>
        </Parameters>
      </Predicate>
    </Predicates>
    ```

5. Каждый элемент **InputValidation** создается с использованием определенных элементов **Predicate**. Этот элемент дает возможность выполнять логическое агрегирование, схожее с `and` и `or`. Добавьте элемент **InputValidations** в элемент **BuildingBlocks**, если в нем нет следующих элементов **InputValidation**.

    ```XML
    <InputValidations>
      <InputValidation Id="PasswordValidation">
        <PredicateReferences Id="LengthGroup" MatchAtLeast="1">
          <PredicateReference Id="Length" />
        </PredicateReferences>
        <PredicateReferences Id="3of4" MatchAtLeast="3" HelpText="You must have at least 3 of the following character classes:">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </InputValidation>
    </InputValidations>
    ```

6. Убедитесь, что технический профиль **PolicyProfile** содержит следующие элементы.

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn"/>
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect"/>
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
        </InputClaims>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="displayName"/>
          <OutputClaim ClaimTypeReferenceId="givenName"/>
          <OutputClaim ClaimTypeReferenceId="surname"/>
          <OutputClaim ClaimTypeReferenceId="email"/>
          <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub"/>
      </TechnicalProfile>
    </RelyingParty>
    ```

7. Сохраните файл политики.

## <a name="test-your-policy"></a>Тестирование политики

При тестировании приложений в Azure AD B2C может потребоваться вернуть маркер Azure AD B2C в `https://jwt.ms` для просмотра в нем утверждений.

### <a name="upload-the-files"></a>Передача файлов

1. Войдите на [портале Azure](https://portal.azure.com/).
2. Убедитесь, что используете каталог, содержащий клиент Azure AD B2C, щелкнув **Фильтр каталога и подписки** в верхнем меню и выбрав каталог, содержащий ваш клиент.
3. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Azure AD B2C**.
4. Выберите **Инфраструктура процедур идентификации**.
5. На странице "Настраиваемые политики" щелкните **Отправить политику**.
6. Выберите **Перезаписать политику, если она существует**, а затем найдите и выберите файл *SingUpOrSignInPasswordComplexity.xml*.
7. Щелкните **Отправить**.

### <a name="run-the-policy"></a>Запуск политики

1. Откройте измененную политику. Например, *B2C_1A_signup_signin_password_complexity*.
2. В разделе **Приложение** выберите зарегистрированное ранее приложение. Чтобы маркер отображался, **URL-адрес ответа** должен быть следующим: `https://jwt.ms`.
3. Щелкните **Запустить сейчас**.
4. Выберите **Зарегистрироваться сейчас**, введите адрес электронной почты и новый пароль. Отображаются рекомендации по ограничению для пароля. Введите данные пользователя и нажмите **Создать**. Вы увидите содержимое возвращенного маркера.

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте, как [настроить сложность пароля в настраиваемых политиках в Azure Active Directory B2C](active-directory-b2c-reference-password-change-custom.md).


