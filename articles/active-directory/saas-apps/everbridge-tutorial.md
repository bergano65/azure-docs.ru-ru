---
title: Руководство по интеграции Azure Active Directory с Everbridge | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Everbridge.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 58d7cd22-98c0-4606-9ce5-8bdb22ee8b3e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: f8dd11e7fb0b9fda0e0f1c7d3f794f6bfd766cdf
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231467"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Руководство по интеграции Azure Active Directory с Everbridge

В этом руководстве описано, как интегрировать Everbridge с Azure Active Directory (Azure AD).
Интеграция Everbridge с Azure AD дает следующие возможности:

* Управление доступом к Everbridge в Azure AD.
* Включение автоматического входа пользователей в Everbridge с помощью учетной записи Azure Active Directory. Это управление доступом называется единым входом (SSO).
* Централизованное управление учетными записями с помощью портала Azure.
Дополнительные сведения об интеграции приложения SaaS с Azure AD см. в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Everbridge, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/));
* подписка Everbridge, использующая единый вход.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Everbridge поддерживает единый вход, инициируемый поставщиком удостоверений.

## <a name="add-everbridge-from-the-azure-marketplace"></a>Добавление Everbridge из Azure Marketplace

Чтобы настроить интеграцию Everbridge с Azure AD, добавьте Everbridge из Azure Marketplace в список управляемых приложений SaaS.

Чтобы добавить Everbridge из Azure Marketplace, сделайте следующее:

1. На [портале Azure](https://portal.azure.com) в области навигации слева выберите **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна выберите **Новое приложение**.

    ![Кнопка "Новое приложение"](common/add-new-app.png)

4. В поле поиска введите **Everbridge**. Выберите **Everbridge** на панели результатов, а затем выберите **Добавить**.

     ![Everbridge в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в приложение Everbridge с использованием тестового пользователя Britta Simon.
Для обеспечения работы единого входа установите связь между пользователем Azure Active Directory и соответствующим пользователем в Everbridge.

Чтобы настроить и проверить единый вход Azure AD в Everbridge, выполните действия в следующих стандартных блоках:

- [Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on) необходима, чтобы пользователи могли использовать эту функцию.
- [Настройка единого входа в Everbridge в качестве портала менеджера](#configure-everbridge-as-everbridge-manager-portal-single-sign-on) необходима, чтобы настроить параметры единого входа на стороне приложения.
- [Настройка единого входа в Everbridge в качестве портала участника](#configure-everbridge-as-everbridge-member-portal-single-sign-on) необходима, чтобы настроить параметры единого входа на стороне приложения.
- [Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user) требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
- [Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user) необходимо, чтобы разрешить Britta Simon использовать единый вход Azure AD.
- [Создание тестового пользователя Everbridge](#create-an-everbridge-test-user) требуется, чтобы в Everbridge существовал пользователь Britta Simon, связанный с представлением этого же пользователя в Azure AD.
- [Проверка единого входа](#test-single-sign-on) позволяет убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure Active Directory в Everbridge, сделайте следующее:

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Everbridge** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

    >[!NOTE]
    >Настройте приложение в качестве портала менеджера *или* в качестве портала участника на портале Azure и портале Everbridge.

4. Чтобы настроить приложение **Everbridge** в качестве **портала менеджера Everbridge** в разделе **Базовая конфигурация SAML**, сделайте следующее:

    ![Сведения о домене и URL-адресах единого входа приложения Everbridge](common/idp-intiated.png)

    a. В поле **Идентификатор** введите URL-адрес в следующем формате: `https://sso.everbridge.net/<API_Name>`

    b. В поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias`

    > [!NOTE]
    > Эти значения приведены в качестве примера. Измените их на фактические значения идентификатора и URL-адреса ответа. Чтобы получить эти значения, обратитесь к [группе поддержки Everbridge](mailto:support@everbridge.com). Вы также можете посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

5. Чтобы настроить приложение **Everbridge** в качестве **портала участника Everbridge**, в разделе **Базовая конфигурация SAML** сделайте следующее:

  * Если вы хотите настроить приложение в режиме, инициируемом поставщиком удостоверений, выполните следующие действия:

     ![Сведения о домене и URL-адресах для единого входа в приложении Everbridge для режима, инициируемого поставщиком удостоверений](common/idp-intiated.png)

    a. В поле **Идентификатор** введите URL-адрес в следующем формате: `https://sso.everbridge.net/<API_Name>/<Organization_ID>`

    b. В поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias`

   * Если вы хотите настроить приложение в режиме, инициируемом поставщиком услуг, выберите **Задать дополнительные URL-адреса** и выполните следующие действия:

     ![Сведения о домене и URL-адресах для единого входа в приложении Everbridge для режима, инициируемого поставщиком услуг](common/both-signonurl.png)

     a. В поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true`

     > [!NOTE]
     > Эти значения приведены в качестве примера. Измените их на фактические значения идентификатора и URL-адресов ответа и входа. Чтобы получить эти значения, обратитесь к [группе поддержки Everbridge](mailto:support@everbridge.com). Вы также можете посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

6. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** нажмите кнопку **Скачать**, чтобы скачать **XML метаданных федерации**. Сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

7. Требуемые URL-адреса можно скопировать из раздела **Настройка Everbridge**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    - URL-адрес входа.
    - Идентификатор Azure AD
    - URL-адрес выхода.

### <a name="configure-everbridge-as-everbridge-manager-portal-single-sign-on"></a>Настройка единого входа в Everbridge в качестве портала менеджера

Чтобы настроить единый вход в **Everbridge** в качестве **портала менеджера**, выполните следующие действия:
 
1. В другом окне веб-браузера войдите в приложение Everbridge с правами администратора.

1. В верхнем меню выберите вкладку **Settings** (Параметры). В разделе **Security** (Безопасность) выберите **Single Sign-On** (Единый вход).
   
     ![Настройка единого входа](./media/everbridge-tutorial/tutorial_everbridge_002.png)
   
     a. В поле **Name** (Имя) введите имя поставщика идентификаторов. Например, название вашей компании.
   
     b. В поле **API Name** (Имя API) введите имя API.
   
     c. Затем нажмите кнопку **Choose File** (Выбор файла), чтобы передать файл метаданных, скачанный с портала Azure.
   
     d. В поле **SAML Identity Location** (Расположение удостоверения SAML) выберите значение **Identity is in the NameIdentifier element of the Subject statement** (Удостоверение находится в элементе NameIdentifier оператора Subject).
   
     д. В поле **Identity Provider Login URL** (URL-адрес для входа поставщика удостоверений) вставьте значение **URL-адреса входа**, скопированное на портале Azure.
   
     Е. В поле **Service Provider Initiated Request Binding** (Связывание запросов, инициируемых поставщиком услуг) выберите значение **HTTP Redirect** (Перенаправление HTTP).

     ж. Щелкните **Сохранить**.

### <a name="configure-everbridge-as-everbridge-member-portal-single-sign-on"></a>Настройка единого входа в Everbridge в качестве портала участника

Чтобы настроить единый вход в **Everbridge** в качестве **портала участника Everbridge**, отправьте скачанный **XML метаданных федерации** [группе поддержки Everbridge](mailto:support@everbridge.com). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD 

Чтобы создать на портале Azure тестового пользователя Britta Simon, сделайте следующее:

1. На портале Azure на панели слева выберите **Azure Active Directory** > **Пользователи** > **Все пользователи**.

    ![Ссылки "Пользователи" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В диалоговом окне **Пользователь** выполните следующие действия:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    a. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите `brittasimon@yourcompanydomain.extension`. Например, BrittaSimon@contoso.com.

    c. Установите флажок **Показать пароль**. Запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

Разрешите пользователю Britta Simon применять единый вход Azure, предоставив доступ к Everbridge.

1. На портале Azure выберите **Корпоративные приложения** > **Все приложения** >**Everbridge**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Everbridge**.

    ![Ссылка на Everbridge в списке "Приложения"](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Выберите **Добавить пользователя**. В диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Диалоговое окно "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка "Пользователи" выберите **Britta Simon**. В нижней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка. В нижней части экрана нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** выберите **Назначить**.

### <a name="create-an-everbridge-test-user"></a>Создание тестового пользователя в Everbridge

В этом разделе вы создадите тестового пользователя Britta Simon в Everbridge. Чтобы добавить пользователей на платформу Everbridge, обратитесь к [группе поддержки Everbridge](mailto:support@everbridge.com). Перед использованием единого входа необходимо создать и активировать пользователей в приложении Everbridge. 

### <a name="test-single-sign-on"></a>Проверка единого входа 

Проверьте конфигурацию единого входа Azure AD с помощью панели доступа.

Выбрав плитку Everbridge на Панели доступа, вы автоматически войдете в учетную запись Everbridge, для которой настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

