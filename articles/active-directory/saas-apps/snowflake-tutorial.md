---
title: Руководство. Интеграция Azure Active Directory со Snowflake | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Snowflake.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3488ac27-0417-4ad9-b9a3-08325fe8ea0d
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.openlocfilehash: 72d557ec2fa0e23dafb498d94c86296a3a534f88
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/07/2019
ms.locfileid: "54064021"
---
# <a name="tutorial-azure-active-directory-integration-with-snowflake"></a>Руководство. Интеграция Azure Active Directory со Snowflake

В этом руководстве описано, как интегрировать Snowflake с Azure Active Directory (Azure AD).
Интеграция Azure AD с приложением Snowflake обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к Snowflake.
* Вы можете включить автоматический вход пользователей в Snowflake (единый вход) с помощью учетных записей Azure AD.
* Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Snowflake, вам потребуется:

* подписка Azure AD; Если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).
* Подписка Snowflake с поддержкой единого входа

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства проводится настройка и проверка единого входа Azure AD в тестовой среде.

* Snowflake поддерживает единый вход инициированного **пакета обновления и выдающей точки распространения**.

## <a name="adding-snowflake-from-the-gallery"></a>Добавление Snowflake из коллекции

Чтобы настроить интеграцию Snowflake с Azure AD, необходимо добавить Snowflake из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Snowflake из коллекции, сделайте следующее.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"](common/add-new-app.png)

4. В поле поиска введите **Snowflake**, выберите **Snowflake** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![Snowflake в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Snowflake с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Snowflake.

Чтобы настроить и проверить единый вход Azure AD в Snowflake, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в Snowflake](#configure-snowflake-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя приложения Snowflake](#create-snowflake-test-user)** требуется для того, чтобы в Snowflake существовал пользователь Britta Simon, связанный с представлением пользователя в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в Snowflake, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Snowflake** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. Если вы хотите настроить приложение в режиме, инициируемом **поставщиком удостоверений**, в разделе **Базовая конфигурация SAML** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа приложения Snowflake](common/idp-intiated.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в формате `https://<SNOWFLAKE-URL>.snowflakecomputing.com`.

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<SNOWFLAKE-URL>.snowflakecomputing.com/fed/login`.

    c. Чтобы настроить приложение для работы в режиме, инициируемом пакетом обновления, щелкните **Задать дополнительные URL-адреса** и выполните следующее действие.

    ![Сведения о домене и URL-адресах единого входа приложения Snowflake](common/metadata-upload-additional-signon.png)

    В текстовом поле **URL-адрес входа** введите URL-адрес в следующем формате: `https://<SNOWFLAKE-URL>.snowflakecomputing.com`.

    > [!NOTE]
    > Эти значения приведены в качестве примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Чтобы получить их, обратитесь к [группе поддержки клиентов Snowflake](https://support.snowflake.net/s/snowflake-support). Можно также обратиться к шаблонам, указанным в разделе **Базовая конфигурация SAML** на портале Azure.

4. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Загрузить**, чтобы загрузить требуемый **сертификат (Base64)** из предложенных вариантов, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

6. Требуемые URL-адреса можно скопировать из раздела **Set up Snowflake** (Настройка Snowflake).

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    a. URL-адрес входа.

    b. Идентификатор Azure AD.

    c. URL-адрес выхода

### <a name="configure-snowflake-single-sign-on"></a>Настройка единого входа в Snowflake

8. В другом окне браузера войдите в приложение Snowflake с правами администратора безопасности.

9. Щелкните **профиль** в правой верхней части страницы, выберите **Switch Role** (Переключение ролей) и затем **ACCOUNTADMIN**.

    > [!NOTE]
    > Это не относится к контексту, выбранному в правом верхнем углу под именем пользователя
    
    ![Администратор Snowflake ](./media/snowflake-tutorial/tutorial_snowflake_accountadmin.png)

10. Откройте в блокноте **скачанный сертификат Base 64**. Скопируйте значение между "---BEGIN CERTIFICATE---" и "---END CERTIFICATE---" и вставьте его в кавычки рядом с полем **Сертификат** ниже. В текстовое поле **ssoUrl** вставьте значение **URL-адрес входа**, скопированное на портале Azure. Выберите **Все запросы** и нажмите **Запустить**.

    ![Snowflake SQL](./media/snowflake-tutorial/tutorial_snowflake_sql.png)

    ```
    use role accountadmin;
    alter account set saml_identity_provider = '{
    "certificate": "<Paste the content of downloaded certificate from Azure portal>",
    "ssoUrl":"<Login URL value which you have copied from the Azure portal>",
    "type":"custom",
    "label":"AzureAD"
    }';
    alter account set sso_login_page = TRUE;
    ```

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD 

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее.

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    a. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon@yourcompanydomain.extension**.  
    Например, BrittaSimon@contoso.com

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Snowflake.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **Snowflake**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений введите и выберите **Snowflake**.

    ![Ссылка на Snowflake в списке "Приложения"](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-snowflake-test-user"></a>Создание тестового пользователя Snowflake

Чтобы пользователи Azure AD могли выполнять вход в Snowflake, они должны быть подготовлены в Snowflake. В Snowflake подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Войдите в Snowflake с правами администратора безопасности.

2. Щелкните **профиль** в правой верхней части страницы, выберите **Switch Role** (Переключение ролей) и затем **ACCOUNTADMIN**.  

    ![Администратор Snowflake ](./media/snowflake-tutorial/tutorial_snowflake_accountadmin.png)

3. Для создания пользователя выполните в рабочей области приведенный ниже SQL-запрос, указав в качестве LOGIN_NAME имя пользователя Azure AD.

    ![Snowflake adminsql ](./media/snowflake-tutorial/tutorial_snowflake_usersql.png)

    ```
    use role accountadmin;
    CREATE USER britta_simon PASSWORD = '' LOGIN_NAME = 'BrittaSimon@contoso.com' DISPLAY_NAME = 'Britta Simon';
    ```

### <a name="test-single-sign-on"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку "Snowflake" на панели доступа, вы автоматически войдете в приложение Snowflake, для которого настроили единый вход. Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

