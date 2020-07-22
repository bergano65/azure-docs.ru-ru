---
title: Добавление собственных атрибутов в пользовательские политики
titleSuffix: Azure AD B2C
description: Пошаговое руководство по использованию свойств расширения и настраиваемых атрибутов, а также по их добавлению в пользовательский интерфейс.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/17/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: dbeb2540084fad2cfab3ce360dd15b60a75e5e59
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85389332"
---
# <a name="azure-active-directory-b2c-enable-custom-attributes-in-a-custom-profile-policy"></a>Azure Active Directory B2C: Включение настраиваемых атрибутов в пользовательской политике профиля

В статье [Добавление утверждений и настройка вводимых пользователем данных с помощью пользовательских политик](custom-policy-configure-user-input.md) вы узнаете, как использовать встроенные [атрибуты профиля пользователя](user-profile-attributes.md). В этой статье вы включите настраиваемый атрибут в каталог Azure Active Directory B2C (Azure AD B2C). Позже можно использовать новый атрибут в качестве настраиваемого утверждения в пользовательских [потоках](user-flow-overview.md) или [пользовательских политиках](custom-policy-get-started.md) одновременно.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="prerequisites"></a>Предварительные условия

Выполните действия, описанные в статье [Azure Active Directory B2C: Начало работы с пользовательскими политиками](custom-policy-get-started.md).

## <a name="use-custom-attributes-to-collect-information-about-your-customers"></a>Использование настраиваемых атрибутов для получения сведений о клиентах 

Каталог Azure AD B2C поставляется со [встроенным набором атрибутов](user-profile-attributes.md). Однако часто приходится создавать собственные атрибуты для управления конкретным сценарием, например:

* Приложению, ориентированному на клиента, необходимо сохранить атрибут **лойалтид** .
* Поставщик удостоверений имеет уникальный идентификатор пользователя **уникуеусергуид**, который должен быть сохранен.
* Пользовательский путь взаимодействия пользователя должен сохранить состояние пользователя **migrationStatus**для выполнения другой логики.

Azure AD B2C позволяет расширить набор атрибутов, хранящихся в каждой учетной записи пользователя. Эти атрибуты можно также считывать и записывать с помощью [Microsoft API Graph](manage-user-accounts-graph-api.md).

## <a name="azure-ad-b2c-extensions-app"></a>Приложение Azure AD B2C Extensions

Атрибуты расширения могут быть зарегистрированы только в объекте приложения, даже если они могут содержать данные для пользователя. Атрибут расширения прикрепляется к приложению с именем b2c-extensions-app. Не изменяйте это приложение, поскольку оно используется Azure AD B2C для хранения данных пользователя. Это приложение можно найти в разделе Azure AD B2C, регистрация приложений.

В контексте этой статьи *свойство расширения*, *настраиваемый атрибут* и *пользовательское утверждение* подразумевают одно и то же. Имя изменяется в зависимости от контекста, например приложения, объекта, политики.

## <a name="get-the-application-properties"></a>Получение свойств приложения

1. Войдите на [портал Azure](https://portal.azure.com).
1. Выберите фильтр **Каталог и подписка** в верхнем меню, а затем выберите каталог, содержащий клиент Azure AD B2C.
1. В меню слева выберите **Azure AD B2C**. Либо щелкните **Все службы**, а затем найдите и выберите **Azure AD B2C**
1. Выберите **Регистрация приложений**, а затем выберите **все приложения**.
1. Выберите `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` приложение.
1. Скопируйте следующие идентификаторы в буфер обмена и сохраните их.
    * **Идентификатор приложения**. Например, `11111111-1111-1111-1111-111111111111`.
    * **Идентификатор объекта**. Например, `22222222-2222-2222-2222-222222222222`.

## <a name="modify-your-custom-policy"></a>Изменение настраиваемой политики

Чтобы включить настраиваемые атрибуты в политике, укажите **идентификатор приложения** и **идентификатор объекта** приложения в метаданных технического профиля AAD-Common. Технический профиль *AAD-Common* находится в основном [Azure Active Directory](active-directory-technical-profile.md) техническом профиле и обеспечивает поддержку управления пользователями в Azure AD. Другие технические профили Azure AD включают в себя AAD-Common, чтобы использовать его конфигурацию. Переопределите общий технический профиль AAD в файле расширения.

1. Откройте файл расширения для политики, например <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.
1. Найдите элемент ClaimsProviders. Добавьте новый поставщика утверждений в элемент Клаимспровидерс.
1. Замените на `ApplicationObjectId` идентификатор объекта, который вы записали ранее. Затем замените `ClientId` идентификатором приложения, записанным ранее в следующем фрагменте кода.

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
      </TechnicalProfiles> 
    </ClaimsProvider>
    ```

## <a name="upload-your-custom-policy"></a>Отправка настраиваемой политики

1. Войдите на [портал Azure](https://portal.azure.com).
2. Убедитесь, что вы используете каталог, содержащий клиент Azure AD, выбрав фильтр " **каталог и подписка** " в верхнем меню и выбрав Каталог, содержащий клиент Azure AD B2C.
3. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Регистрация приложений**.
4. Выберите **Инфраструктура процедур идентификации**.
5. Выберите **Отправить настраиваемую политику**, а затем отправьте измененные файлы политики TrustFrameworkExtensions.xml.

> [!NOTE]
> В первый раз, когда технический профиль Azure AD сохраняет утверждение в каталоге, он проверяет, существует ли настраиваемый атрибут. В противном случае он создает настраиваемый атрибут.  

## <a name="create-a-custom-attribute-through-azure-portal"></a>Создание настраиваемого атрибута с помощью портал Azure

Одни и те же атрибуты расширения являются общими для встроенных и пользовательских политик. При добавлении настраиваемых атрибутов с помощью портала эти атрибуты регистрируются с помощью **B2C-Extensions-App** , которые существуют в каждом клиенте B2C.

Эти атрибуты можно создать с помощью пользовательского интерфейса портала до или после их использования в пользовательских политиках. Следуйте указаниям по [определению настраиваемых атрибутов в Azure Active Directory B2C](user-flow-custom-attributes.md). При создании атрибута **лойалтид** на портале необходимо ссылаться на него следующим образом:

|name     |Используется в |
|---------|---------|
|`extension_loyaltyId`  | Настраиваемая политика|
|`extension_<b2c-extensions-app-guid>_loyaltyId`  | [API Microsoft Graph](manage-user-accounts-graph-api.md)|

В следующем примере показано использование настраиваемых атрибутов в определении утверждения настраиваемой политики Azure AD B2C.

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

В следующем примере демонстрируется использование настраиваемого атрибута в Azure AD B2C настраиваемой политики в техническом профиле, вводе, выходе и материализованных утверждениях.

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

## <a name="use-a-custom-attribute-in-a-policy"></a>Использование настраиваемого атрибута в политике

Следуйте указаниям по [добавлению утверждений и настройке пользовательского ввода с помощью пользовательских политик](custom-policy-configure-user-input.md). В этом образце используется встроенное утверждение "City". Чтобы использовать настраиваемый атрибут, замените City на собственные настраиваемые атрибуты.


## <a name="next-steps"></a>Дальнейшие действия

См. также:

- [Azure AD B2C атрибутов профиля пользователя](user-profile-attributes.md)
- [Определение атрибутов расширения](user-profile-attributes.md#extension-attributes)
- [Управление Azure AD B2C учетными записями пользователей с помощью Microsoft Graph](manage-user-accounts-graph-api.md)
