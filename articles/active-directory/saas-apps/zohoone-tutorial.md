---
title: Руководство по Интеграция Azure Active Directory с Zoho One | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Zoho One.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bbc3038c-0d8b-45dd-9645-368bd3d01a0f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: 0a37789e7c7efeb71770ff0e8061d57e6603b6c4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67086235"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho-one"></a>Руководство по Интеграция Azure Active Directory с Zoho One

B этом руководстве описано, как интегрировать Zoho One с Azure Active Directory (Azure AD).
Интеграция Microsoft Azure Active Directory с Zoho One обеспечивает следующие преимущества:

* С помощью Microsoft Azure Active Directory вы можете контролировать доступ к Zoho One.
* Вы можете включить автоматический вход для пользователей в Zoho One (единый вход) с помощью учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Microsoft Azure Active Directory с Zoho One, вам потребуется следующее:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/));
* подписка Zoho One с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Zoho One поддерживает единый вход, инициированный **поставщиком услуг** и **поставщиком удостоверений**.

## <a name="adding-zoho-one-from-the-gallery"></a>Добавление Zoho One из коллекции

Чтобы настроить интеграцию Zoho One с Microsoft Azure Active Directory, необходимо добавить Zoho One из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Zoho One из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Zoho One**, выберите **Zoho One** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![Zoho One в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в приложение Zoho One с использованием тестового пользователя **Britta Simon**.
Чтобы единый вход функционировал, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Zoho One.

Чтобы настроить и проверить единый вход Microsoft Azure Active Directory в Zoho One, выполните действия из следующих разделов:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в Zoho One](#configure-zoho-one-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя приложения Zoho One](#create-zoho-one-test-user)** требуется для того, чтобы в Zoho One существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в Zoho One, сделайте следующее:

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Zoho One** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. Если вы хотите настроить приложение в режиме, инициируемом **поставщиком удостоверений**, в разделе **Базовая конфигурация SAML** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа приложения Zoho One](common/idp-relay.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес: `one.zoho.com`

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в формате `https://accounts.zoho.com/samlresponse/<saml-identifier>`.

    > [!NOTE]
    > Приведенное выше значение **URL-адрес ответа** используется только для примера. Вы получите значение `<saml-identifier>` из шага 4 раздела **Настройка единого входа в Zoho One**, как описано далее в учебнике.

    c. Щелкните **Задать дополнительные URL-адреса**.

    d. В текстовом поле **Состояние ретранслятора** введите такой URL-адрес: `https://one.zoho.com`

5. Если вы хотите настроить приложение в **режиме, инициированном поставщиком услуг**, выполните следующие действия.


    ![Сведения о домене и URL-адресах единого входа приложения Zoho One](common/both-signonurl.png)

    В текстовом поле **URL-адрес входа** введите URL-адрес в формате `https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com`. 

    > [!NOTE] 
    > Приведенное выше значение **URL-адрес входа** используется только для примера. Вместо него нужно указать фактическое значение URL-адреса входа из раздела **Настройка единого входа в Zoho One**, как описано далее в учебнике. 

6. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Загрузить**, чтобы загрузить требуемый **сертификат (Base64)** из предложенных вариантов, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

7. Требуемый URL-адрес вы можете скопировать из раздела **Настройка Zoho One**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    b. Идентификатор Azure AD

    c. URL-адрес выхода.

### <a name="configure-zoho-one-single-sign-on"></a>Настройка единого входа в Zoho One

1. В другом окне браузера войдите на свой корпоративный сайт Zoho One в качестве администратора.

2. На вкладке **Organization** (Организация) в разделе **SAML Authentication** (Аутентификация SAML) щелкните **Setup** (Настройка).

    ![Вкладка "Организация" на сайте Zoho One](./media/zohoone-tutorial/tutorial_zohoone_setup.png)

3. В открывшемся окне сделайте следующее:

    ![Адреса для входа-выхода, Zoho One](./media/zohoone-tutorial/tutorial_zohoone_save.png)

    a. В текстовое поле **URL-адрес входа** вставьте **URL-адрес входа**, скопированный на портале Azure.

    b. В текстовое поле **URL-адрес выхода** вставьте **URL-адрес выхода**, скопированный на портале Azure.

    c. Нажмите кнопку **Browse** (Обзор), чтобы отправить **сертификат (Base64)** , скачанный с портала Microsoft Azure.

    d. Выберите команду **Сохранить**.

4. После сохранения параметров проверки подлинности SAML скопируйте значение параметра **SAML-Identifier** (Идентификатор SAML) и добавьте к нему **URL-адрес ответа** вместо `<saml-identifier>`, например `https://accounts.zoho.com/samlresponse/one.zoho.com`, и вставьте созданное значение в текстовое поле **URL-адрес ответа** в разделе **Базовая конфигурация SAML**.

    ![Идентификатор SAML, Zoho One](./media/zohoone-tutorial/tutorial_zohoone_samlidenti.png)

5. Перейдите на вкладку **Domains** (Домены) и нажмите кнопку **Add Domain** (Добавить домен).

    ![Домены, Zoho One](./media/zohoone-tutorial/tutorial_zohoone_domain.png)

6. На странице **Add Domain** (Добавление домена) сделайте следующее:

    ![Добавление домена, Zoho One](./media/zohoone-tutorial/tutorial_zohoone_adddomain.png)

    a. В текстовом поле **Имя домена** введите домен, например contoso.com.

    b. Щелкните **Добавить**.

    >[!Note]
    >Добавив домен, выполните [эти](https://www.zoho.com/one/help/admin-guide/domain-verification.html) действия, чтобы подтвердить его. Когда домен будет подтвержден, укажите его имя в поле **URL-адрес входа** на портале Azure в разделе **Базовая конфигурация SAML**.

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

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Zoho One.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем **Zoho One**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Zoho One**.

    ![Ссылка на Zoho One в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-zoho-one-test-user"></a>Создание тестового пользователя Zoho One

Чтобы пользователи Azure AD могли входить в Zoho One, они должны быть подготовлены в приложении Zoho One. В Zoho One подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Войдите в Zoho One с правами администратора безопасности.

2. На вкладке **Users** (Пользователи) щелкните **значок пользователя**.

    ![Пользователь, Zoho One](./media/zohoone-tutorial/tutorial_zohoone_users.png)

3. На странице **Добавление пользователя** сделайте следующее:

    ![Добавление пользователя, Zoho One](./media/zohoone-tutorial/tutorial_zohoone_adduser.png)
    
    a. В текстовое поле **Name** (Имя) введите имя пользователя, например **Britta Simon**.
    
    b. В текстовое поле **Email Address** (Адрес электронной почты) введите адрес электронной почты пользователя, например brittasimon@contoso.com.

    >[!Note]
    >В списке доменов выберите свой подтвержденный домен.

    c. Щелкните **Добавить**.

### <a name="test-single-sign-on"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Zoho One на Панели доступа, вы автоматически войдете в приложение Zoho One, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что такое условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

