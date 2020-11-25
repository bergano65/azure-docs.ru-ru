---
title: Настройка пользовательского интерфейса приложения с помощью настраиваемой политики
titleSuffix: Azure AD B2C
description: Дополнительные сведения о настройке пользовательского интерфейса с помощью настраиваемой политики в Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 340c9629af89bfacb85b37503743fc5770070ae3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2020
ms.locfileid: "95990918"
---
# <a name="customize-the-user-interface-of-your-application-using-a-custom-policy-in-azure-active-directory-b2c"></a>Настройка пользовательского интерфейса приложения с помощью настраиваемой политики в Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Выполнив действия, описанные в этой статье, вы создадите настраиваемую политику регистрации и входа в систему с фирменной символикой и внешним видом. Благодаря Azure Active Directory B2C (Azure AD B2C) вы получаете практически полный контроль над содержимым HTML и CSS, которое представляется пользователям. При использовании настраиваемой политики вы настраиваете пользовательский интерфейс в XML-файле, а не с помощью элементов управления на портале Azure.

## <a name="prerequisites"></a>Предварительные требования

Выполните шаги, описанные в статье [Начало работы с настраиваемыми политиками в Azure Active Directory B2C](custom-policy-get-started.md). Для регистрации и входа с использованием локальных учетных записей необходимо иметь рабочую настраиваемую политику.

[!INCLUDE [active-directory-b2c-html-how-to](../../includes/active-directory-b2c-html-how-to.md)]

### <a name="4-modify-the-extensions-file"></a>4. изменение файла расширений

Чтобы настроить настройку пользовательского интерфейса, скопируйте **контентдефинитион** и его дочерние элементы из базового файла в файл Extensions.

1. Откройте базовый файл политики. Например, <em>`SocialAndLocalAccounts/`**`TrustFrameworkBase.xml`**</em>. Этот базовый файл является одним из файлов политики, включенных в начальный пакет пользовательской политики, который необходимо получить в предварительных требованиях, чтобы приступить [к работе с пользовательскими политиками](./custom-policy-get-started.md).
1. Найдите и скопируйте содержимое элемента **ContentDefinitions**.
1. Откройте файл расширения, Например, *TrustFrameworkExtensions.xml*. Найдите элемент **BuildingBlocks**. Если такой элемент не существует, добавьте его.
1. Вставьте все содержимое элемента **ContentDefinitions** , скопированного как дочерний элемент **BuildingBlocks** .
1. Найдите элемент **ContentDefinition**, содержащий `Id="api.signuporsignin"` в скопированном файле XML.
1. Измените значение **LoadUri** на URL-адрес файла HTML, который был передан в хранилище. Например, `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.

    Ваша пользовательская политика должна выглядеть, как в следующем фрагменте кода:

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

### <a name="5-upload-and-test-your-updated-custom-policy"></a>5. Отправьте и протестируйте обновленную настраиваемую политику

#### <a name="51-upload-the-custom-policy"></a>5,1. Отправка настраиваемой политики

1. Убедитесь, что используете каталог с клиентом Azure AD B2C, выбрав фильтр **Каталог и подписка** в меню вверху и каталог с вашим клиентом.
1. Найдите и выберите **Azure AD B2C**.
1. В разделе **Политики** выберите **Identity Experience Framework**.
1. Выберите **Отправить пользовательскую политику**.
1. Выполните отправку ранее измененного файла расширений.

#### <a name="52-test-the-custom-policy-by-using-run-now"></a>5,2. Протестируйте настраиваемую политику с помощью команды " **Запустить сейчас** "

1. Выберите переданную политику и щелкните **Запустить сейчас**.
1. Вы сможете зарегистрироваться, используя адрес электронной почты.

[!INCLUDE [active-directory-b2c-html-templates](../../includes/active-directory-b2c-html-templates.md)]

## <a name="configure-dynamic-custom-page-content-uri"></a>Настройка динамического URI содержимого настраиваемой страницы

С помощью Azure AD B2C пользовательских политик можно отправить параметр в URL-пути или в строку запроса. Передавая параметр в конечную точку HTML, вы можете динамически изменять содержимое страницы. Например, можно изменить фоновое изображение страницы регистрации или входа в Azure AD B2C на основе параметра, передаваемого из веб-приложения или мобильного приложения. Параметр может быть любым [арбитром утверждений](claim-resolver-overview.md), таким как идентификатор приложения, идентификатор языка или пользовательский параметр строки запроса, например `campaignId` .

### <a name="sending-query-string-parameters"></a>Отправка параметров строки запроса

Чтобы отправить параметры строки запроса, в [политике проверяющей стороны](relyingparty.md)добавьте `ContentDefinitionParameters` элемент, как показано ниже.

```xml
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

В определении содержимого измените значение `LoadUri` на `https://<app_name>.azurewebsites.net/home/unified` . Ваша пользовательская политика `ContentDefinition` должна выглядеть, как в следующем фрагменте кода:

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://<app_name>.azurewebsites.net/home/unified</LoadUri>
  ...
</ContentDefinition>
```

Когда Azure AD B2C загружает страницу, она вызывает конечную точку веб-сервера:

```http
https://<app_name>.azurewebsites.net/home/unified?campaignId=123&lang=fr&appId=f893d6d3-3b6d-480d-a330-1707bf80ebea
```

### <a name="dynamic-page-content-uri"></a>URI содержимого динамической страницы

Содержимое можно извлечь из разных мест в зависимости от используемых параметров. В конечной точке с поддержкой CORS Настройте структуру папок для размещения содержимого. Например, можно организовать содержимое в следующей структуре. Корневая *Папка или папка для каждого языка/HTML-файлов*. Например, URI настраиваемой страницы может выглядеть следующим образом:

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

Azure AD B2C отправляет двухбуквенный код ISO для языка, `fr` французский:

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения об элементах пользовательского интерфейса, которые можно настроить, см. в [справочном руководстве по настройке пользовательского интерфейса для потоков пользователей](customize-ui-overview.md).