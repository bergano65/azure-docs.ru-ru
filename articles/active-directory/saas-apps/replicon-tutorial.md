---
title: Руководство по Интеграция Azure Active Directory с Replicon | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Replicon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 02a62f15-917c-417c-8d80-fe685e3fd601
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: be006cd49041a33c13b5cc5c459a1d54c46c3b47
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64727600"
---
# <a name="tutorial-azure-active-directory-integration-with-replicon"></a>Руководство по Интеграция Azure Active Directory с Replicon

В этом руководстве описано, как интегрировать Replicon с Azure Active Directory (Azure AD).
Интеграция Azure AD с приложением Replicon обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к Replicon.
* Вы можете включить автоматический вход пользователей в Replicon (единый вход) с помощью учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Replicon, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/));
* подписка Replicon с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Replicon поддерживает единый вход, инициированный **пакетом обновления**.

## <a name="adding-replicon-from-the-gallery"></a>Добавление Replicon из коллекции

Чтобы настроить интеграцию Replicon с Azure AD, необходимо добавить Replicon из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Replicon из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Replicon**, выберите **Replicon** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Replicon в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Replicon с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Replicon.

Чтобы настроить и проверить единый вход Azure AD в Replicon, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в Replicon](#configure-replicon-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя приложения Replicon](#create-replicon-test-user)** требуется для того, чтобы в Replicon существовал пользователь Britta Simon, связанный с одноименным пользователем в Azur AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в Replicon, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Replicon** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    ![Сведения о домене и URL-адресах единого входа приложения Replicon](common/sp-identifier-reply.png)

    a. В текстовое поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://na2.replicon.com/<companyname>/saml2/sp-sso/post`.

    b. В поле **Идентификатор** введите URL-адрес в следующем формате: `https://global.replicon.com/<companyname>`.

    c. В текстовом поле **URL-адрес ответа** введите URL-адрес в формате `https://global.replicon.com/!/saml2/<companyname>/sso/post`.

    > [!NOTE]
    > Эти значения приведены для примера. Укажите вместо них фактические значения URL-адреса для входа, идентификатора и URL-адреса ответа. Чтобы получить их, обратитесь в [группу поддержки клиентов Replicon](https://www.replicon.com/customerzone/contact-support). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

5. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать нужный вам **XML метаданных федерации**, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

6. Скопируйте требуемые URL-адреса в разделе **Настройка Replicon**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    b. Идентификатор Azure AD

    c. URL-адрес выхода.

### <a name="configure-replicon-single-sign-on"></a>Настройка единого входа Replicon

1. В другом окне веб-браузера войдите на корпоративный сайт Replicon с правами администратора.

2. Для настройки SAML 2.0 выполните следующие действия:

    ![Включение аутентификации SAML](./media/replicon-tutorial/ic777805.png "Включение аутентификации SAML")

    a. Чтобы отобразить диалоговое окно **EnableSAML Authentication2**, добавьте следующую строку в URL-адрес после ключа компании: `/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

    * Ниже показана схема полного URL-адреса: `https://na2.replicon.com/\<YourCompanyKey\>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

   b. Щелкните **+** , чтобы развернуть раздел **v20Configuration**.

   c. Щелкните **+** , чтобы развернуть раздел **metaDataConfiguration**.

   d. Щелкните **Выбрать файл**, чтобы выбрать XML-файл метаданных поставщика удостоверений, и нажмите кнопку **Отправить**.

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

В этом разделе описано, как предоставить пользователю Britta Simon доступ к Replicon, чтобы он мог использовать единый вход Azure.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем **Replicon**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Replicon**.

    ![Ссылка на Replicon в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-replicon-test-user"></a>Создание тестового пользователя Replicon

Цель этого раздела — создать пользователя с именем Britta Simon в Replicon.

**Если необходимо создать пользователя вручную, выполните следующие действия:**

1. В окне веб-браузера войдите на корпоративный сайт Replicon в качестве администратора.

2. Выберите **Administration \> Users** ("Администрирование" > "Пользователи").

    ![Пользователи](./media/replicon-tutorial/ic777806.png "Пользователи")

3. Нажмите кнопку **+ Добавить пользователя**.

    ![Добавление пользователя](./media/replicon-tutorial/ic777807.png "Добавление пользователя")

4. В разделе **Профиль пользователя** сделайте следующее:

    ![Профиль пользователя](./media/replicon-tutorial/ic777808.png "Профиль пользователя")

    a. В текстовом поле **Имя для входа в систему** введите электронный адрес пользователя Azure AD, которого хотите подготовить, например `BrittaSimon\@contoso.com`.

    b. Для параметра **Authentication Type** (Тип аутентификации) выберите значение **SSO** (Единый вход).

    c. В текстовом поле **Отдел** введите отдел пользователя.

    d. Для параметра **Employee Type** (Тип сотрудника) выберите значение **Administrator** (Администратор).

    д. Нажмите кнопку **Сохранить профиль пользователя**.

> [!NOTE]
> Вы можете использовать любые другие инструменты создания учетных записей пользователя Replicon или API, предоставляемые Replicon для подготовки учетных записей пользователя Azure AD.

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Replicon на Панели доступа, вы автоматически войдете в приложение Replicon, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)