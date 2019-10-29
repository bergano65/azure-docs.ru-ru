---
title: Руководство по интеграции единого входа Azure Active Directory с KnowledgeOwl | Документация Майкрософт
description: Сведения о настройке единого входа между Azure Active Directory и KnowledgeOwl.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2ae30996-864d-4872-90bc-f770e1ea159a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc7d481b757a76ba65e0c78a93bde1bc58ace7cc
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791637"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-knowledgeowl"></a>Руководство по интеграции единого входа Azure Active Directory с KnowledgeOwl

В этом руководстве описано, как интегрировать KnowledgeOwl с Azure Active Directory (Azure AD). Интеграция KnowledgeOwl с Azure AD обеспечивает следующие возможности.

* C помощью Azure AD вы можете контролировать доступ к KnowledgeOwl.
* Вы можете включить автоматический вход пользователей в KnowledgeOwl с помощью учетных записей Azure AD.
* Централизованное управление учетными записями через портал Azure.

Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее:

* подписка Azure AD Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* подписка KnowledgeOwl с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* KnowledgeOwl поддерживает единый вход, инициированный **пакетом обновления и IDP**.
* KnowledgeOwl поддерживает **JIT**-подготовку пользователей.

## <a name="adding-knowledgeowl-from-the-gallery"></a>Добавление KnowledgeOwl из коллекции

Чтобы настроить интеграцию KnowledgeOwl с Azure AD, нужно добавить KnowledgeOwl из коллекции в список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в поле поиска введите **KnowledgeOwl**.
1. Выберите **KnowledgeOwl** в области результатов и добавьте это приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.


## <a name="configure-and-test-azure-ad-single-sign-on-for-knowledgeowl"></a>Настройка и проверка единого входа Azure AD для KnowledgeOwl

Настройте и проверьте единый вход Azure AD в KnowledgeOwl с помощью тестового пользователя **B.Simon**. Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в KnowledgeOwl.

Чтобы настроить и проверить единый вход Azure AD в KnowledgeOwl, выполните действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
    * **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD с помощью пользователя B.Simon.
    * **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить пользователю B.Simon использовать единый вход Azure AD.
1. **[Настройка единого входа в KnowledgeOwl](#configure-knowledgeowl-sso)** необходима, чтобы настроить параметры единого входа на стороне приложения.
    * **[Создание тестового пользователя приложения KnowledgeOwl](#create-knowledgeowl-test-user)** требуется для того, чтобы в KnowledgeOwl существовал пользователь B.Simon, связанный с одноименным пользователем в Azure AD.
1. **[Проверка единого входа](#test-sso)** необходима, чтобы убедиться в корректной работе конфигурации.

## <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **KnowledgeOwl** найдите раздел **Управление** и выберите **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

   ![Правка базовой конфигурации SAML](common/edit-urls.png)

1. Если вы хотите настроить приложение в режиме, инициируемом **поставщиком удостоверений**, в разделе **Базовая конфигурация SAML** введите значения следующих полей.

    a. В текстовом поле **Идентификатор** введите URL-адрес в таком формате:
    
    | | |
    |-|-|
    | `https://app.knowledgeowl.com/sp`|
    | `https://app.knowledgeowl.com/sp/id/<unique ID>`|

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в таком формате:
    
    | | |
    |-|-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|

1. Чтобы настроить приложение для работы в режиме, инициируемом **поставщиком услуг**, щелкните **Задать дополнительные URL-адреса** и выполните следующие действия.

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате:
    
    | | |
    |-|-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|

    > [!NOTE]
    > Эти значения приведены для примера. Потребуется изменить эти значения, указав фактические идентификатор, URL-адрес ответа и URL-адрес для входа. Это описано ниже.

1. Приложение KnowledgeOwl ожидает проверочные утверждения SAML в определенном формате, который требует добавить настраиваемые сопоставления атрибутов в вашу конфигурацию атрибутов токена SAML. На следующем снимке экрана показан список атрибутов по умолчанию.

    ![image](common/default-attributes.png)

1. В дополнение к описанному выше приложение KnowledgeOwl ожидает несколько дополнительных атрибутов в ответе SAML, как показано ниже. Эти атрибуты также заранее заполнены, но вы можете изменить их в соответствии со своими требованиями.

    | ИМЯ | Исходный атрибут | Пространство имен |
    | ------------ | -------------------- | -----|
    | ssoid | user.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

1. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** найдите элемент **Сертификат (необработанный)** и щелкните **Скачать**, чтобы скачать сертификат. Сохраните этот сертификат на компьютере.

    ![Ссылка для скачивания сертификата](common/certificateraw.png)

1. Скопируйте требуемые URL-адреса из раздела **Настройка KnowledgeOwl**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

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

В этом разделе описано, как разрешить пользователю B. Simon применять единый вход Azure, предоставив ему доступ к KnowledgeOwl.

1. На портале Azure выберите **Корпоративные приложения**, а затем —**Все приложения**.
1. В списке приложений выберите **KnowledgeOwl**.
1. На странице "Обзор" приложения найдите раздел **Управление** и выберите **Пользователи и группы**.

   ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Ссылка "Добавить пользователя"](common/add-assign-user.png)

1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
1. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.
1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

## <a name="configure-knowledgeowl-sso"></a>Настройка единого входа KnowledgeOwl

1. В другом окне веб-браузера войдите на корпоративный сайт KnowledgeOwl в качестве администратора.

1. Выберите **Параметры** и затем **Безопасность**.

    ![Конфигурация KnowledgeOwl](./media/knowledgeowl-tutorial/configure1.png)

1. Прокрутите вниз до раздела **SAML SSO Integration** (Интеграция единого входа SAML) и выполните следующие действия:

    ![Конфигурация KnowledgeOwl](./media/knowledgeowl-tutorial/configure2.png)

    a. Выберите **Enable SAML SSO** (Включить единый вход SAML).

    b. Скопируйте **SP Entity ID** (Идентификатор сущности пакета обновления) и вставьте его в **Identifier (Entity ID)** (Идентификатор (сущности)) в разделе **Базовая конфигурация SAML** на портале Azure.

    c. Скопируйте значение **SP Login URL** (URL-адрес входа с помощью пакета обновления) и вставьте его в текстовые поля **"URL-адрес входа" и "URL-адрес ответа"** в разделе **Базовая конфигурация SAML** на портале Azure.

    d. В текстовое поле **IdP entityID** (Идентификатор сущности для поставщика удостоверений) вставьте значение **Идентификатор Azure AD**, скопированное на портале Azure.

    д. В текстовое поле **IdP Login URL** (URL-адрес для входа с помощью IdP) вставьте значение **URL-адрес входа**, скопированное на портале Azure.

    Е. В текстовое поле **IdP Logout URL** (URL-адрес для выхода с помощью IdP) вставьте значение **URL-адреса выхода**, скопированное на портале Azure.

    ж. Отправьте скачанный сертификат с портала Azure, выбрав **Upload IdP Certificate** (Отправить сертификат поставщика удостоверений).

    h. Выберите **Map SAML Attributes** (Сопоставить атрибуты SAML), чтобы сопоставить атрибуты, и выполните следующие действия:

    ![Конфигурация KnowledgeOwl](./media/knowledgeowl-tutorial/configure3.png)

    * Введите `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ssoid` в текстовом поле **SSO ID** (Идентификатор единого входа).
    * Введите `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` в текстовом поле **Username/Email** (Имя пользователя/электронная почта).
    * Введите `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` в текстовом поле **First Name** (Имя).
    * Введите `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` в текстовом поле **Last Name** (Фамилия).
    * Нажмите кнопку **Сохранить**

    i. В нижней части страницы нажмите кнопку **Сохранить** .

    ![Конфигурация KnowledgeOwl](./media/knowledgeowl-tutorial/configure4.png)

### <a name="create-knowledgeowl-test-user"></a>Создание тестового пользователя KnowledgeOwl

В этом разделе вы создадите в KnowledgeOwl пользователя B.Simon. Приложение KnowledgeOwl поддерживает JIT-подготовку пользователей, которая включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Если пользователь еще не существует в KnowledgeOwl, он создается после проверки подлинности.

> [!Note]
> Чтобы создать пользователя вручную, обратитесь к [группе поддержки KnowledgeOwl](mailto:support@knowledgeowl.com).

## <a name="test-sso"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку KnowledgeOwl на Панели доступа, вы автоматически войдете в приложение KnowledgeOwl, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Попробуйте использовать KnowledgeOwl с Azure AD](https://aad.portal.azure.com/)