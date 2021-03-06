---
title: Руководство по Интеграция Azure Active Directory с Proxyclick | Документация Майкрософт
description: Из этого руководства вы узнаете, как настроить единый вход между Azure Active Directory и Proxyclick.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5c58a859-71c2-4542-ae92-e5f16a8e7f18
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 6a4d1c8a390ebd1194d14c057bb32d3111bf39be
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67093495"
---
# <a name="tutorial-azure-active-directory-integration-with-proxyclick"></a>Руководство по Интеграция Azure Active Directory с Proxyclick

В этом руководстве описано, как интегрировать Proxyclick с Azure Active Directory (Azure AD).
Такая интеграция обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к Proxyclick.
* Вы можете включить автоматический вход пользователей (единый вход) в Proxyclick с использованием учетных записей Azure AD.
* Вы можете управлять учетными записями централизованно — через портал Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Proxyclick, вам потребуется:

* подписка Azure AD Если у вас нет среды Azure AD, вы можете получить [пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).
* подписка Proxyclick с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure Active Directory в тестовой среде.

* Proxyclick поддерживает единый вход, инициируемый поставщиком службы и поставщиком удостоверений.

## <a name="add-proxyclick-from-the-gallery"></a>Добавление Proxyclick из коллекции

Чтобы настроить интеграцию Proxyclick с Azure AD, необходимо добавить Proxyclick из коллекции в список управляемых приложений SaaS.

1. На [портале Azure](https://portal.azure.com) в области слева щелкните **Azure Active Directory**.

    ![Выберите Azure Active Directory.](common/select-azuread.png)

2. Выберите **Корпоративные приложения** > **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, выберите **Новое приложение** в верхней части окна.

    ![Выбор элемента "Новое приложение"](common/add-new-app.png)

4. В поле поиска введите **Proxyclick**. В результатах поиска выберите **Proxyclick**, а затем щелкните **Добавить**.

     ![Результаты поиска](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Proxyclick с использованием тестового пользователя Britta Simon.
Для единого входа необходимо установить связь между пользователем в Azure AD и соответствующим пользователем в Proxyclick.

Чтобы настроить и проверить единый вход Azure AD в Proxyclick, вам потребуется выполнить следующие действия.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в Proxyclick](#configure-proxyclick-single-sign-on)** на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить ему использовать единый вход Azure AD.
5. **[Создание тестового пользователя Proxyclick](#create-a-proxyclick-test-user)** , связанного с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться, что конфигурация работает правильно.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в Proxyclick, выполните следующие действия:

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением Proxyclick выберите **Единый вход**.

    ![Выбор параметра "Единый вход"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Выбор метода единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Значок "Изменить"](common/edit-urls.png)

4. Если вы хотите настроить приложение в режиме, инициируемом поставщиком удостоверений, в диалоговом окне **Базовая конфигурация SAML** выполните следующие действия.

    ![Диалоговое окно "Базовая конфигурация SAML"](common/idp-intiated.png)

    1. В поле **Идентификатор** введите URL-адрес в следующем формате:
   
       `https://saml.proxyclick.com/init/<companyId>`

    1. В поле **URL-адрес ответа** введите URL-адрес в следующем формате:

       `https://saml.proxyclick.com/consume/<companyId>`

5. Если вы хотите настроить приложение для работы в режиме, инициируемом поставщиком услуг, выберите **Задать дополнительные URL-адреса**. В поле **URL-адрес для входа** введите URL-адрес в следующем формате:
   
   `https://saml.proxyclick.com/init/<companyId>`

    ![Сведения о домене и URL-адресах единого входа для приложения Proxyclick](common/metadata-upload-additional-signon.png)

    

    > [!NOTE]
    > Эти значения представляют собой заполнители. Вместо них нужно указать фактический идентификатор, URL-адрес ответа и URL-адрес для входа. Шаги для получения этих значений описаны далее в этом руководстве.

6. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните ссылку **Скачать** рядом с нужным **сертификатом (Base64)** и сохраните сертификат на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

7. Требуемые URL-адреса можно скопировать из раздела **Настройка Proxyclick**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    1. **URL-адрес входа**.

    1. **Идентификатор Azure AD**

    1. **URL-адрес выхода**.

### <a name="configure-proxyclick-single-sign-on"></a>Настройка единого входа в Proxyclick

1. В новом окне веб-браузера войдите на свой корпоративный сайт Proxyclick в качестве администратора.

2. Выберите **Account & Settings** (Учетная запись и параметры).

    ![Account & Settings (Учетная запись и параметры)](./media/proxyclick-tutorial/configure1.png)

3. Прокрутите экран вниз до раздела **Integrations** (Интеграции) и выберите **SAML**.

    ![Выбор SAML](./media/proxyclick-tutorial/configure2.png)

4. В разделе **SAML** выполните следующие действия.

    ![Раздел SAML](./media/proxyclick-tutorial/configure3.png)

    1. Скопируйте значение в поле **SAML Consumer URL** (URL-адрес получателя SAML) и вставьте его в текстовое поле **URL-адрес ответа** в диалоговом окне **Базовая конфигурация SAML** на портале Azure.

    1. Скопируйте значение в поле **SAML SSO Redirect URL** (URL-адрес перенаправления для единого входа SAML) и вставьте его в текстовые поля **URL-адрес для входа** и **Идентификатор** в диалоговом окне **Базовая конфигурация SAML** на портале Azure.

    1. В списке **SAML Request Method** (Метод запроса SAML) выберите значение **HTTP Redirect** (Перенаправление HTTP).

    1. В поле **SSO URL** (URL-адрес единого входа) вставьте значение **идентификатора Azure AD**, скопированное на портале Azure.

    1. В поле **SAML 2.0 Endpoint URL** (URL-адрес конечной точки SAML 2.0) вставьте **URL-адрес входа**, скопированный на портале Azure.

    1. В Блокноте откройте файл сертификата, скачанный с портала Azure. Вставьте содержимое этого файла в поле **Certificate** (Сертификат).

    1. Щелкните **Save changes** (Сохранить изменения).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

В этом разделе описано, как создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Выбор "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Выбор "Новый пользователь"](common/new-user.png)

3. В диалоговом окне **Пользователь** сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    1. В поле **Имя** введите **BrittaSimon**.
  
    1. В поле **Имя пользователя** введите **BrittaSimon @\<ваш_домен>.\<доменная_зона>** . (Например, BrittaSimon@contoso.com).

    1. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    1. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Proxyclick.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **Proxyclick**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Proxyclick**.

    ![Список приложений](common/all-applications.png)

3. В области слева выберите **Пользователи и группы**.

    ![Выбор параметра "Пользователи и группы"](common/users-groups-blade.png)

4. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** — **Пользователи и группы**.

    ![Выбор элемента "Добавить пользователя"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** выберите **Britta Simon** из списка пользователей, а затем в нижней части окна нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка. В нижней части окна нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** выберите **Назначить**.

### <a name="create-a-proxyclick-test-user"></a>Создание тестового пользователя Proxyclick

Чтобы разрешить пользователям Azure AD входить в Proxyclick, их нужно добавить в Proxyclick. Необходимо добавить их вручную.

Чтобы создать учетную запись пользователя, выполните следующие действия.

1. Войдите на корпоративный сайт Proxyclick в качестве администратора.

1. В верхней части окна выберите **Colleagues** (Коллеги).

    ![Выбор элемента Colleagues (Коллеги)](./media/proxyclick-tutorial/user1.png)

1. Выберите **Add Colleague** (Добавить коллегу).

    ![Выбор элемента Add Colleague (Добавить коллегу)](./media/proxyclick-tutorial/user2.png)

1. В разделе **Add a colleague** (Добавление коллеги) сделайте следующее:

    ![Раздел Add a colleague (Добавление коллеги)](./media/proxyclick-tutorial/user3.png)

    1. В текстовом поле **Email** (Электронная почта) введите адрес электронной почты пользователя. В нашем случае это будет **brittasimon\@contoso.com**.

    1. В текстовом поле **First Name** (Имя) введите имя пользователя. В нашем случае это будет **Britta**.

    1. В текстовом поле **Last Name** (Фамилия) введите фамилию пользователя. В нашем случае это будет **Simon**.

    1. Щелкните **Add User** (Добавить пользователя).

### <a name="test-single-sign-on"></a>Проверка единого входа

Теперь необходимо проверить конфигурацию единого входа Azure AD с помощью Панели доступа.

Щелкнув плитку Proxyclick на Панели доступа, вы автоматически войдете в экземпляр Proxyclick, для которого настроили единый вход. Дополнительные сведения о Панели доступа см. в статье [Access and use apps on the My Apps portal](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Доступ к приложениям и их использование на портале "Мои приложения").

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что такое условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

