---
title: Руководство по Интеграция Azure Active Directory с TalentLMS | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в TalentLMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c903d20d-18e3-42b0-b997-6349c5412dde
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0243a3e0ed83abc1edead5ecece4fd5c6ff1cad9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67089168"
---
# <a name="tutorial-azure-active-directory-integration-with-talentlms"></a>Руководство по Интеграция Azure Active Directory с TalentLMS

В этом руководстве описано, как интегрировать приложение TalentLMS с Azure Active Directory (Azure AD).
Интеграция Azure AD с приложением TalentLMS обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к TalentLMS.
* Вы можете включить автоматический вход пользователей в TalentLMS (единый вход) с помощью учетными записями Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с TalentLMS, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/));
* подписка TalentLMS с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* TalentLMS поддерживает единый вход, инициируемый **поставщиком услуг**.

## <a name="adding-talentlms-from-the-gallery"></a>Добавление TalentLMS из коллекции

Чтобы настроить интеграцию TalentLMS с Azure AD, необходимо добавить TalentLMS из коллекции в список управляемых приложений SaaS.

**Чтобы добавить TalentLMS из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **TalentLMS**, выберите **TalentLMS** в области результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![TalentLMS в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в TalentLMS с использованием тестового пользователя **Britta Simon**.
Чтобы единый вход функционировал, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в TalentLMS.

Чтобы настроить и проверить единый вход Azure AD в TalentLMS, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в TalentLMS](#configure-talentlms-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя TalentLMS](#create-talentlms-test-user)** требуется для того, чтобы в TalentLMS существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в TalentLMS, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **TalentLMS** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    ![Сведения о домене и URL-адресах единого входа для TalentLMS](common/sp-identifier.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<tenant-name>.TalentLMSapp.com`.

    b. В текстовом поле **Идентификатор (сущности)** введите URL-адрес в следующем формате: `http://<tenant-name>.talentlms.com`.

    > [!NOTE]
    > Эти значения приведены для примера. Необходимо обновить эти значения действующим URL-адресом для входа и идентификатором. Чтобы получить эти значения, обратитесь к [группе поддержки TalentLMS](https://www.talentlms.com/contact). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

5. В разделе **Сертификат подписи SAML** щелкните кнопку **Правка**, чтобы открыть диалоговое окно **Сертификат подписи SAML**.

    ![Изменение сертификата подписи SAML](common/edit-certificate.png)

6. В разделе **Сертификат подписи SAML** скопируйте значение **Отпечаток** и сохраните его на компьютере.

    ![Копирование значения "Отпечаток"](common/copy-thumbprint.png)

7. Требуемые URL-адреса можно скопировать из раздела **Настройка TalentLMS**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    b. Идентификатор Azure AD

    c. URL-адрес выхода.

### <a name="configure-talentlms-single-sign-on"></a>Настройка единого входа в TalentLMS

1. В другом окне веб-браузера войдите на свой корпоративный веб-сайт TalentLMS в качестве администратора.

1. В разделе **Учетная запись и параметры** выберите вкладку **Пользователи**.

    ![Учетная запись и параметры](./media/talentlms-tutorial/IC777296.png "Учетная запись и параметры")

1. Щелкните **Единый вход**.

1. В разделе "Единый вход" выполните следующие действия:

    ![Единый вход](./media/talentlms-tutorial/IC777297.png "Единый вход")

    a. Из списка **Тип интеграции единого входа** выберите **SAML 2.0**.

    b. В текстовое поле **Identity provider (IDP)** (Поставщик удостоверений) вставьте значение **Идентификатор Azure AD**, скопированное на портале Azure.

    c. Вставьте значение **Отпечаток** с портала Azure в текстовое поле **Certificate Fingerprint** (Отпечаток сертификата).

    d.  В текстовое поле **Remote sign-in URL** (URL-адрес удаленного входа) вставьте значение **URL-адрес входа**, скопированное на портале Azure.

    д. В текстовое поле **Remote sign-out URL** (URL-адрес удаленного выхода) вставьте значение **URL-адрес выхода**, скопированное на портале Azure.

    Е. Укажите следующие сведения.

    * В текстовое поле **TargetedID** (Целевой идентификатор) введите `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    * В текстовое поле **First Name** (Имя) введите `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    * В текстовое поле **Last Name** (Фамилия) введите `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.

    * В текстовое поле **Email** (Электронная почта) введите `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

1. Выберите команду **Сохранить**.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    а. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите `brittasimon@yourcompanydomain.extension`. Например BrittaSimon@contoso.com.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к TalentLMS.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем **TalentLMS**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. Из списка приложений выберите **TalentLMS**.

    ![Ссылка на TalentLMS в списке "Приложения"](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-talentlms-test-user"></a>Создание тестового пользователя TalentLMS

Чтобы пользователи Azure AD могли выполнить вход в TalentLMS, они должны быть подготовлены к работе в TalentLMS. В случае использования TalentLMS подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Войдите в клиент **TalentLMS**.

1. Щелкните **Пользователи**, а затем — **Добавить пользователя**.

1. На странице диалогового окна **Добавление пользователя** сделайте следующее:

    ![Добавление пользователя](./media/talentlms-tutorial/IC777299.png "Добавление пользователя")  

    a. В текстовое поле **First name** (Имя) введите имя пользователя, например **Britta**.

    b. В текстовое поле **Last name** (Фамилия) введите фамилию пользователя, например **Simon**.
 
    c. В текстовое поле **Email address** (Адрес электронной почты) введите адрес электронной почты пользователя, например `brittasimon\@contoso.com`.

    d. Нажмите кнопку **Add User**(Добавить пользователя).

> [!NOTE]
> Вы можете использовать любые другие средства создания учетной записи пользователя TalentLMS или API-интерфейсы, предоставляемые TalentLMS для подготовки учетных записей пользователя AAD.

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "TalentLMS" на Панели доступа, вы автоматически войдете в приложение TalentLMS, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что такое условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

