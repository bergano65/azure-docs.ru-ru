---
title: Руководство. Интеграция Azure Active Directory с Mimecast Personal Portal | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Mimecast Personal Portal.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 345b22be-d87e-45a4-b4c0-70a67eaf9bfd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed7d8d1a2c2f8c515fa520f6701068e4655cad35
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "65871900"
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-personal-portal"></a>Руководство. Интеграция Azure Active Directory с Mimecast Personal Portal

В этом руководстве описано, как интегрировать Mimecast Personal Portal с Azure Active Directory (Azure AD).
Интеграция Azure AD с Mimecast Personal Portal обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к Mimecast Personal Portal.
* Вы можете включить автоматический вход пользователей в Mimecast Personal Portal (единый вход) с использованием учетных записей Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Mimecast Personal Portal, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* Подписка на Mimecast Personal Portal с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Mimecast Personal Portal поддерживает единый вход инициированного **пакета обновления**.

## <a name="adding-mimecast-personal-portal-from-the-gallery"></a>Добавление Mimecast Personal Portal из коллекции

Чтобы настроить интеграцию Mimecast Personal Portal с Azure AD, необходимо добавить Mimecast Personal Portal из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Mimecast Personal Portal из коллекции, сделайте следующее.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Mimecast Personal Portal**, выберите **Mimecast Personal Portal** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![Mimecast Personal Portal в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Mimecast Personal Portal с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и одноименным пользователем в Mimecast Personal Portal.

Чтобы настроить и проверить единый вход Azure AD в Mimecast Personal Portal, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в Mimecast Personal Portal](#configure-mimecast-personal-portal-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя Mimecast Personal Portal](#create-mimecast-personal-portal-test-user)** требуется для того, чтобы в Mimecast Personal Portal существовал пользователь Britta Simon, связанный с представлением этого же пользователя в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в Mimecast Personal Portal, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Mimecast Personal Portal** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    ![Сведения о домене и URL-адресах единого входа для приложения Mimecast Personal Portal](common/sp-identifier-reply.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес. 

    | Регион  |  Значение | 
    | --------------- | --------------- | 
    | Европа          | `https://eu-api.mimecast.com/login/saml`|
    | США   | `https://us-api.mimecast.com/login/saml`|
    | ЮАР    | `https://za-api.mimecast.com/login/saml`|
    | Австралия       | `https://au-api.mimecast.com/login/saml`|
    | Внешнее расположение        | `https://jer-api.mimecast.com/login/saml`|

    b. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате:

    | Регион  |  Значение | 
    | --------------- | --------------- |
    | Европа          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | США   | `https://us-api.mimecast.com/sso/<accountcode>`|    
    | ЮАР    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | Австралия       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | Внешнее расположение        | `https://jer-api.mimecast.com/sso/<accountcode>`|

    c. В текстовом поле **URL-адрес ответа** введите URL-адрес: 

    | Регион  |  Значение | 
    | --------------- | --------------- | 
    | Европа          | `https://eu-api.mimecast.com/login/saml`|
    | США   | `https://us-api.mimecast.com/login/saml`|
    | ЮАР    | `https://za-api.mimecast.com/login/saml`|
    | Австралия       | `https://au-api.mimecast.com/login/saml`|
    | Внешнее расположение        | `https://jer-api.mimecast.com/login/saml`|

    > [!NOTE]
    > Значение идентификатора приведено для примера и не является реальным. Вместо него нужно указать фактический идентификатор. Чтобы получить это значение, обратитесь к [группе поддержки клиентов Mimecast Personal Portal](https://www.mimecast.com/customer-success/technical-support/). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

4. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Загрузить**, чтобы загрузить требуемый **сертификат (Base64)** из предложенных вариантов, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

6. Требуемый URL-адрес можно скопировать из раздела **Set up Mimecast Personal Portal** (Настройка Mimecast Personal Portal).

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    б) Идентификатор Azure AD.

    в) URL-адрес выхода.

### <a name="configure-mimecast-personal-portal-single-sign-on"></a>Настройка единого входа Mimecast Personal Portal

1. В другом окне веб-браузера войдите в личный портал Mimecast в качестве администратора.

2. Выберите **Services \> Application** ("Службы" > "Приложение").
   
    ![Приложения](./media/mimecast-personal-portal-tutorial/ic794998.png "Приложения")

3. Щелкните **Профили проверки подлинности**.
   
    ![Authentication Profiles](./media/mimecast-personal-portal-tutorial/ic794999.png "Authentication Profiles") (Профили аутентификации)

4. Щелкните **Новый профиль проверки подлинности**.
   
    ![Создание профиля аутентификации](./media/mimecast-personal-portal-tutorial/ic795000.png "New Authentication Profile")

5. В разделе **Профиль проверки подлинности** сделайте следующее:
   
    ![Authentication Profile](./media/mimecast-personal-portal-tutorial/ic795001.png "Authentication Profile") (Профиль аутентификации)
   
    a. В текстовом поле **Описание** введите имя конфигурации.
   
    b. Выберите **Обязательное использование проверки подлинности SAML для личного портала Mimecast**.
   
    c. В поле **Provider** (Поставщик) выберите **Azure Active Directory**.
   
    d. В текстовое поле **URL-адрес издателя** вставьте **идентификатор Azure AD**, скопированный c портала Azure.
   
    д. В текстовое поле **URL-адрес входа** вставьте значение **URL-адреса входа**, скопированное с портала Azure.
   
    Е. В текстовое поле **URL-адрес выхода** вставьте значение **URL-адреса выхода**, скопированное с портала Azure.

    ж. Откройте в Блокноте сертификат в кодировке **Base64**, скачанный с портала Azure, скопируйте его в буфер обмена и вставьте в текстовое поле **Identity Provider Certificate (Metadata)** (Сертификат поставщика удостоверений (метаданные)).

    h. Установите флажок **Разрешить единый вход**.
   
    i. Выберите команду **Сохранить**.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD 

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    а. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon\@домен_вашей_компании.доменная_зона**.  
    Например BrittaSimon@contoso.com.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Mimecast Personal Portal.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **Mimecast Personal Portal**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. Из списка приложений введите и выберите **Mimecast Personal Portal**.

    ![Ссылка на Mimecast Personal Portal в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-mimecast-personal-portal-test-user"></a>Создание тестового пользователя Mimecast Personal Portal

Чтобы разрешить пользователям Azure AD вход в личный портал Mimecast, они должны быть подготовлены для личного портала Mimecast. В случае с личным порталом Mimecast подготовка выполняется вручную.

Перед созданием пользователей необходимо зарегистрировать домен.

**Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.**

1. Войдите на **Mimecast Personal Portal** в качестве администратора.

2. Выберите **Directories \> Internal** (Каталоги > Внутренние).
   
    ![Directories](./media/mimecast-personal-portal-tutorial/ic795003.png "Directories") (Каталоги)

3. Щелкните **Зарегистрировать новый домен**.
   
    ![Register New Domain](./media/mimecast-personal-portal-tutorial/ic795004.png "Register New Domain") (Зарегистрировать новый домен)

4. После создания нового домена щелкните **Новый адрес**.
   
    ![New Address](./media/mimecast-personal-portal-tutorial/ic795005.png "New Address") (Новый адрес)

5. В диалоговом окне "New Address" (Новый адрес) введите соответствующие данные действующей учетной записи Azure AD, которую необходимо подготовить.
   
    ![Сохранить](./media/mimecast-personal-portal-tutorial/ic795006.png "Сохранить")
   
    a. В текстовое поле **Email Address** (Адрес электронной почты) введите **адрес электронной почты** пользователя, например **BrittaSimon\@contoso.com**.
    
    b. В текстовое поле **Global Name** (Глобальное имя) введите **имя пользователя** **BrittaSimon**.

    c. В текстовые поля **Password** (Пароль) и **Confirm Password** (Подтверждение пароля) введите **пароль** пользователя.
   
    b. Выберите команду **Сохранить**.

>[!NOTE]
>Вы можете использовать любые другие инструменты создания учетных записей пользователя Mimecast Personal Portal или API, предоставляемые Mimecast Personal Portal для подготовки учетных записей пользователя Azure Active Directory.

### <a name="test-single-sign-on"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку "Mimecast Personal Portal" на панели доступа, вы автоматически войдете в приложение Mimecast Personal Portal, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

