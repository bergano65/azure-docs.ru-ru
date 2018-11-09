---
title: Руководство по интеграции Azure Active Directory со Snowflake | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Snowflake.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3488ac27-0417-4ad9-b9a3-08325fe8ea0d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: jeedes
ms.openlocfilehash: 6e1f76548e2433f9bc4b0b26b0894a92b7ec0aa0
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50092565"
---
# <a name="tutorial-azure-active-directory-integration-with-snowflake"></a>Руководство по интеграции Azure Active Directory со Snowflake

В этом руководстве описано, как интегрировать Snowflake с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Snowflake обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Snowflake.
- Вы можете включить автоматический вход пользователей в Snowflake (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Snowflake, вам потребуется:

- подписка Azure AD;
- подписка Snowflake с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Snowflake из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-snowflake-from-the-gallery"></a>Добавление Snowflake из коллекции

Чтобы настроить интеграцию Snowflake с Azure AD, необходимо добавить Snowflake из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Snowflake из коллекции, сделайте следующее.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Snowflake**, выберите **Snowflake** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Snowflake в списке результатов](./media/snowflake-tutorial/tutorial_snowflake_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Snowflake с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Snowflake соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Snowflake.

Чтобы настроить и проверить единый вход Azure AD в Snowflake, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя приложения Snowflake](#creating-snowflake-test-user)** требуется для того, чтобы в Snowflake существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Snowflake.

**Чтобы настроить единый вход Azure AD в Snowflake, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Snowflake** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Выбрать метод единого входа** щелкните **Выбрать** для режима **SAML**, чтобы включить единый вход.

    ![Настройка единого входа](common/tutorial_general_301.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Настройка единого входа](common/editconfigure.png)

4. Если вы хотите настроить приложение в режиме, инициируемом **поставщиком удостоверений**, в разделе **Базовая конфигурация SAML** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа приложения Snowflake](./media/snowflake-tutorial/tutorial_snowflake_url1.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<SNOWFLAKE-URL>.snowflakecomputing.com`

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<SNOWFLAKE-URL>.snowflakecomputing.com/fed/login`.

5. Чтобы настроить приложение для работы в режиме, инициируемом **поставщиком услуг**, щелкните **Задать дополнительные URL-адреса** и выполните следующее действие:

    ![Сведения о домене и URL-адресах единого входа приложения Snowflake](./media/snowflake-tutorial/tutorial_snowflake_url2.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<SNOWFLAKE-URL>.snowflakecomputing.com`

    > [!NOTE]
    > Эти значения приведены в качестве примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа.

6. На странице **сертификата подписи SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/snowflake-tutorial/tutorial_snowflake_certificate.png) 

7. Требуемый URL-адрес можно скопировать из раздела **Set up Snowflake** (Настройка Snowflake).

    a. URL-адрес входа.

    b. Идентификатор Azure AD

    c. URL-адрес выхода

    ![Конфигурации Snowflake](common/configuresection.png)

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

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Создание пользователя Azure AD][100]

2. В верхней части экрана выберите **Новый пользователь**.

    ![Создание тестового пользователя Azure AD](common/create_aaduser_01.png) 

3. В разделе свойств пользователя сделайте следующее.

    ![Создание тестового пользователя Azure AD](common/create_aaduser_02.png)

    a. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon@yourcompanydomain.extension**.  
    Например, BrittaSimon@contoso.com

    c. Выберите **Свойства**, установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="creating-snowflake-test-user"></a>Создание тестового пользователя Snowflake

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

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Snowflake.

1. На портале Azure перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Назначение пользователя][201]

2. Из списка приложений выберите **Snowflake**.

    ![Настройка единого входа](./media/snowflake-tutorial/tutorial_snowflake_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "Snowflake" на панели доступа, вы автоматически войдете в приложение Snowflake.
Дополнительные сведения о панели доступа см. в статье с [общими сведениями о панели доступа](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
