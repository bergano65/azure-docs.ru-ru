---
title: Руководство по Интеграция Azure Active Directory с Citrix ShareFile | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Citrix ShareFile.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: e14fc310-bac4-4f09-99ef-87e5c77288b6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9904ba14e8d5a518807eda371ba655963b33bd4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67091013"
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Руководство по Интеграция Azure Active Directory с Citrix ShareFile

В этом руководстве описано, как интегрировать Citrix ShareFile с Azure Active Directory (Azure AD).
Интеграция Azure AD с Citrix ShareFile обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к Citrix ShareFile.
* Вы можете включить автоматический вход пользователей в Citrix ShareFile (единый вход) под учетной записью Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Citrix ShareFile, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* Подписка Citrix ShareFile с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Citrix ShareFile поддерживает единый вход, инициированный **поставщиком услуг**.

## <a name="adding-citrix-sharefile-from-the-gallery"></a>Добавление Citrix ShareFile из коллекции

Чтобы настроить интеграцию Citrix ShareFile с Azure AD, вам нужно добавить Citrix ShareFile из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Citrix ShareFile из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Citrix ShareFile**, выберите **Citrix ShareFile** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![Citrix ShareFile в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Citrix ShareFile с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Citrix ShareFile.

Чтобы настроить и проверить единый вход Azure AD в Citrix ShareFile, вам нужно выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в Citrix ShareFile](#configure-citrix-sharefile-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя Citrix ShareFile](#create-citrix-sharefile-test-user)** требуется для того, чтобы в Citrix ShareFile существовал пользователь Britta Simon, связанный с представлением этого же пользователя в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в Citrix ShareFile, выполните следующие действия:

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Citrix ShareFile** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    ![Сведения о домене и URL-адресах единого входа приложения Citrix ShareFile](common/sp-identifier-reply.png)

    a. В текстовое поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<tenant-name>.sharefile.com/saml/login`.

    b. В текстовом поле **Identifier (Entity ID)** (Идентификатор (ИД сущности)) введите URL-адрес в следующем формате.

    | |
    |---|
    | `https://<tenant-name>.sharefile.com`|
    | `https://<tenant-name>.sharefile.com/saml/info`|
    | `https://<tenant-name>.sharefile1.com/saml/info`|
    | `https://<tenant-name>.sharefile1.eu/saml/info`|
    | `https://<tenant-name>.sharefile.eu/saml/info`|
    | |
    
    c. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате:
    
    | |
    |---|
    | `https://<tenant-name>.sharefile.com/saml/acs`|
    | `https://<tenant-name>.sharefile.eu/saml/<URL path>`|
    | `https://<tenant-name>.sharefile.com/saml/<URL path>`|

    > [!NOTE]
    > Эти значения приведены для примера. Укажите вместо них фактические значения URL-адреса для входа, идентификатора и URL-адреса ответа. Чтобы получить эти значения, обратитесь к [группе поддержки клиентов Citrix ShareFile](https://www.citrix.co.in/products/citrix-content-collaboration/support.html). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

4. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Загрузить**, чтобы загрузить требуемый **сертификат (Base64)** из предложенных вариантов, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

6. Скопируйте требуемый URL-адрес из раздела **Настройка Citrix ShareFile**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    б) Идентификатор Azure AD.

    в) URL-адрес выхода.

### <a name="configure-citrix-sharefile-single-sign-on"></a>Настройка единого входа в Citrix ShareFile

1. В другом окне веб-браузера войдите на свой корпоративный веб-сайт **Citrix ShareFile** в качестве администратора.

2. На панели инструментов в верхней части экрана нажмите **Администратор**.

3. В левой области навигации нажмите **Настройка единого входа**.
   
    ![Администрирование учетной записи](./media/sharefile-tutorial/ic773627.png "Администрирование учетной записи")

4. На странице диалогового окна **Single Sign-On/ SAML 2.0 Configuration** (Настройка единого входа или SAML 2.0) в разделе **Основные параметры** выполните следующие действия:
   
    ![Единый вход](./media/sharefile-tutorial/ic773628.png "Единый вход")
   
    a. Выберите команду **Enable SAML**(Включить SAML).
    
    b. В текстовое поле **Your IDP Issuer/ Entity ID** (Издатель поставщика удостоверений/идентификатор сущности) вставьте значение **Идентификатор Azure AD**, скопированное на портале Azure.

    c. Нажмите **Изменить** рядом с полем **X.509 Certificate** (Сертификат X.509) и передайте сертификат, скачанный с портала Azure AD.
    
    d. В текстовое поле **Login URL** (URL-адрес входа) вставьте значение **URL-адреса входа**, скопированное с портала Azure.
    
    д. В текстовое поле **Logout URL** (URL-адрес выхода) вставьте значение **URL-адреса выхода**, скопированное с портала Azure.

5. Нажмите **Сохранить** на портале управления Citrix ShareFile.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD 

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    а. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon\@домен_вашей_компании.доменная_зона**.  
    Например BrittaSimon@contoso.com.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как настроить для пользователя Britta Simon единый вход Azure, предоставив этому пользователю доступ к Citrix ShareFile.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем **Citrix ShareFile**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений введите и выберите **Citrix ShareFile**.

    ![Ссылка на ShareFile в списке "Приложения"](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-citrix-sharefile-test-user"></a>Создание тестового пользователя Citrix ShareFile

Чтобы пользователи Azure AD могли выполнить вход в Citrix ShareFile, они должны быть подготовлены для Citrix ShareFile. В случае с Citrix ShareFile подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Войдите в клиент **Citrix ShareFile** .

2. Последовательно выберите пункты **Manage Users \> Manage Users Home \> + Create Employee** (Управление пользователями > Управление домашним ресурсом пользователей > + Создать сотрудника).
   
    ![Создание сотрудника](./media/sharefile-tutorial/IC781050.png "Создание сотрудника")

3. В разделе **Basic Information** (Базовые сведения) выполните следующие действия.
   
    ![Основные сведения](./media/sharefile-tutorial/IC799951.png "Основные сведения")
   
    a. В текстовое поле **Email Address** (Электронная почта) введите адрес электронной почты пользователя Britta Simon: **brittasimon\@contoso.com**.
   
    b. В текстовое поле **First Name** (Имя) введите **имя пользователя**, например **Britta**.
   
    c. В текстовое поле **Last Name** (Фамилия) введите **фамилию**, например **Simon**.

4. Нажмите кнопку **Add User**(Добавить пользователя).
  
    >[!NOTE]
    >Владелец учетной записи Azure AD получит по электронной почте сообщение со ссылкой для активации учетной записи. Вы можете использовать любые другие инструменты создания учетной записи пользователя Citrix ShareFile или API, предоставляемые Citrix ShareFile для подготовки учетных записей пользователей AAD.

### <a name="test-single-sign-on"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Citrix ShareFile на панели доступа, вы автоматически войдете в приложение Citrix ShareFile, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что такое условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

