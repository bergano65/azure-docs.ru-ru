---
title: Руководство по Интеграция Azure Active Directory с консолью администрирования Mimecast | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в консоль администрирования Mimecast.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/21/2020
ms.author: jeedes
ms.openlocfilehash: 6a9599c20905168b425675d0b028545143c7d19f
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2020
ms.locfileid: "91941081"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mimecast-admin-console"></a>Руководство по интеграции единого входа Azure Active Directory с консолью администрирования Mimecast

В этом руководстве описано, как интегрировать консоль администрирования Mimecast с Azure Active Directory (Azure AD). Интеграция консоли администрирования Mimecast с Azure AD обеспечивает следующие возможности.

* С помощью Azure AD вы можете контролировать доступ к консоли администрирования Mimecast.
* Вы можете включить автоматический вход пользователей в консоль администрирования Mimecast (единый вход) с учетными записями Azure AD.
* Централизованное управление учетными записями через портал Azure.

Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее.

* Подписка Azure AD. Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* Подписка на консоль администрирования Mimecast с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Консоль администрирования Mimecast поддерживает единый вход, инициированный **поставщиком услуг или поставщиком удостоверений**.
* После настройки консоли администрирования Mimecast вы сможете применять элемент управления сеансами, который защищает от хищения и несанкционированного доступа к конфиденциальным данным вашей организации в режиме реального времени. Управление сеансом является расширением функции условного доступа. [Узнайте, как применять управление сеансами с помощью Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-mimecast-admin-console-from-the-gallery"></a>Добавление консоли администрирования Mimecast из коллекции

Чтобы настроить интеграцию консоли администрирования Mimecast с Azure AD, необходимо добавить консоль администрирования Mimecast из коллекции в список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в поле поиска введите **Mimecast Admin Console**.
1. Выберите **Mimecast Admin Console** на панели результатов, а затем добавьте это приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.

## <a name="configure-and-test-azure-ad-single-sign-on-for-mimecast-admin-console"></a>Настройка и проверка единого входа Azure AD для консоли администрирования Mimecast

Настройте и проверьте единый вход Azure AD в консоли администрирования Mimecast с помощью тестового пользователя **B.Simon**. Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в консоли администрирования Mimecast.

Чтобы настроить и проверить единый вход Azure AD в консоли администрирования Mimecast, выполните действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
    1. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD с помощью пользователя B.Simon.
    1. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить пользователю B.Simon использовать единый вход Azure AD.
1. **[Настройка единого входа в консоль администрирования Mimecast](#configure-mimecast-admin-console-sso)** необходима, чтобы настроить параметры единого входа на стороне приложения.
    1. **[Создание тестового пользователя консоли администрирования Mimecast](#create-mimecast-admin-console-test-user)** требуется для того, чтобы в консоли администрирования Mimecast существовал пользователь B.Simon, связанный с представлением этого же пользователя в Azure AD.
1. **[Проверка единого входа](#test-sso)** позволяет убедиться в правильности конфигурации.

## <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Mimecast Admin Console** найдите раздел **Управление** и выберите **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

   ![Изменение базовой конфигурации SAML](common/edit-urls.png)

1. Если вы хотите настроить приложение в режиме, инициируемом поставщиком удостоверений, в разделе **Базовая конфигурация SAML** выполните следующие действия:

    а. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате:

    | Регион  |  Значение | 
    | --------------- | --------------- |
    | Европа          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | США   | `https://us-api.mimecast.com/sso/<accountcode>`|
    | ЮАР    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | Австралия       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | Внешнее расположение        | `https://jer-api.mimecast.com/sso/<accountcode>`|

    > [!NOTE]
    > Значение `accountcode` можно найти в консоли администрирования Mimecast в разделе **Account** > **Settings** > **Account Code** (Учетная запись — Параметры — Код учетной записи). Добавьте `accountcode` к идентификатору.

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес: 

    | Регион  |  Значение | 
    | --------------- | --------------- | 
    | Европа          | `https://eu-api.mimecast.com/login/saml`|
    | США   | `https://us-api.mimecast.com/login/saml`|
    | ЮАР    | `https://za-api.mimecast.com/login/saml`|
    | Австралия       | `https://au-api.mimecast.com/login/saml`|
    | Внешнее расположение        | `https://jer-api.mimecast.com/login/saml`|

1. если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**.

    В текстовом поле **URL-адрес для входа** введите URL-адрес. 

    | Регион  |  Значение | 
    | --------------- | --------------- | 
    | Европа          | `https://login-eu.mimecast.com/administration/app/#/administration-dashboard`|
    | США   | `https://login-us.mimecast.com/administration/app/#/administration-dashboard`|
    | ЮАР    | `https://login-za.mimecast.com/administration/app/#/administration-dashboard`|
    | Австралия       | `https://login-au.mimecast.com/administration/app/#/administration-dashboard`|
    | Внешнее расположение        | `https://login-jer.mimecast.com/administration/app/#/administration-dashboard`|

1. Выберите команду **Сохранить**.

1. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** нажмите кнопку "Копировать", чтобы скопировать **URL-адрес метаданных федерации приложений** и сохранить его на компьютере.

    ![Ссылка для скачивания сертификата](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

В этом разделе описано, как на портале Azure создать тестового пользователя с именем B.Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.
1. В верхней части экрана выберите **Новый пользователь**.
1. В разделе **Свойства пользователя** выполните следующие действия.
   1. В поле **Имя** введите `B.Simon`.  
   1. В поле **Имя пользователя** введите username@companydomain.extension. Например, `B.Simon@contoso.com`.
   1. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.
   1. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю B.Simon использовать единый вход Azure, предоставив этому пользователю доступ к консоли администрирования Mimecast.

1. На портале Azure выберите **Корпоративные приложения**, а затем — **Все приложения**.
1. Из списка приложений выберите **Консоль администрирования Mimecast**.
1. На странице "Обзор" приложения найдите раздел **Управление** и выберите **Пользователи и группы**.

   ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Ссылка "Добавить пользователя"](common/add-assign-user.png)

1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
1. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.
1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

## <a name="configure-mimecast-admin-console-sso"></a>Настройка консоли администрирования Mimecast с поддержкой единого входа

1. В другом окне веб-браузера войдите в консоль администрирования Mimecast.

1. Перейдите в раздел **Administration** > **Services** > **Applications** (Администрирование — Службы — Приложения).

    ![Снимок экрана: окно Mimecast с выбранным пунктом Applications (Приложения)](./media/mimecast-admin-console-tutorial/services.png)

1. Выберите вкладку **Authentication Profiles** (Профили проверки подлинности).
    
    ![Снимок экрана: вкладка Authentication (Аутентификация) с выбранным параметром Authentication Profiles (Профили аутентификации)](./media/mimecast-admin-console-tutorial/authentication-profiles.png)

1. Выберите вкладку **New Authentication Profile** (Новый профиль проверки подлинности).

    ![Снимок экрана: вкладка New Authentication Profile (Новый профиль аутентификации)](./media/mimecast-admin-console-tutorial/new-authenticatio-profile.png)

1. Укажите допустимое описание в текстовом поле **Description** (Описание) и установите флажок **Enforce SAML Authentication for Administration Console** (Принудительная проверка подлинности SAML для консоли администрирования).

    ![Снимок экрана: флажок Enforce SAML Authentication for Administration Console (Принудительная проверка подлинности SAML для консоли администрирования)](./media/mimecast-admin-console-tutorial/selecting-admin-consle.png)

1. На странице **SAML Configuration for Administration Console** (Настройка SAML для консоли администрирования) сделайте следующее:

    ![Снимок экрана: страница SAML Configuration for Administration Console (Настройка SAML для консоли администрирования)](./media/mimecast-admin-console-tutorial/sso-settings.png)

    а. Для **Provider** (Поставщик) выберите **Azure Active Directory**.

    b. В текстовое поле **Metadata URL** (URL-адрес метаданных) вставьте значение **URL-адреса метаданных федерации приложения**, скопированное на портале Azure.

    c. Щелкните **Импорт**. После импорта URL-адреса метаданных поля будут заполнены автоматически, никакие дополнительные действия с ними не требуются.

    d. Не забудьте снять флажки **Use Password protected Context** (Использовать защищенный паролем контекст) и **Use Integrated Authentication Context** (Использовать интегрированный контекст проверки подлинности).

    д) Выберите команду **Сохранить**.

### <a name="create-mimecast-admin-console-test-user"></a>Создание тестового пользователя консоли администрирования Mimecast

1. В другом окне веб-браузера войдите в консоль администрирования Mimecast.

1. Перейдите к разделу **Administration** > **Directories** > **Internal Directories** (Администрирование — Каталоги — Внутренние каталоги).

    ![Снимок экрана: окно Mimecast с выбранным пунктом Internal Directories (Внутренние каталоги)](./media/mimecast-admin-console-tutorial/internal-directories.png)

1. Если ниже указаны домены, выберите нужный, иначе создайте новый с помощью ссылки **New Domain**.

    ![Снимок экрана: выбранный домен](./media/mimecast-admin-console-tutorial/domain-name.png)

1. Щелкните вкладку **New Address** (Новый адрес).

    ![Снимок экрана: вкладка New Address (Новый адрес)](./media/mimecast-admin-console-tutorial/new-address.png)

1. Введите обязательные сведения о пользователе на следующей странице.

    ![Снимок экрана: страница для ввода описанных значений](./media/mimecast-admin-console-tutorial/user-information.png)

    а. В текстовом поле **Email address** (Адрес электронной почты) введите адрес электронной почты пользователя, например `B.Simon@yourdomainname.com`.

    b. В текстовом поле **GLobal Name** (Глобальное имя) введите **полное имя** пользователя.

    c. В текстовые поля **Password** (Пароль) и **Confirm Password** (Подтверждение пароля) введите пароль пользователя.

    d. Установите флажок **Force Change at Login** (Потребовать смену при входе).

    д) Выберите команду **Сохранить**.

    е) Чтобы назначить пользователю роли, щелкните **Role Edit** (Изменить роли) и выберите роль, которая лучше всего соответствует требованиям вашей организации.

    ![Снимок экрана: раздел Address Settings (Параметры адреса) с пунктом Role Edit (Изменить роли)](./media/mimecast-admin-console-tutorial/assign-role.png)

## <a name="test-sso"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку "Mimecast Admin Console" (Консоль администрирования Mimecast) на панели доступа, вы автоматически войдете в приложение консоли администрирования Mimecast, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Попробуйте работу консоли администрирования Mimecast с Azure AD](https://aad.portal.azure.com/)

- [Что такое управление сеансами в Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Защита приложений с помощью функции управления настройками условного доступа для приложений в Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
