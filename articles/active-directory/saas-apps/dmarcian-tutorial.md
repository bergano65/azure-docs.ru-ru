---
title: Руководство по Интеграция Azure Active Directory с dmarcian | Документация Майкрософт
description: Сведения о том, как настроить единый вход между Azure Active Directory и dmarcian.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a04b9383-3a60-4d54-9412-123daaddff3b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/30/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 232742c9ca3f090e2900566c1e80f9720e284135
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67104056"
---
# <a name="tutorial-azure-active-directory-integration-with-dmarcian"></a>Руководство по Интеграция Azure Active Directory с dmarcian

В этом руководстве описано, как интегрировать dmarcian с Azure Active Directory (Azure AD).
Интеграция dmarcian с Azure AD предоставляет следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к dmarcian.
* Вы можете включить автоматический вход пользователей в dmarcian (единый вход) с помощью учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с dmarcian, потребуется следующее:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* подписка dmarcian с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* dmarcian поддерживает единый вход, инициированный **поставщиком услуг** и **поставщиком удостоверений**.

## <a name="adding-dmarcian-from-the-gallery"></a>Добавление dmarcian из коллекции

Чтобы настроить интеграцию dmarcian с Azure AD, необходимо добавить dmarcian из коллекции в список управляемых приложений SaaS.

**Чтобы добавить dmarcian из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **dmarcian**, выберите **dmarcian** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![dmarcian в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в dmarcian с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в dmarcian.

Чтобы настроить и проверить единый вход Azure AD в dmarcian, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в dmarcian](#configure-dmarcian-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя dmarcian](#create-dmarcian-test-user)** требуется для того, чтобы в dmarcian существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в dmarcian, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **dmarcian** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. Если вы хотите настроить приложение в режиме, инициируемом **поставщиком удостоверений**, в разделе **Базовая конфигурация SAML** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа приложения dmarcian](common/idp-intiated.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в таком формате:

    | |
    | -- |
    | `https://us.dmarcian.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-eu.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-ap.com/sso/saml/<ACCOUNT_ID>/sp.xml` |

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в таком формате:

    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-ap.com/login/<ACCOUNT_ID>/handle/` |

5. Чтобы настроить приложение для работы в режиме, инициируемом **поставщиком услуг**, щелкните **Задать дополнительные URL-адреса** и выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа приложения dmarcian](common/metadata-upload-additional-signon.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате:
    
    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>` |
    | `https://dmarciam-ap.com/login/<ACCOUNT_ID>` |
     
    > [!NOTE] 
    > Эти значения приведены для примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа, которые описываются далее в этом руководстве. 

6. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** нажмите кнопку "Копировать", чтобы копировать **URL-адрес метаданных федерации приложений** и сохранить его на компьютере.

    ![Ссылка для скачивания сертификата](common/copy-metadataurl.png)

### <a name="configure-dmarcian-single-sign-on"></a>Настройка единого входа в dmarcian

1. В другом окне веб-браузера войдите в dmarcian в качестве администратора безопасности.

2. Щелкните **Profile** (Профиль) в верхнем правом углу, а затем выберите **Preferences** (Предпочтения).

    ![Предпочтения](./media/dmarcian-tutorial/tutorial_dmarcian_pref.png)

3. Прокрутите вниз до раздела **Single Sign-On** (Единый вход) и нажмите кнопку **Configure** (Настроить).

    ![Единый вход](./media/dmarcian-tutorial/tutorial_dmarcian_sso.png)

4. На странице **SAML Single Sign-On** (Единый вход SAML) установите в поле **Status** (Состояние) значение **Enabled** (Включено) и выполните следующие действия:

    ![Аутентификация](./media/dmarcian-tutorial/tutorial_dmarcian_auth.png)

    * В разделе **Add dmarcian to your Identity Provider** (Добавление dmarcian к поставщику удостоверений) щелкните **COPY** (Копировать), чтобы скопировать **URL-адрес службы обработчика утверждений** для вашего экземпляра и вставить его в текстовое поле **URL-адреса ответа** в разделе **Базовая конфигурация SAML** на портале Azure.

    * В разделе **Add dmarcian to your Identity Provider** (Добавление dmarcian к поставщику удостоверений) щелкните **COPY** (Копировать), чтобы скопировать **идентификатор сущности** для вашего экземпляра и вставить его в текстовое поле **идентификатора** в разделе **Базовая конфигурация SAML** на портале Azure.

    * В разделе **Set up Authentication** (Настройка проверки подлинности) в текстовое поле **Identity Provider Metadata** (Метаданные поставщика удостоверений) вставьте **URL-адрес метаданных федерации приложений**, который вы скопировали на портале Azure.

    * В разделе **Set up Authentication** (Настройка проверки подлинности) в текстовое поле **Attribute Statements** (Операторы атрибута) вставьте URL-адрес `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    * В разделе **Set up Login URL** (Настройка URL-адреса входа) скопируйте **URL-адрес входа** для вашего экземпляра и вставьте его в текстовое поле **URL-адреса входа** в разделе **Базовая конфигурация SAML** на портале Azure.

        > [!Note]
        > Вы можете изменить **URL-адрес входа** в соответствии со своей организацией.

    * Выберите команду **Сохранить**.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD 

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    a. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon\@домен_вашей_компании.доменная_зона**.  
    Например BrittaSimon@contoso.com.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к dmarcian.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **dmarcian**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **dmarcian**.

    ![Ссылка на dmarcian в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** выберите **Britta Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-dmarcian-test-user"></a>Создание тестового пользователя в dmarcian

Чтобы пользователи Azure AD могли выполнять вход в dmarcian, они должны быть подготовлены для dmarcian. В dmarcian подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Войдите в dmarcian с правами администратора безопасности.

2. Щелкните **Profile** (Профиль) в верхнем правом углу, а затем выберите **Manage Users** (Управление пользователями).

    ![Пользователь](./media/dmarcian-tutorial/tutorial_dmarcian_user.png)

3. В правой части раздела **SSO Users** (Пользователи единого входа) щелкните **Add New User** (Добавить нового пользователя).

    ![Добавление пользователя](./media/dmarcian-tutorial/tutorial_dmarcian_addnewuser.png)

4. Во всплывающем меню **Add New User** (Добавление нового пользователя) сделайте следующее:

    ![Новый пользователь](./media/dmarcian-tutorial/tutorial_dmarcian_save.png)

    a. В текстовом поле **New User Email** (Электронная почта нового пользователя) введите адрес электронной почты пользователя, например **brittasimon\@contoso.com**.

    b. Если нужно предоставить пользователю права администратора, выберите **Make User an Admin** (Сделать пользователя администратором).

    c. Нажмите кнопку **Add User**(Добавить пользователя).

### <a name="test-single-sign-on"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку dmarcian на Панели доступа, вы автоматически войдете в приложение dmarcian, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что такое условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

