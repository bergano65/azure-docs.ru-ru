---
title: Добавление собственных атрибутов в пользовательские политики
titleSuffix: Azure AD B2C
description: Пошаговое руководство по использованию свойств расширения и настраиваемых атрибутов, а также по их добавлению в пользовательский интерфейс.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bc5204518cb6e801ba661aecd5498a501122225f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473665"
---
# <a name="azure-active-directory-b2c-enable-custom-attributes-in-a-custom-profile-policy"></a>Активный каталог Azure B2C: Включить пользовательские атрибуты в пользовательской политике профиля

В [притязаниях и настройке пользовательского ввода с помощью статьи пользовательских политик](custom-policy-configure-user-input.md) вы узнаете, как использовать встроенные [атрибуты профиля пользователя.](user-profile-attributes.md) В этой статье вы можете включить пользовательский атрибут в каталоге Azure Active Directory B2C (Azure AD B2C). Позже новый атрибут можно использовать в качестве пользовательского требования в [потоках пользователей](user-flow-overview.md) или [пользовательских политиках](custom-policy-get-started.md) одновременно.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="prerequisites"></a>Предварительные требования

Выполните действия, описанные в статье [Azure Active Directory B2C: Начало работы с пользовательскими политиками](custom-policy-get-started.md).

## <a name="use-custom-attributes-to-collect-information-about-your-customers"></a>Используйте пользовательские атрибуты для сбора информации о ваших клиентах 

Каталог Azure AD B2C оснащен [встроенным набором атрибутов.](user-profile-attributes.md) Однако часто требуется создавать собственные атрибуты для управления конкретным сценарием, например, когда:

* Приложение, ориентированное на клиента, должно сохранять атрибут **LoyaltyId.**
* Поставщик идентификационных данных имеет уникальный идентификатор пользователя, **uniqueUserGUID**, который должен быть упорным.
* Пользовательский пользовательский путь должен сохраняться в состоянии пользователя, **migrationStatus**, для другой логики, чтобы работать.

Azure AD B2C позволяет расширить набор атрибутов, хранящихся на каждой учетной записи пользователя. Вы также можете прочитать и написать эти атрибуты с помощью [API Графика Майкрософт.](manage-user-accounts-graph-api.md)

## <a name="azure-ad-b2c-extensions-app"></a>Приложение расширения Azure AD B2C

Атрибуты расширения могут быть зарегистрированы только на объекте приложения, даже если они могут содержать данные для пользователя. Атрибут расширения прикрепляется к приложению под названием b2c-расширение-приложение. Не изменяйте это приложение, так как оно используется Azure AD B2C для хранения пользовательских данных. Вы можете найти это приложение в Azure AD B2C, регистрация приложений.

В контексте этой статьи *свойство расширения*, *настраиваемый атрибут* и *пользовательское утверждение* подразумевают одно и то же. Имя изменяется в зависимости от контекста, например приложения, объекта, политики.

## <a name="get-the-application-properties"></a>Получите свойства приложения

1. Войдите на [портал Azure](https://portal.azure.com).
1. Выберите фильтр **Каталог и подписка** в верхнем меню, а затем выберите каталог, содержащий клиент Azure AD B2C.
1. В меню слева выберите **Azure AD B2C**. Либо щелкните **Все службы**, а затем найдите и выберите **Azure AD B2C**
1. Выберите **регистрацию приложений (Предварительный просмотр),** а затем выберите **все приложения.**
1. Выберите `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` приложение.
1. Скопируйте следующие идентификаторы в буфер обмена и сохраните их.
    * **Идентификатор приложения**. Например, `11111111-1111-1111-1111-111111111111`.
    * **Идентификатор объекта**. Например, `22222222-2222-2222-2222-222222222222`.

## <a name="modify-your-custom-policy"></a>Измените свою пользовательскую политику

Чтобы включить пользовательские атрибуты в политику, предоставьте **идентификатор приложения** и **идентификатор объекта** приложения в метаданных технического профиля AAD-Common. Технический профиль *AAD-Common* содержится в базовом техническом профиле [Azure Active Directory](active-directory-technical-profile.md) и обеспечивает поддержку управления пользователями Azure AD. Другие технические профили Azure AD включают AAD-Common, чтобы использовать свою конфигурацию. Переизобить технический профиль AAD-Common в файле расширения.

1. Откройте файл расширений вашей политики. Например, <em> `SocialAndLocalAccounts/` </em>.
1. Найдите элемент ClaimsProviders. Добавьте новый поставщик претензий в элемент ClaimsProviders.
1. Замените `ApplicationObjectId` идентификатор объекта, который вы ранее записали. Затем `ClientId` замените идентификатор приложения, который вы ранее записали в нижеприведенном фрагменте.

    ```xml
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-Common">
          <Metadata>
            <!--Insert b2c-extensions-app application ID here, for example: 11111111-1111-1111-1111-111111111111-->  
            <Item Key="ClientId"></Item>
            <!--Insert b2c-extensions-app application ObjectId here, for example: 22222222-2222-2222-2222-222222222222-->
            <Item Key="ApplicationObjectId"></Item>
          </Metadata>
        </TechnicalProfile>
      <TechnicalProfiles> 
    </ClaimsProvider>
    ```

## <a name="upload-your-custom-policy"></a>Загрузите свою пользовательскую политику

1. Войдите на [портал Azure](https://portal.azure.com).
2. Убедитесь, что вы используете каталог, содержащий ваш клиент Azure AD, выбрав фильтр **подписки каталога** в верхнем меню и выбрав каталог, содержащий ваш клиент Azure AD B2C.
3. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Регистрация приложений**.
4. Выберите **Инфраструктура процедур идентификации**.
5. Выберите **загрузите пользовательскую политику,** а затем загрузите измененные файлы политики TrustFrameworkExtensions.xml.

> [!NOTE]
> В первый раз, когда технический профиль Azure AD сохраняет претензию к каталогу, он проверяет, существует ли пользовательский атрибут. Если нет, то он создает пользовательский атрибут.  

## <a name="create-a-custom-attribute-through-azure-portal"></a>Создание пользовательского атрибута через портал Azure

Те же атрибуты расширения распределяются между встроенными и пользовательскими политиками. При добавлении пользовательских атрибутов через портал эти атрибуты регистрируются с помощью **b2c-расширения-приложения,** которое существует в каждом арендаторе B2C.

Вы можете создать эти атрибуты, используя пользовательский пользовательский участок портала до или после использования их в пользовательских политиках. Следуйте инструкциям по [определению пользовательских атрибутов в Azure Active Directory B2C.](user-flow-custom-attributes.md) При создании **атрибута loyaltyId** на портале необходимо сослаться на него следующим образом:

|name     |Используется в |
|---------|---------|
|`extension_loyaltyId`  | Настраиваемая политика|
|`extension_<b2c-extensions-app-guid>_loyaltyId`  | [API Microsoft Graph](manage-user-accounts-graph-api.md)|

Следующий пример демонстрирует использование пользовательских атрибутов в определении пользовательской политики Azure AD B2C.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="extension_loyaltyId">
      <DisplayName>Loyalty Identification</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Your loyalty number from your membership card</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  </ClaimsSchema>
</BuildingBlocks>
```

Следующий пример демонстрирует использование пользовательского атрибута в пользовательской политике Azure AD B2C в техническом профиле, вводах, выводах и сохраняющиеся требованиях.

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="extension_loyaltyId"  />
</InputClaims>
<PersistedClaims>
  <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />
</PersistedClaims>
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
</OutputClaims>
```

## <a name="use-a-custom-attribute-in-a-policy"></a>Использование пользовательского атрибута в политике

Следуйте инструкциям по [добавлению заявок и настройке пользовательского ввода с помощью пользовательских политик.](custom-policy-configure-user-input.md) В этом примере используется встроенная претензия "город". Чтобы использовать пользовательский атрибут, замените 'город' своими пользовательскими атрибутами.


## <a name="next-steps"></a>Дальнейшие действия

См. также:

- [Атрибуты профиля пользователя Azure AD B2C](user-profile-attributes.md)
- [Определение атрибутов расширения](user-profile-attributes.md#extension-attributes)
- [Управление учетными записями пользователей Azure AD B2C с помощью Microsoft Graph](manage-user-accounts-graph-api.md)
