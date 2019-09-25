---
title: Начало работы с настраиваемыми политиками — Azure Active Directory B2C
description: Узнайте, как приступить к работе с пользовательскими политиками в Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 8e858869d742120138e7997ce21d9e4cca93ed9b
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71264364"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>Начало работы с настраиваемыми политиками в Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[Пользовательские политики](active-directory-b2c-overview-custom.md) — это файлы конфигурации, определяющие поведение клиента Azure Active Directory B2C (Azure AD B2C). В рамках этой статьи вы создадите настраиваемую политику, которая будет поддерживать регистрацию и вход с помощью локальной учетной записи с использованием адреса электронной почты и пароля. Вы также можете подготовить среду для добавления поставщиков удостоверений.

## <a name="prerequisites"></a>Предварительные требования

- [Создайте Azure AD B2C клиент](tutorial-create-tenant.md) , связанный с подпиской Azure, если у вас его еще нет.
- [Зарегистрируйте приложение](tutorial-register-applications.md) в созданном клиенте, чтобы он мог взаимодействовать с Azure AD B2C.
- Выполните действия, описанные в статье [Настройка регистрации и входа с помощью учетной записи Facebook](active-directory-b2c-setup-fb-app.md) для настройки приложения Facebook.

## <a name="add-signing-and-encryption-keys"></a>Добавление ключей подписи и шифрования

1. Войдите на [портал Azure](https://portal.azure.com)
1. Используйте фильтр **каталог и подписка** в верхнем меню, чтобы выбрать каталог, содержащий клиент Azure AD B2C.
1. В меню слева выберите **Azure AD B2C**. Или выберите **все службы** , найдите и выберите **Azure AD B2C**.
1. На странице "Обзор" выберите **Identity Experience Framework**.

### <a name="create-the-signing-key"></a>Создание ключа подписи

1. Выберите **Ключи политики**, а затем щелкните **Добавить**.
1. Для пункта **Параметры** выберите `Generate`.
1. В разделе **Имя** введите `TokenSigningKeyContainer`. Префикс `B2C_1A_` может быть добавлен автоматически.
1. Для параметра **Тип ключа** выберите **RSA**.
1. Для параметра **Использование ключа** выберите **Подпись**.
1. Нажмите кнопку **Создать**.

### <a name="create-the-encryption-key"></a>Создание ключа шифрования

1. Выберите **Ключи политики**, а затем щелкните **Добавить**.
1. Для пункта **Параметры** выберите `Generate`.
1. В разделе **Имя** введите `TokenEncryptionKeyContainer`. Префикс `B2C_1A`_ может быть добавлен автоматически.
1. Для параметра **Тип ключа** выберите **RSA**.
1. Для параметра **Использование ключа** задайте значение **Шифрование**.
1. Нажмите кнопку **Создать**.

### <a name="create-the-facebook-key"></a>Создание ключа Facebook

Добавьте [секрет](active-directory-b2c-setup-fb-app.md) приложения Facebook в качестве ключа политики. Вы можете использовать секрет приложения созданного приложения в рамках предварительных требований этой статьи.

1. Выберите **Ключи политики**, а затем щелкните **Добавить**.
1. Для пункта **Параметры** выберите `Manual`.
1. Для параметра **Имя** введите `FacebookSecret`. Префикс `B2C_1A_` может быть добавлен автоматически.
1. В качестве **секрета**введите *секрет* приложения Facebook из Developers.Facebook.com. Это значение является секретом, а не ИДЕНТИФИКАТОРом приложения.
1. Для параметра **Использование ключа** выберите **Подпись**.
1. Нажмите кнопку **Создать**.

## <a name="register-identity-experience-framework-applications"></a>Регистрация приложений инфраструктуры процедур идентификации

Для Azure AD B2C необходимо зарегистрировать два приложения, которые используются для регистрации и входа пользователей: IdentityExperienceFramework (веб-приложение) и ProxyIdentityExperienceFramework (собственное приложение) с делегированным разрешением IdentityExperienceFramework. Локальные учетные записи хранятся только в клиенте. Пользователи регистрируются в системе, используя уникальное сочетание адреса электронной почты и пароля, чтобы получить доступ к зарегистрированным в клиенте приложениям.

### <a name="register-the-identityexperienceframework-application"></a>Регистрация приложения IdentityExperienceFramework

1. Выберите **все службы** в левом верхнем углу портал Azure.
1. В поле поиска введите `Azure Active Directory`.
1. В результатах поиска выберите **Azure Active Directory**.
1. В разделе **Управление** в меню слева выберите **Регистрация приложений (прежние версии)** .
1. Выберите **Регистрация нового приложения**.
1. Для параметра **Имя** введите `IdentityExperienceFramework`.
1. Для параметра **Тип приложения** выберите **Веб-приложение или API**.
1. Для параметра **URL-адрес для входа** введите `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, где `your-tenant-name` — это доменное имя клиента Azure AD B2C. Всем URL-адресам следует использовать [b2clogin.com](b2clogin.md).
1. Нажмите кнопку **Создать**. По завершении операции создания скопируйте идентификатор приложения и сохраните его для последующего использования.

### <a name="register-the-proxyidentityexperienceframework-application"></a>Регистрация приложения ProxyIdentityExperienceFramework

1. В **Регистрация приложений (устаревший)** выберите **Регистрация нового приложения**.
1. Для параметра **Имя** введите `ProxyIdentityExperienceFramework`.
1. Для параметра **Тип приложения** выберите **Собственный**.
1. Для параметра **URI перенаправления** введите `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, где `your-tenant-name` — это ваш клиент Azure AD B2C.
1. Нажмите кнопку **Создать**. По завершении операции создания скопируйте идентификатор приложения и сохраните его для последующего использования.
1. Выберите **Параметры**, затем выберите **необходимые разрешения**и нажмите кнопку **добавить**.
1. Выберите **выбрать API**, выполните поиск и выберите **IdentityExperienceFramework**, а затем нажмите кнопку **выбрать**.
1. Установите флажок рядом с элементом **Access IdentityExperienceFramework** (Доступ к IdentityExperienceFramework), щелкните **Выбрать**, а затем — **Готово**.
1. Выберите **предоставить разрешения**, а затем подтвердите, выбрав **Да**.

## <a name="custom-policy-starter-pack"></a>Начальный пакет настраиваемой политики

Настраиваемые политики — это набор XML-файлов, отправляемых в клиент Azure AD B2C для определения технических профилей и путей взаимодействия пользователя. Мы предоставляем начальные пакеты с несколькими предварительно созданными политиками, чтобы быстро приступить к работе. Каждый из этих начальных пакетов содержит наименьшее количество технических профилей и путей взаимодействия пользователей, необходимых для реализации описанных сценариев:

- **LocalAccounts** — включает использование только локальных учетных записей.
- **СоЦиалаккаунтс** — включает использование только социальных (или федеративных) учетных записей.
- **SocialAndLocalAccounts** — включает использование учетных записей как локальной, так и социальных сетей.
- **СоЦиаландлокалаккаунтсвисмфа** — включает параметры социальных сетей, локальных и многофакторных проверок подлинности.

Каждый начальный пакет содержит:

- **Базовый файл** — несколько изменений необходимы для базы. Пример: *TrustFrameworkBase. XML*
- **Файл расширения** — здесь выполняется большинство изменений конфигурации. Пример: *TrustFrameworkExtensions. XML*
- **Файлы проверяющей стороны** — файлы конкретной задачи, которые вызываются приложением. Примеры: *SignUpOrSignin. XML*, *ProfileEdit. XML*, *PasswordReset. XML*

В этой статье вы измените файлы настраиваемой политики XML в **SocialAndLocalAccounts** Starter Pack. Если вам нужен редактор XML, попробуйте [Visual Studio Code](https://code.visualstudio.com/download), простой межплатформенный редактор.

### <a name="get-the-starter-pack"></a>Получение начального пакета

Получите начальные пакеты настраиваемой политики из GitHub, а затем обновите XML-файлы в SocialAndLocalAccounts Starter Pack, указав имя клиента Azure AD B2C.

1. [Скачайте zip-файл](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) или создайте клон репозитория:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. Во всех файлах в каталоге **SocialAndLocalAccounts** замените строку `yourtenant` именем клиента Azure AD B2C.

    Например, если имя клиента B2C — *контосотенант*, все экземпляры `yourtenant.onmicrosoft.com` станут `contosotenant.onmicrosoft.com`.

### <a name="add-application-ids-to-the-custom-policy"></a>Добавление идентификаторов приложений в настраиваемую политику

В файл расширений *TrustFrameworkExtensions* добавьте идентификаторы приложений.

1. Откройте `SocialAndLocalAccounts/` `<TechnicalProfile Id="login-NonInteractive">`и **найдитеэлемент.`TrustFrameworkExtensions.xml`**
1. Замените оба экземпляра `IdentityExperienceFrameworkAppId` идентификатором приложения приложения IdentityExperienceFramework, созданного ранее.
1. Замените оба экземпляра `ProxyIdentityExperienceFrameworkAppId` идентификатором приложения приложения ProxyIdentityExperienceFramework, созданного ранее.
1. Сохраните файл.

## <a name="upload-the-policies"></a>Отправка политик

1. Выберите элемент меню **инфраструктура процедур идентификации** в клиенте B2C в портал Azure.
1. Выберите **Отправить настраиваемую политику**.
1. В этом порядке отправьте файлы политики:
    1. *TrustFrameworkBase. XML*
    1. *TrustFrameworkExtensions. XML*
    1. *SignUpOrSignin. XML*
    1. *ProfileEdit. XML*
    1. *PasswordReset. XML*

При передаче файлов Azure добавляет префикс `B2C_1A_` в каждый из них.

> [!TIP]
> Если редактор XML поддерживает проверку, проверьте файлы на соответствие `TrustFrameworkPolicy_0.3.0.0.xsd` схеме XML, расположенной в корневом каталоге начального пакета. При проверке по схеме XML определяются ошибки перед отправкой.

## <a name="test-the-custom-policy"></a>Проверка пользовательской политики

1. В разделе **пользовательские политики**выберите **B2C_1A_signup_signin**.
1. Для параметра **выбрать приложение** на странице Обзор настраиваемой политики выберите веб-приложение с именем *APP1* , которое вы зарегистрировали ранее.
1. Убедитесь, что **URL-адрес ответа** имеет значение `https://jwt.ms`.
1. Выберите **Запустить сейчас**.
1. Зарегистрируйтесь, используя адрес электронной почты.
1. Выберите **выполнить сейчас** .
1. Войдите в систему с помощью той же учетной записи, чтобы подтвердить правильность конфигурации.

## <a name="add-facebook-as-an-identity-provider"></a>Добавление Facebook в качестве поставщика удостоверений

1. В файле замените значение`client_id` на идентификатор приложения Facebook: **`TrustFrameworkExtensions.xml`** `SocialAndLocalAccounts/`

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

1. Отправьте файл *TrustFrameworkExtensions.xml* в клиент.
1. В разделе **пользовательские политики**выберите **B2C_1A_signup_signin**.
1. Выберите **выполнить сейчас** и выберите Facebook для входа с помощью Facebook и протестируйте настраиваемую политику.

## <a name="next-steps"></a>Следующие шаги

Затем попробуйте добавить Azure Active Directory (Azure AD) в качестве поставщика удостоверений. Базовый файл, используемый в этом руководство по началу работы, уже содержит часть содержимого, необходимого для добавления других поставщиков удостоверений, таких как Azure AD.

Сведения о настройке Azure AD как и поставщика удостоверений см. [в статье Настройка регистрации и входа с использованием учетной записи Azure Active Directory с помощью Active Directory B2C настраиваемых политик](active-directory-b2c-setup-aad-custom.md).
