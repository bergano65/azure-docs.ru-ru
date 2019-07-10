---
title: Руководство по Интеграция Azure Active Directory с Adaptive Insights | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Adaptive Insights.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/17/2019
ms.author: jeedes
ms.openlocfilehash: c217663c5752907e0b3d6372d4522f6aba982b3d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67107407"
---
# <a name="tutorial-azure-active-directory-integration-with-adaptive-insights"></a>Руководство по Интеграция Azure Active Directory с Adaptive Insights

В этом руководстве описано, как интегрировать Adaptive Insights с Azure Active Directory (Azure AD).
Интеграция приложения Adaptive Insights с Azure AD обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к Adaptive Insights.
* Вы можете разрешить своим пользователям автоматический вход в приложение Adaptive Insights (единый вход) с помощью учетных записей Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Adaptive Insights, вам потребуется следующее:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* подписка Adaptive Insights с возможностью единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Adaptive Insights поддерживает единый вход, инициированный **IDP**

## <a name="adding-adaptive-insights-from-the-gallery"></a>Добавление Adaptive Insights из коллекции

Чтобы настроить интеграцию приложения Adaptive Insights с Azure AD, вам нужно добавить это приложение из коллекции в список управляемых приложений SaaS.

**Добавление приложения Adaptive Insights из коллекции**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Adaptive Insights**, выберите **Adaptive Insights** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![Adaptive Insights в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описаны настройка и проверка единого входа Azure AD в Adaptive Insights с помощью тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Adaptive Insights.

Чтобы настроить и проверить единый вход Azure AD в Adaptive Insights, выполните следующие действия.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в Adaptive Insights](#configure-adaptive-insights-single-sign-on)** необходима, чтобы конфигурировать параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя Adaptive Insights](#create-adaptive-insights-test-user)** нужно для того, чтобы в Adaptive Insights также существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Для настройки единого входа Azure AD с помощью Adaptive Insights выполните следующие действия.

1. Откройте [портал Azure](https://portal.azure.com/) и на странице интеграции с приложением **Adaptive Insights** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. На странице **Настройка единого входа с помощью SAML** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа Adaptive Insights](common/idp-intiated.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в формате `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`.

    б) В текстовом поле **URL-адрес ответа** введите URL-адрес в формате `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`.

    > [!NOTE]
    > Идентификатор сущности и URL-адрес ответа можно получить на странице **Параметры единого входа SAML** Adaptive Insights.

5. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Загрузить**, чтобы загрузить требуемый **сертификат (Base64)** из предложенных вариантов, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

6. Скопируйте нужные URL-адреса в разделе **Настройка Adaptive Insights**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    b. Идентификатор Azure AD

    c. URL-адрес выхода.

### <a name="configure-adaptive-insights-single-sign-on"></a>Настройка единого входа в Adaptive Insights

1. В другом окне веб-браузера войдите на корпоративный сайт Adaptive Insights на правах администратора.

2. Откройте страницу **Администратор**.

    ![Администратор](./media/adaptivesuite-tutorial/ic805644.png "Администратор")

3. В разделе **Users and Roles** (Пользователи и роли) щелкните **Manage SAML SSO Settings** (Управление параметрами единого входа SAML).

    ![Управление параметрами единого входа SAML](./media/adaptivesuite-tutorial/ic805645.png "Управление параметрами единого входа SAML")

4. На странице **SAML SSO Settings** (Параметры единого входа SAML) выполните следующие действия.

    ![Параметры единого входа SAML](./media/adaptivesuite-tutorial/ic805646.png "Параметры единого входа SAML")

    a. Введите имя конфигурации в текстовое поле **Identity provider name** (Имя поставщика удостоверений).

    b. Вставьте значение поля **Идентификатор Azure AD**, скопированное на портале Azure, в текстовое поле **Идентификатор сущности поставщика удостоверений**.

    c. Вставьте значение поля **URL-адрес входа**, скопированное на портале Azure, в текстовое поле **URL-адрес единого входа поставщика удостоверений**.

    d. Вставьте значение поля **URL-адрес выхода**, скопированное на портале Azure, в текстовое поле **Пользовательский URL-адрес для выхода**.

    д. Чтобы отправить загруженный сертификат, нажмите кнопку **Выбрать файл**.

    f. Выберите следующие параметры:

     * Для параметра **SAML user id** (Идентификатор пользователя SAML) выберите значение **User’s Adaptive Insights user name** (Имя пользователя Adaptive Insights).

     * Для параметра **SAML user id location** (Расположение идентификатора пользователя SAML) выберите значение **User id in NameID of Subject** (Идентификатор пользователя из NameID в Subject).

     * Для параметра **SAML NameID format** (Формат NameID SAML) выберите значение **Адрес электронной почты**.

     * Для параметра **Включить SAML** выберите значение **Allow SAML SSO and direct Adaptive Insights login** (Разрешить единый вход SAML и прямой вход Adaptive Insights).

    ж. Скопируйте значение **Adaptive Insights SSO URL** (URL-адрес единого входа Adaptive Insights) и вставьте его в текстовые поля **Идентификатор сущности** и **URL-адрес ответа** в разделе **Домены и URL-адреса приложения Adaptive Insights** на портале Azure.

    h. Выберите команду **Сохранить**.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    а. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите `brittasimon@yourcompanydomain.extension. Например, BrittaSimon@contoso.com.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе вы разрешите пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Adaptive Insights.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем **Adaptive Insights**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. Из списка приложений выберите **Adaptive Insights**.

    ![Ссылка на Adaptive Insights в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-adaptive-insights-test-user"></a>Создание тестового пользователя Adaptive Insights

Чтобы пользователи Azure AD могли выполнять вход в Adaptive Insights, их следует подготовить для работы в Adaptive Insights. В случае Adaptive Insights подготовка выполняется вручную.

**Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.**

1. Выполните вход на корпоративный сайт **Adaptive Insights** на правах администратора.

2. Откройте страницу **Администратор**.

   ![Администратор](./media/adaptivesuite-tutorial/IC805644.png "Администратор")

3. В разделе **Users and Roles** (Пользователи и роли) щелкните **Добавить пользователя**.

   ![Добавление пользователя](./media/adaptivesuite-tutorial/IC805648.png "Добавление пользователя")

4. В разделе **New User** (Новый пользователь) выполните следующие действия.

   ![Отправка](./media/adaptivesuite-tutorial/IC805649.png "Отправка")

   a. Введите в текстовые поля **Name** (Имя), **Login** (Имя для входа), **Email** (Адрес электронной почты) и **Password** (Пароль) соответствующие данные действующего пользователя Azure Active Directory, для которого выполняется подготовка.

   b. Выберите **Роль**.

   c. Нажмите кнопку **Submit**(Отправить).

> [!NOTE]
> Вы можете использовать любые другие средства создания учетной записи пользователя Adaptive Insights или API, предоставляемые Adaptive Insights для подготовки учетных записей пользователя AAD.

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Adaptive Insights на панели доступа, вы автоматически войдете в приложение Adaptive Insights, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что такое условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)