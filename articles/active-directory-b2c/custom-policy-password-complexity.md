---
title: Настройка сложности пароля с помощью пользовательских политик
titleSuffix: Azure AD B2C
description: Узнайте, как настроить требования к сложности паролей с помощью настраиваемой политики в Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b16790e288f6569f08ce14e5a7c751bbd8083faf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79138440"
---
# <a name="configure-password-complexity-using-custom-policies-in-azure-active-directory-b2c"></a>Настройка сложности пароля в настраиваемых политиках в Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В Azure Active Directory B2C (Azure AD B2C) можно настроить требования к сложности паролей, которые предоставляются пользователем при создании учетной записи. По умолчанию Azure AD B2C использует **надежные** пароли. В этой статье рассказывается о настройке сложности пароля в [настраиваемых политиках](custom-policy-overview.md). Вы также можете настроить сложность пароля в [пользовательских потоках](user-flow-password-complexity.md).

## <a name="prerequisites"></a>Предварительные требования

Выполните шаги, описанные в статье [Начало работы с настраиваемыми политиками в Azure Active Directory B2C](custom-policy-get-started.md). Для регистрации и входа с использованием локальных учетных записей необходимо иметь рабочую настраиваемую политику.


## <a name="add-the-elements"></a>Добавление элементов

Чтобы настроить сложность `newPassword` пароля, переопределить `reenterPassword` [и претензии типов](claimsschema.md) со ссылкой на [предикат проверки.](predicates.md#predicatevalidations) Элемент PredicateValidations группирует набор предикатов для формирования проверки ввода пользователя, которая может быть применена к типу претензии. Откройте файл расширений вашей политики. Например, <em> `SocialAndLocalAccounts/` </em>.

1. Найдите элемент [BuildingBlocks](buildingblocks.md). Если такой элемент не существует, добавьте его.
1. Найдите элемент [ClaimsSchema.](claimsschema.md) Если такой элемент не существует, добавьте его.
1. Добавьте `newPassword` `reenterPassword` и претензии к элементу **ClaimsSchema.**

    ```XML
    <ClaimType Id="newPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    <ClaimType Id="reenterPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    ```

1. [Predicates](predicates.md) определяет базовую проверку для проверки значения типа претензии и возвращает сяпоническую или ложную. Проверка осуществляется с помощью определенного элемента метода и набора параметров, относящихся к методу. Добавьте следующие предикаты к элементу **BuildingBlocks** `</ClaimsSchema>` сразу после закрытия элемента:

    ```XML
    <Predicates>
      <Predicate Id="LengthRange" Method="IsLengthRange">
        <UserHelpText>The password must be between 6 and 64 characters.</UserHelpText>
        <Parameters>
          <Parameter Id="Minimum">6</Parameter>
          <Parameter Id="Maximum">64</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Lowercase" Method="IncludesCharacters">
        <UserHelpText>a lowercase letter</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">a-z</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Uppercase" Method="IncludesCharacters">
        <UserHelpText>an uppercase letter</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">A-Z</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Number" Method="IncludesCharacters">
        <UserHelpText>a digit</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">0-9</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Symbol" Method="IncludesCharacters">
        <UserHelpText>a symbol</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\\:',.?/`~"();!</Parameter>
        </Parameters>
      </Predicate>
    </Predicates>
    ```

1. Добавьте следующие предикатные проверки в элемент **BuildingBlocks** `</Predicates>` сразу после закрытия элемента:

    ```XML
    <PredicateValidations>
      <PredicateValidation Id="CustomPassword">
        <PredicateGroups>
          <PredicateGroup Id="LengthGroup">
            <PredicateReferences MatchAtLeast="1">
              <PredicateReference Id="LengthRange" />
            </PredicateReferences>
          </PredicateGroup>
          <PredicateGroup Id="CharacterClasses">
            <UserHelpText>The password must have at least 3 of the following:</UserHelpText>
            <PredicateReferences MatchAtLeast="3">
              <PredicateReference Id="Lowercase" />
              <PredicateReference Id="Uppercase" />
              <PredicateReference Id="Number" />
              <PredicateReference Id="Symbol" />
            </PredicateReferences>
          </PredicateGroup>
        </PredicateGroups>
      </PredicateValidation>
    </PredicateValidations>
    ```

1. Следующими техническими профилями являются [технические профили Active Directory,](active-directory-technical-profile.md)которые считывают и записывают данные в Active Directory Azure. Переопределить эти технические профили в файле расширения. Используйте `PersistedClaims` для отторгки сильной политики паролей. Найдите элемент **ClaimsProviders**.  Добавить следующие поставщики претензий следующим образом:

    ```XML
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
          <PersistedClaims>
            <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
          </PersistedClaims>
        </TechnicalProfile>
        <TechnicalProfile Id="AAD-UserWritePasswordUsingObjectId">
          <PersistedClaims>
            <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
          </PersistedClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Сохраните файл политики.

## <a name="test-your-policy"></a>Тестирование политики

### <a name="upload-the-files"></a>Передача файлов

1. Войдите на [портал Azure](https://portal.azure.com/).
2. Убедитесь, что используете каталог с клиентом Azure AD B2C, выбрав фильтр **Каталог и подписка** в меню вверху и каталог с вашим клиентом.
3. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Azure AD B2C**.
4. Выберите **Инфраструктура процедур идентификации**.
5. На странице "Настраиваемые политики" щелкните **Отправить политику**.
6. Выберите **Перезапись политики, если она существует,** а затем выполнить поиск и выбрать файл *TrustFrameworkExtensions.xml.*
7. Нажмите кнопку **Отправка**.

### <a name="run-the-policy"></a>Запуск политики

1. Откройте политику регистрации или регистрации. Например, *B2C_1A_signup_signin*.
2. В разделе **Приложение** выберите зарегистрированное ранее приложение. Чтобы маркер отображался, **URL-адрес ответа** должен быть следующим: `https://jwt.ms`.
3. Щелкните **Запустить сейчас**.
4. Выберите **Зарегистрироваться сейчас**, введите адрес электронной почты и новый пароль. Отображаются рекомендации по ограничению для пароля. Введите данные пользователя и нажмите **Создать**. Вы увидите содержимое возвращенного маркера.

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте, как [настроить сложность пароля в настраиваемых политиках в Azure Active Directory B2C](custom-policy-password-change.md).
- Узнайте больше об элементах [Predicates](predicates.md) и [PredicateValidations](predicates.md#predicatevalidations) в справке IEF.
