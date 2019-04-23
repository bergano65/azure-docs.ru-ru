---
title: Руководство по Интеграция Azure Active Directory с ThousandEyes | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в ThousandEyes.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 790e3f1e-1591-4dd6-87df-590b7bf8b4ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 587d6f8ecdc447672c14ce400ebc64609e4d472d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59273912"
---
# <a name="tutorial-azure-active-directory-integration-with-thousandeyes"></a>Руководство по Интеграция Azure Active Directory с ThousandEyes

В этом руководстве описано, как интегрировать приложение ThousandEyes с Azure Active Directory (Azure AD).
Интеграция Azure AD с приложением ThousandEyes обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к ThousandEyes.
* Вы можете включить автоматический вход для пользователей (единый вход) в ThousandEyes с помощью учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с ThousandEyes, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* подписка ThousandEyes с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* ThousandEyes поддерживает единый вход, инициированный **поставщиком услуг**.

* ThousandEyes поддерживает [**автоматическую** подготовку пользователей](https://docs.microsoft.com/azure/active-directory/saas-apps/thousandeyes-provisioning-tutorial).

## <a name="adding-thousandeyes-from-the-gallery"></a>Добавление ThousandEyes из коллекции

Чтобы настроить интеграцию ThousandEyes с Azure AD, необходимо добавить ThousandEyes из коллекции в список управляемых приложений SaaS.

**Чтобы добавить ThousandEyes из коллекции, сделайте следующее.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **ThousandEyes**, выберите **ThousandEyes** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![ThousandEyes в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в ThousandEyes с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в ThousandEyes.

Чтобы настроить и проверить единый вход Azure AD в ThousandEyes, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в ThousandEyes](#configure-thousandeyes-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя ThousandEyes](#create-thousandeyes-test-user)** требуется для того, чтобы в ThousandEyes существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в ThousandEyes, сделайте следующее.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **ThousandEyes** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    ![Сведения о домене и URL-адресах единого входа для приложения ThousandEyes](common/sp-signonurl.png)

    В текстовом поле **URL-адрес входа** введите URL-адрес: `https://app.thousandeyes.com/login/sso`.

5. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Загрузить**, чтобы загрузить требуемый **сертификат (Base64)** из предложенных вариантов, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

6. Скопируйте требуемый URL-адрес из раздела **Настройка ThousandEyes**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    b. Идентификатор Azure AD

    c. URL-адрес выхода.

### <a name="configure-thousandeyes-single-sign-on"></a>Настройка единого входа в ThousandEyes

1. В другом окне веб-браузера войдите на веб-сайт **ThousandEyes** своей компании в качестве администратора.

2. В верхнем меню нажмите пункт **Параметры**.

    ![Параметры](./media/thousandeyes-tutorial/ic790066.png "Параметры")

3. В нижней части страницы нажмите кнопку **Учетная запись**

    ![Учетная запись](./media/thousandeyes-tutorial/ic790067.png "Учетная запись")

4. Откройте вкладку **Security & Authentication** (Безопасность и проверка подлинности).

    ![Безопасность и проверка подлинности](./media/thousandeyes-tutorial/ic790068.png "Безопасность и проверка подлинности")

5. В разделе **Настройка единого входа** сделайте следующее:

    ![Настройка единого входа](./media/thousandeyes-tutorial/ic790069.png "Настройка единого входа")

    a. Выберите пункт **Включить единый вход**.

    b. В текстовое поле **Login Page URL** (URL-адрес страницы входа) вставьте **URL-адрес входа**, скопированный на портале Azure.

    c. В текстовое поле **Logout Page URL** (URL-адрес страницы выхода) вставьте значение **URL-адрес выхода**, скопированное на портале Azure.

    d. В текстовое поле **Identity Provider Issuer** (Издатель поставщика удостоверений) вставьте значение **идентификатора Azure AD**, скопированное на портале Azure.

    д. В разделе **Verification Certificate** (Сертификат проверки) нажмите кнопку **Choose file** (Выбрать файл) и передайте сертификат, скачанный с портала Azure.

    Е. Выберите команду **Сохранить**.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD 

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    а. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите brittasimon@yourcompanydomain.extension. Например BrittaSimon@contoso.com.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к ThousandEyes.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **ThousandEyes**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. Из списка приложений выберите **ThousandEyes**.

    ![Ссылка на ThousandEyes в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-thousandeyes-test-user"></a>Создание тестового пользователя в ThousandEyes

В рамках этого раздела создается пользователь с именем Britta Simon в ThousandEyes. ThousandEyes поддерживает автоматическую подготовку пользователей, которая по умолчанию включена. Дополнительные сведения о настройке автоматической подготовки пользователей можно найти [здесь](thousandeyes-provisioning-tutorial.md).

**Если необходимо создать пользователя вручную, выполните следующие действия:**

1. Войдите на свой корпоративный веб-сайт ThousandEyes в качестве администратора.

2. Щелкните **Параметры**.

    ![Параметры](./media/thousandeyes-tutorial/IC790066.png "Параметры")

3. Выберите раздел **Учетная запись**.

    ![Учетная запись](./media/thousandeyes-tutorial/IC790067.png "Учетная запись")

4. Щелкните вкладку **Accounts & Users** (Учетные записи и пользователи).

    ![Учетные записи и пользователи](./media/thousandeyes-tutorial/IC790073.png "Учетные записи и пользователи")

5. В разделе **Add Users & Accounts** (Добавление пользователей и учетных записей) сделайте следующее.

    ![Добавление учетных записей пользователей](./media/thousandeyes-tutorial/IC790074.png "Добавление учетных записей пользователей")

    a. В текстовое поле **Name** (Имя) введите имя пользователя, например **Britta Simon**.

    b. В текстовое поле **Email** (Электронная почта) введите адрес электронной почты пользователя, например brittasimon@contoso.com.

    b. Щелкните **Добавить нового пользователя к учетной записи**.

    > [!NOTE]
    > Владелец учетной записи Azure Active Directory получит электронное сообщение со ссылкой для подтверждения и активации учетной записи.

> [!NOTE]
> Вы можете использовать любые другие инструменты создания учетных записей пользователя ThousandEyes или API, предоставляемые ThousandEyes для подготовки учетных записей пользователя Azure Active Directory.

### <a name="test-single-sign-on"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку ThousandEyes на Панели доступа, вы автоматически войдете в приложение ThousandEyes, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Руководство по настройке Google Apps для автоматической подготовки пользователей](https://docs.microsoft.com/azure/active-directory/saas-apps/thousandeyes-provisioning-tutorial)
