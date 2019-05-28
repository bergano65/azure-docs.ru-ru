---
title: Руководство по Интеграция Azure Active Directory с KnowledgeOwl | Документы Майкрософт
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
ms.date: 04/10/2019
ms.author: jeedes
ms.openlocfilehash: b57beb18b8bf7d82694f89e131d79d15c6fcd6bc
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64728298"
---
# <a name="tutorial-azure-active-directory-integration-with-knowledgeowl"></a>Руководство по Интеграция Azure Active Directory с KnowledgeOwl

В этом руководстве описано, как интегрировать KnowledgeOwl с Azure Active Directory (Azure AD).
Интеграция Azure AD с приложением KnowledgeOwl обеспечивает следующие преимущества:

* C помощью Azure AD вы можете контролировать доступ к KnowledgeOwl.
* Вы можете включить автоматический вход пользователей в KnowledgeOwl (единый вход) с помощью учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с KnowledgeOwl, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/));
* подписка KnowledgeOwl с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* KnowledgeOwl поддерживает единый вход, инициированный **пакетом обновления и IDP**.
* KnowledgeOwl поддерживает **JIT**-подготовку пользователей.

## <a name="adding-knowledgeowl-from-the-gallery"></a>Добавление KnowledgeOwl из коллекции

Чтобы настроить интеграцию KnowledgeOwl с Azure AD, нужно добавить KnowledgeOwl из коллекции в список управляемых приложений SaaS.

**Чтобы добавить KnowledgeOwl из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **KnowledgeOwl**, выберите **KnowledgeOwl** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![KnowledgeOwl в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в KnowledgeOwl с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в KnowledgeOwl.

Чтобы настроить и проверить единый вход Azure AD в KnowledgeOwl, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в KnowledgeOwl](#configure-knowledgeowl-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя приложения KnowledgeOwl](#create-knowledgeowl-test-user)** требуется для того, чтобы в KnowledgeOwl существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в KnowledgeOwl, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **KnowledgeOwl** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. Если вы хотите настроить приложение в режиме, инициируемом **поставщиком удостоверений**, в разделе **Базовая конфигурация SAML** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения KnowledgeOwl](common/idp-intiated.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в таком формате:

    ||
    |-|
    | `https://app.knowledgeowl.com/sp`|
    | `https://app.knowledgeowl.com/sp/id/<unique ID>`|

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в таком формате:

    ||
    |-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|

5. Чтобы настроить приложение для работы в режиме, инициируемом **поставщиком услуг**, щелкните **Задать дополнительные URL-адреса** и выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения KnowledgeOwl](common/metadata-upload-additional-signon.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате:

    ||
    |-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|

    > [!NOTE]
    > Эти значения приведены для примера. Потребуется изменить эти значения, указав фактические идентификатор, URL-адрес ответа и URL-адрес для входа. Это описано ниже.

6. Приложение KnowledgeOwl ожидает проверочные утверждения SAML в определенном формате, который требует добавления сопоставлений настраиваемых атрибутов в конфигурацию атрибутов токена SAML. На следующем снимке экрана показан список атрибутов по умолчанию. Нажмите кнопку **Изменить**, чтобы открыть диалоговое окно **Атрибуты пользователя**.

    ![image](common/edit-attribute.png)

7. В разделе **Утверждения пользователя** диалогового окна **Атрибуты пользователя** измените утверждения, щелкнув значок **Изменить**, или добавьте утверждение, нажав кнопку **Добавить новое утверждение**, чтобы настроить атрибут токена SAML, как показано на рисунке выше, и выполните следующие действия. 

    | ИМЯ | Исходный атрибут | Пространство имен |
    | ------------ | -------------------- | -----|
    | ssoid | user.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

    a. Щелкните **Добавить новое утверждение**, чтобы открыть диалоговое окно **Управление утверждениями пользователя**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.

    c. В списке **Пространство имен** введите значение пространства имен, отображаемое для этой строки.

    d. В качестве источника выберите **Атрибут**.

    д. В списке **Атрибут источника** введите значение атрибута, отображаемое для этой строки.

    Е. Нажмите кнопку **ОК**.

    ж. Выберите команду **Сохранить**.

8. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Загрузить**, чтобы загрузить требуемый **сертификат (необработанный)** из предложенных вариантов и сохранить его на компьютере.

    ![Ссылка для скачивания сертификата](common/certificateraw.png)

9. Скопируйте требуемые URL-адреса из раздела **Настройка KnowledgeOwl**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    b. Идентификатор Azure AD

    c. URL-адрес выхода.

### <a name="configure-knowledgeowl-single-sign-on"></a>Настройка единого входа KnowledgeOwl

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

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    а. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите `brittasimon@yourcompanydomain.extension`.  
    Например BrittaSimon@contoso.com.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив доступ к KnowledgeOwl.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем **KnowledgeOwl**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **KnowledgeOwl**.

    ![Ссылка на KnowledgeOwl в списке "Приложения"](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-knowledgeowl-test-user"></a>Создание тестового пользователя KnowledgeOwl

В этом разделе вы создадите в KnowledgeOwl пользователя Britta Simon. Приложение KnowledgeOwl поддерживает JIT-подготовку пользователей, которая включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Если пользователь еще не существует в KnowledgeOwl, он создается после проверки подлинности.

> [!Note]
> Чтобы создать пользователя вручную, обратитесь к [группе поддержки KnowledgeOwl](mailto:support@knowledgeowl.com).

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку KnowledgeOwl на Панели доступа, вы автоматически войдете в приложение KnowledgeOwl, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)