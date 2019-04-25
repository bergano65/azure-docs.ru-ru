---
title: Руководство по Интеграция Azure Active Directory с Absorb LMS | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в приложении Absorb LMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: ba9f1b3d-a4a0-4ff7-b0e7-428e0ed92142
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b46135366c76abf8da5387ff0698b4dc7634d79c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59698548"
---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>Руководство по Интеграция Azure Active Directory с Absorb LMS

В этом руководстве описано, как интегрировать приложение Absorb LMS с Azure Active Directory (Azure AD).
Интеграция Absorb LMS с Azure AD обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к Absorb LMS.
* Вы можете включить автоматический вход пользователей в Absorb LMS (единый вход) с помощью учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Absorb LMS, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/));
* подписка Absorb LMS с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Absorb LMS поддерживает единый вход, инициированный **поставщиком услуг**.

## <a name="adding-absorb-lms-from-the-gallery"></a>Добавление Absorb LMS из коллекции

Чтобы настроить интеграцию Absorb LMS с Azure AD, нужно добавить Absorb LMS из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Absorb LMS из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Absorb LMS**, выберите **Absorb LMS** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![Absorb LMS в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Absorb LMS с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Absorb LMS.

Чтобы настроить и проверить единый вход Azure AD в Absorb LMS, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в Absorb LMS](#configure-absorb-lms-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя Absorb LMS](#create-absorb-lms-test-user)** требуется, чтобы в Absorb LMS существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в Absorb LMS, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Absorb LMS** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. На странице **Настройка единого входа с помощью SAML** нажмите кнопку **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Сведения о домене и URL-адресах единого входа для приложения Absorb LMS](common/idp-intiated.png)

    Если вы используете **Absorb 5 — UI**, примените следующую конфигурацию.

    a. В текстовом поле **Идентификатор** введите URL-адрес в формате `https://company.myabsorb.com/account/saml`.

    б) В текстовом поле **URL-адрес ответа** введите URL-адрес в формате `https://company.myabsorb.com/account/saml`.

    Если вы используете **Absorb 5 — New Learner Experience**, примените следующую конфигурацию.

    a. В текстовом поле **Идентификатор** введите URL-адрес в формате `https://company.myabsorb.com/api/rest/v2/authentication/saml`.

    б) В текстовом поле **URL-адрес ответа** введите URL-адрес в формате `https://company.myabsorb.com/api/rest/v2/authentication/saml`.

    > [!NOTE]
    > Эти значения приведены для примера. Измените их на фактические значения идентификатора и URL-адреса ответа. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов Absorb LMS](https://support.absorblms.com/hc/). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

5. На следующем снимке экрана показан список атрибутов по умолчанию, когда **nameidentifier** сопоставляется с **user.userprincipalname**.

    ![image](common/edit-attribute.png)

6. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать нужный вам **XML метаданных федерации**, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

7. Скопируйте требуемый URL-адрес из раздела **Настройка Absorb LMS**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    b. Идентификатор Azure AD

    c. URL-адрес выхода.

### <a name="configure-absorb-lms-single-sign-on"></a>Настройка единого входа Absorb LMS

1. В новом окне веб-браузера войдите на свой корпоративный веб-сайт Absorb LMS в качестве администратора.

2. Нажмите кнопку **Account** (Учетная запись) в правом верхнем углу.

    ![Кнопка "Account" (Учетная запись)](./media/absorblms-tutorial/1.png)

3. В области "Account" (Учетная запись) щелкните **Portal Settings** (Параметры портала).

    ![Ссылка "Portal Settings" (Параметры портала)](./media/absorblms-tutorial/2.png)

4. Выберите вкладку **Manage SSO Settings** (Управление параметрами единого входа).

    ![Вкладка "Пользователи"](./media/absorblms-tutorial/managesso.png)

5. На странице **Manage Single Sign-On Settings** (Управление параметрами единого входа) выполните следующие действия.

    ![Страница "Single Sign-On Configuration" (Конфигурация единого входа)](./media/absorblms-tutorial/settings.png)

    a. В текстовом поле **Имя** введите имя, например Azure AD Marketplace SSO.

    b. Выберите **SAML**в качестве **метода**.

    c. В Блокноте откройте сертификат, скачанный с портала Azure. Удалите теги **---BEGIN CERTIFICATE---** и **---END CERTIFICATE---**. Затем в поле **Key** (Ключ) вставьте оставшееся содержимое.

    d. В поле **Mode** (Режим) выберите **Identity Provider Initiated** (Инициируемый поставщиком удостоверений).

    д. В поле **Id Property** (Свойство идентификатора) выберите атрибут, который вы настроили в качестве идентификатора пользователя в Azure AD. Например, если вы выбрали в Azure AD *nameidentifier*, то выберите атрибут **Username**.

    Е. Выберите **Sha256** в качестве **типа подписи**.

    ж. В поле **Login URL** (URL-адрес входа) вставьте значение **URL-адрес пользовательского доступа** со страницы **Свойства** приложения на портале Azure.

    h. В поле **Logout URL** (URL-адрес выхода) вставьте значение **URL-адрес выхода**, скопированное из окна **Настройка единого входа** на портале Azure.

    i. Установите переключатель **Automatically Redirect** (Автоматическое перенаправление) в значение **Вкл**.

6. Щелкните **Сохранить**.

    ![Включение параметра "Only Allow SSO Login" (Разрешить только единый вход)](./media/absorblms-tutorial/save.png)

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    а. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите `brittasimon\@yourcompanydomain.extension`.  
    Например BrittaSimon@contoso.com.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Absorb LMS.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем **Absorb LMS**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений введите и выберите **Absorb LMS**.

    ![Ссылка на Absorb LMS в списке "Приложения"](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-absorb-lms-test-user"></a>Создание тестового пользователя Absorb LMS

Чтобы пользователи Azure AD могли входить в Absorb LMS, их нужно настроить в Absorb LMS. В случае с Absorb LMS подготовка выполняется вручную.

**Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.**

1. Войдите на свой корпоративный веб-сайт Absorb LMS в качестве администратора.

2. В области **Users** (Пользователи) щелкните **Users** (Пользователи).

    ![Ссылка "Users" (Пользователи)](./media/absorblms-tutorial/absorblms_userssub.png)

3. Выберите вкладку **Пользователь**.

    ![Раскрывающийся список "Add New" (Добавить)](./media/absorblms-tutorial/absorblms_createuser.png)

4. На странице **Add User** (Добавление пользователя) сделайте следующее.

    ![Страница "Add User" (Добавление пользователя)](./media/absorblms-tutorial/user.png)

    a. В текстовом поле **First Name** (Имя) введите имя, например **Britta**.

    b. В текстовом поле **Last Name** (Фамилия) введите фамилию, например **Simon**.

    c. В текстовом поле **Username** (Имя пользователя) введите полное имя, например **Britta Simon**.

    d. В поле **Пароль** введите пароль пользователя.

    д. В поле **Confirm Password** (Подтверждение пароля) введите пароль еще раз.

    Е. Переведите переключатель **Is Active** (Активность) в положение **Active** (Активный).

5. Щелкните **Сохранить**.

    ![Включение параметра "Only Allow SSO Login" (Разрешить только единый вход)](./media/absorblms-tutorial/save.png)

    > [!NOTE]
    > По умолчанию для подготовки пользователей единый вход не включен. Если клиент хочет включить эту функцию, он должен ее настроить с помощью [этой](https://support.absorblms.com/hc/en-us/articles/360014083294-Incoming-SAML-2-0-SSO-Account-Provisioning) документации. Кроме того, обратите внимание, что подготовка пользователей доступна только в **Absorb 5 — New Learner Experience** с URL-адресом ACS: `https://company.myabsorb.com/api/rest/v2/authentication/saml`

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Absorb LMS на панели доступа, вы автоматически войдете в приложение Absorb LMS, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
