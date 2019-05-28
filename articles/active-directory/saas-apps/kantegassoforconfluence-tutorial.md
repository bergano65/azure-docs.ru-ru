---
title: Руководство по Интеграция Azure Active Directory с Kantega SSO for Confluence | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Kantega SSO for Confluence.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d0d99c14-a6ca-45f2-bb84-633126095e7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: bea7106bd0c3317fe71de201f219c151471e8208
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64704896"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-confluence"></a>Руководство по Интеграция Azure Active Directory с Kantega SSO for Confluence

В этом руководстве описано, как интегрировать Kantega SSO for Confluence с Azure Active Directory (Azure AD).
Интеграция Azure AD с приложением Kantega SSO for Confluence обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к Kantega SSO for Confluence.
* Вы можете включить автоматический вход пользователей в Kantega SSO for Confluence (единый вход) с использованием учетных записей Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Kantega SSO for Confluence, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/));
* подписка Kantega SSO for Confluence с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Kantega SSO for Confluence поддерживает вход, инициированный **пакетом обновления и IDP**.

## <a name="adding-kantega-sso-for-confluence-from-the-gallery"></a>Добавление Kantega SSO for Confluence из коллекции

Чтобы настроить интеграцию Kantega SSO for Confluence с Azure AD, необходимо добавить Kantega SSO for Confluence из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Kantega SSO for Confluence из коллекции, сделайте следующее.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Kantega SSO for Confluence**, выберите **Kantega SSO for Confluence** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Kantega SSO for Confluence в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описаны настройка и проверка единого входа Azure AD в Kantega SSO for Confluence с использованием тестового пользователя **Britta Simon**.
Чтобы обеспечить работу единого входа, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Kantega SSO for Confluence.

Чтобы настроить и проверить единый вход Azure AD в Kantega SSO for Confluence, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в Kantega SSO for Confluence](#configure-kantega-sso-for-confluence-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя Kantega SSO for Confluence](#create-kantega-sso-for-confluence-test-user)** требуется для того, чтобы в Kantega SSO for Confluence существовал пользователь Britta Simon, связанный с представлением этого же пользователя в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в Kantega SSO for Confluence, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Kantega SSO for Confluence** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. Если вы хотите настроить приложение в режиме, инициируемом **поставщиком удостоверений**, в разделе **Базовая конфигурация SAML** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения Kantega SSO for Confluence](common/idp-intiated.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в формате `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`.

    б) В текстовом поле **URL-адрес ответа** введите URL-адрес в формате `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`.

5. Чтобы настроить приложение для работы в режиме, инициируемом **поставщиком услуг**, щелкните **Задать дополнительные URL-адреса** и выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения Kantega SSO for Confluence](common/metadata-upload-additional-signon.png)

    В текстовом поле **URL-адрес входа** введите URL-адрес в формате `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`.

    > [!NOTE]
    > Эти значения приведены для примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Эти значения предоставляются во время настройки подключаемого модуля Confluence, которая описывается далее в этом руководстве.

6. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать нужный вам **XML метаданных федерации**, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

7. Скопируйте требуемые URL-адреса в разделе **Настройка Kantega SSO for Confluence**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    b. Идентификатор Azure AD

    c. URL-адрес выхода.

### <a name="configure-kantega-sso-for-confluence-single-sign-on"></a>Настройка единого входа в Kantega SSO for Confluence

1. В другом окне веб-браузера войдите на **портал администрирования Confluence** с правами администратора.

1. Наведите указатель мыши на шестеренку и щелкните **Add-ons** (Надстройки).

    ![Настройка единого входа](./media/kantegassoforconfluence-tutorial/addon1.png)

1. В разделе **ATLASSIAN MARKETPLACE** щелкните **Find new add-ons** (Найти новые надстройки).

    ![Настройка единого входа](./media/kantegassoforconfluence-tutorial/addon.png)

1. Найдите подключаемый модуль **Kantega SSO for Confluence (SAML & Kerberos)** и нажмите кнопку **Install** (Установить), чтобы установить новый подключаемый модуль SAML.

    ![Настройка единого входа](./media/kantegassoforconfluence-tutorial/addon2.png)

1. Начнется установка подключаемого модуля.

    ![Настройка единого входа](./media/kantegassoforconfluence-tutorial/addon3.png)

1. Установка завершится. Нажмите кнопку **Закрыть**

    ![Настройка единого входа](./media/kantegassoforconfluence-tutorial/addon33.png)

1. Нажмите кнопку **Управление**.

    ![Настройка единого входа](./media/kantegassoforconfluence-tutorial/addon34.png)

1. Щелкните **Configure** (Настройка), чтобы настроить новый подключаемый модуль.

    ![Настройка единого входа](./media/kantegassoforconfluence-tutorial/addon35.png)

1. Этот новый подключаемый модуль можно также найти на вкладке **USERS & SECURITY** (Пользователи и безопасность).

    ![Настройка единого входа](./media/kantegassoforconfluence-tutorial/addon36.png)

1. В разделе **SAML** сделайте следующее. Выберите **Azure Active Directory (Azure AD)** из раскрывающегося списка **Добавление поставщика удостоверений**.

    ![Настройка единого входа](./media/kantegassoforconfluence-tutorial/addon4.png)

1. Выберите уровень подписки **Базовый**.

    ![Настройка единого входа](./media/kantegassoforconfluence-tutorial/addon5.png)

1. В разделе **Свойства приложения** выполните следующие действия.

    ![Настройка единого входа](./media/kantegassoforconfluence-tutorial/addon6.png)

    a. Скопируйте значение **App ID URI** (URI кода приложения) и используйте его как **идентификатор, URL-адрес ответа и URL-адрес входа** в разделе **Базовая конфигурация SAML** на портале Azure.

    b. Щелкните **Далее**.

1. В разделе **Metadata import** (Импорт метаданных) выполните следующие действия. 

    ![Настройка единого входа](./media/kantegassoforconfluence-tutorial/addon7.png)

    a. Щелкните **Metadata file on my computer** (Файл метаданных на моем компьютере) и передайте файл метаданных, который вы скачали с портала Azure.

    b. Щелкните **Далее**.

1. В разделе **Name and SSO location** (Имя и расположение единого входа) выполните следующее.

    ![Настройка единого входа](./media/kantegassoforconfluence-tutorial/addon8.png)

    a. В текстовом поле **Provider Name** (Имя поставщика) введите имя поставщика (например, Azure AD).

    b. Щелкните **Далее**.

1. Проверьте сертификат для подписи и нажмите кнопку **Next** (Далее).

    ![Настройка единого входа](./media/kantegassoforconfluence-tutorial/addon9.png)

1. В разделе **Confluence user accounts** (Учетные записи пользователей Confluence) выполните следующие действия.

    ![Настройка единого входа](./media/kantegassoforconfluence-tutorial/addon10.png)

    a. Щелкните переключатель **Create users in Confluence's internal Directory if needed** (При необходимости создать пользователей во внутреннем каталоге Confluence) и введите соответствующее имя группы пользователей (это может быть несколько групп, разделенных запятой).

    b. Щелкните **Далее**.

1. Нажмите кнопку **Готово**

    ![Настройка единого входа](./media/kantegassoforconfluence-tutorial/addon11.png)

1. В разделе **Known domains for Azure AD** (Известные домены для Azure AD) выполните следующие действия. 

    ![Настройка единого входа](./media/kantegassoforconfluence-tutorial/addon12.png)

    a. Щелкните **Known domains** (Известные домены) на левой панели страницы.

    b. Введите имя домена в текстовое поле **Known domains** (Известные домены).

    c. Выберите команду **Сохранить**.

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

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Kantega SSO for Confluence.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем **Kantega SSO for Confluence**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. Из списка приложений выберите **Kantega SSO for Confluence**.

    ![Ссылка на Kantega SSO for Confluence в списке "Приложения"](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-kantega-sso-for-confluence-test-user"></a>Создание тестового пользователя Kantega SSO for Confluence

Чтобы пользователи Azure AD могли выполнить вход в Confluence, для них нужно выполнить подготовку к работе в Confluence. В случае с Kantega SSO for Confluence подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Войдите на корпоративный сайт Kantega SSO for Confluence с правами администратора.

1. Наведите указатель мыши на шестеренку и щелкните **User management** (Управление пользователями).

    ![Добавление сотрудника](./media/kantegassoforconfluence-tutorial/user1.png)

1. В разделе "Users" (Пользователи) выберите вкладку **Add Users** (Добавление пользователей). На диалоговой странице **Add a User** (Добавление пользователя) выполните следующее.

    ![Добавление сотрудника](./media/kantegassoforconfluence-tutorial/user2.png)

    a. В текстовом поле **Username** (Имя пользователя) введите электронный адрес пользователя, например Brittasimon@contoso.com.

    b. В текстовом поле **Full Name** (Полное имя) введите полное имя пользователя, например Britta Simon.

    c. В текстовом поле **Email** (Электронная почта) введите адрес электронной почты пользователя, например Brittasimon@contoso.com.

    d. В текстовом поле **Password** (Пароль) введите пароль пользователя.

    д. Щелкните **Confirm Password** (Подтвердить пароль) и повторно введите пароль.

    Е. Нажмите кнопку **Добавить**.

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Kantega SSO for Confluence на Панели доступа, вы автоматически войдете в приложение Kantega SSO for Confluence, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

