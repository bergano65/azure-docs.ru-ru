---
title: Настройка пользовательского интерфейса приложения с помощью пользовательской политики
titleSuffix: Azure AD B2C
description: Дополнительные сведения о настройке пользовательского интерфейса с помощью настраиваемой политики в Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e50d6d0623e87dfa68a7cc9744c3f595ff0179c6
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396387"
---
# <a name="customize-the-user-interface-of-your-application-using-a-custom-policy-in-azure-active-directory-b2c"></a>Настройка пользовательского интерфейса приложения с помощью настраиваемой политики в Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Выполнив шаги в этой статье, вы создаете регистрацию и регистрацию пользовательской политики с вашим брендом и внешний вид. Благодаря Azure Active Directory B2C (Azure AD B2C) вы получаете практически полный контроль над содержимым HTML и CSS, которое представляется пользователям. При использовании настраиваемой политики вы настраиваете пользовательский интерфейс в XML-файле, а не с помощью элементов управления на портале Azure.

## <a name="prerequisites"></a>Предварительные требования

Выполните шаги, описанные в статье [Начало работы с настраиваемыми политиками в Azure Active Directory B2C](custom-policy-get-started.md). Для регистрации и входа с использованием локальных учетных записей необходимо иметь рабочую настраиваемую политику.

[!INCLUDE [active-directory-b2c-html-how-to](../../includes/active-directory-b2c-html-how-to.md)]

### <a name="4-modify-the-extensions-file"></a>4. Изменить файл расширений

Чтобы настроить настройку пользовательского элемента, скопируйте **ContentDefinition** и элементы его ребенка из базового файла в файл расширений.

1. Откройте базовый файл политики. Например, <em> `SocialAndLocalAccounts/` </em>. Этот базовый файл является одним из файлов политики, включенных в пользовательский пакет стартера политики, который вы должны были получить в предпосылке, [Начать работу с пользовательских политик.](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom)
1. Найдите и скопируйте содержимое элемента **ContentDefinitions**.
1. Откройте файл расширения, Например, *TrustFrameworkExtensions.xml*. Найдите элемент **BuildingBlocks**. Если такой элемент не существует, добавьте его.
1. Вставьте содержимое скопированного элемента **ContentDefinitions** в качестве дочернего элемента **BuildingBlocks**.
1. Найдите элемент **ContentDefinition**, содержащий `Id="api.signuporsignin"` в скопированном файле XML.
1. Измените значение **LoadUri** на URL-адрес файла HTML, который был передан в хранилище. Например, `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.

    Пользовательская политика должна выглядеть следующим фрагментом кода:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <LoadUri>https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

1. Сохраните файл расширений.

### <a name="5-upload-and-test-your-updated-custom-policy"></a>5. Загрузить и протестировать обновленную пользовательскую политику

#### <a name="51-upload-the-custom-policy"></a>5.1 Загрузить пользовательскую политику

1. Убедитесь, что используете каталог с клиентом Azure AD B2C, выбрав фильтр **Каталог и подписка** в меню вверху и каталог с вашим клиентом.
1. Поиск и выберите **Azure AD B2C.**
1. В соответствии с **политиками**выберите **рамки опыта идентификации.**
1. Выберите **пользовательскую политику загрузки.**
1. Выполните отправку ранее измененного файла расширений.

#### <a name="52-test-the-custom-policy-by-using-run-now"></a>5.2 Протестуйте пользовательскую политику, используя **Run now**

1. Выберите загруженную политику, а затем выберите **«Выполнить» сейчас.**
1. Вы сможете зарегистрироваться, используя адрес электронной почты.

[!INCLUDE [active-directory-b2c-html-templates](../../includes/active-directory-b2c-html-templates.md)]

## <a name="configure-dynamic-custom-page-content-uri"></a>Настройка динамического пользовательского содержимого страницы URI

Используя пользовательские политики Azure AD B2C, можно отправить параметр в траекторию URL или строку запроса. Передавая параметр в конечную точку HTML, вы можете динамически изменять содержимое страницы. Например, можно изменить фоновое изображение страницы регистрации или входа в Azure AD B2C на основе параметра, передаваемого из веб-приложения или мобильного приложения. Параметр может быть любым [разрешительницей претензий,](claim-resolver-overview.md)например идентификатором приложения, идентификатором языка или параметром строки пользовательского запроса, `campaignId`например.

### <a name="sending-query-string-parameters"></a>Параметры строки запроса

Для отправки параметров строки запроса в политике `ContentDefinitionParameters` [опоры стороны](relyingparty.md)добавьте элемент, показанный ниже.

```XML
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <UserJourneyBehaviors>
    <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        <Parameter Name="lang">{Culture:LanguageName}</Parameter>
        <Parameter Name="appId">{OIDC:ClientId}</Parameter>
    </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    ...
</RelyingParty>
```

В определении `LoadUri` содержания измените `https://<app_name>.azurewebsites.net/home/unified`значение до . Пользовательская `ContentDefinition` политика должна выглядеть следующим фрагментом кода:

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://<app_name>.azurewebsites.net/home/unified</LoadUri>
  ...
</ContentDefinition>
```

Когда Azure AD B2C загружает страницу, он делает звонок в конечную точку вашего веб-сервера:

```http
https://<app_name>.azurewebsites.net/home/unified?campaignId=123&lang=fr&appId=f893d6d3-3b6d-480d-a330-1707bf80ebea
```

### <a name="dynamic-page-content-uri"></a>Динамическое содержимое страницы URI

Содержимое может быть извлечено из разных мест в зависимости от используемых параметров. В своей конечной точке с поддержкой CORS настройте структуру папки для размещения содержимого. Например, можно организовать содержимое в следующей структуре. *Корневая папка/папка на язык/ваши HTML-файлы.* Например, пользовательская страница URI может выглядеть следующим образом:

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

Azure AD B2C отправляет два буквы кода `fr` ISO для языка, для французского языка:

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="next-steps"></a>Следующие шаги

Для получения дополнительной информации об элементах [reference guide for UI customization for user flows](customize-ui-overview.md)пользовательского интерфейса, которые могут быть настроены, см.
