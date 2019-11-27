---
title: Руководство по Интеграция Azure Active Directory с Picturepark | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в приложении Picturepark.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 31c21cd4-9c00-4cad-9538-a13996dc872f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: 08c5bd8da0dda74156b2d44c8106ed345ef749dd
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73177018"
---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>Руководство по Интеграция Azure Active Directory с Picturepark

В этом руководстве описано, как интегрировать Picturepark с Azure Active Directory (Azure AD).
Интеграция Azure AD с приложением Picturepark обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к Picturepark.
* Вы можете включить автоматический вход пользователей в Picturepark (единый вход) с помощью учетных записей Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Picturepark, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/));
* подписка Picturepark с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Picturepark поддерживает единый вход, инициируемый **поставщиком услуг**.

## <a name="adding-picturepark-from-the-gallery"></a>Добавление Picturepark из коллекции

Чтобы настроить интеграцию Picturepark с Azure AD, необходимо добавить Picturepark из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Picturepark из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Picturepark**, выберите **Picturepark** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![Picturepark в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Picturepark с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Picturepark.

Чтобы настроить и проверить единый вход Azure AD в Picturepark, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в Picturepark](#configure-picturepark-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя Picturepark](#create-picturepark-test-user)** требуется для того, чтобы в Picturepark существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в Picturepark, выполните следующее.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Picturepark** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    ![Сведения о домене и URL-адресах единого входа для Picturepark](common/sp-identifier.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<companyname>.picturepark.com`.

    b. В текстовом поле **Идентификатор (сущности)** введите URL-адрес в следующем формате:

    |  |
    |--|
    | `https://<companyname>.current-picturepark.com`|
    | `https://<companyname>.picturepark.com`|
    | `https://<companyname>.next-picturepark.com`|
    | |

    > [!NOTE]
    > Эти значения приведены для примера. Необходимо обновить эти значения действующим URL-адресом для входа и идентификатором. Чтобы получить эти значения, обратитесь к [группе поддержки клиентов Picturepark](https://picturepark.com/company/picturepark-customer-support). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

5. В разделе **Сертификат подписи SAML** щелкните кнопку **Правка**, чтобы открыть диалоговое окно **Сертификат подписи SAML**.

    ![Изменить сертификат подписи SAML](common/edit-certificate.png)

6. В разделе **Сертификат подписи SAML** скопируйте значение **Отпечаток** и сохраните его на компьютере.

    ![Копирование значения "Отпечаток"](common/copy-thumbprint.png)

7. Требуемые URL-адреса можно скопировать из раздела **Настройка Picturepark**. В поле **URL-адрес входа** введите значение в следующем формате: `https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ — это идентификатор клиента подписки Azure AD.

    ![Копирование URL-адресов настройки](./media/picturepark-tutorial/configurls.png)

    a. Идентификатор Azure AD

    b. URL-адрес выхода.

### <a name="configure-picturepark-single-sign-on"></a>Настройка единого входа в Picturepark

1. В другом окне веб-браузера войдите на свой корпоративный сайт Picturepark в качестве администратора.

2. Щелкните **Administrative tools** (Администрирование) на панели инструментов в верхней части страницы и выберите **Management Console** (Консоль управления).
   
    ![Консоль управления](./media/picturepark-tutorial/ic795062.png "Консоль управления")

3. Щелкните **Authentication** (Аутентификация), а затем выберите **Identity providers** (Поставщики удостоверений).
   
    ![Аутентификация](./media/picturepark-tutorial/ic795063.png "Аутентификация")

4. В разделе **Конфигурация поставщика удостоверений** сделайте следующее:
   
    ![Конфигурация поставщика удостоверений](./media/picturepark-tutorial/ic795064.png "Конфигурация поставщика удостоверений")
   
    a. Щелкните **Добавить**.
  
    b. Введите имя конфигурации.
   
    c. Выберите **По умолчанию**.
   
    d. В текстовое поле **Issuer URI** (URI издателя) вставьте значение **URL-адрес входа**, скопированное на портале Azure.
   
    д. В текстовое поле **Trusted Issuer Thumb Print** (Отпечаток доверенного издателя) вставьте значение **Отпечаток**, скопированное в разделе **Сертификат подписи SAML**. 

5. Щелкните **JoinDefaultUsersGroup**.

6. Чтобы задать атрибут **Emailaddress** в текстовом поле **Claim** (Утверждение), введите `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` и нажмите кнопку **Save** (Сохранить).

      ![Конфигурация](./media/picturepark-tutorial/ic795065.png "Конфигурация")

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

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Picturepark.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **Picturepark**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. Из списка приложений выберите **Picturepark**.

    ![Ссылка на Picturepark в списке "Приложения"](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-picturepark-test-user"></a>Создание тестового пользователя Picturepark

Чтобы пользователи Azure AD могли выполнять вход в Picturepark, они должны быть подготовлены в Picturepark. В случае с Picturepark подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Выполните вход в клиент **Picturepark**.

1. Щелкните **Administrative tools** (Администрирование) на панели инструментов в верхней части страницы и выберите **Users** (Пользователи).
   
    ![Пользователи](./media/picturepark-tutorial/ic795067.png "Пользователи")

1. На вкладке **Users overview** (Обзор пользователей) щелкните **New** (Создать).
   
    ![Управление пользователями](./media/picturepark-tutorial/ic795068.png "Управление пользователями")

1. В диалоговом окне **Create User** (Создание пользователя) введите приведенные ниже данные действительной учетной записи Azure AD, которую необходимо подготовить.
   
    ![Создание пользователя](./media/picturepark-tutorial/ic795069.png "Создать пользователя")
   
    a. В текстовое поле **Email Address** (Адрес электронной почты) введите **адрес электронной почты** пользователя, `BrittaSimon@contoso.com`.  
   
    b. В текстовые поля **Password** (Пароль) и **Confirm Password** (Подтверждение пароля) введите **пароль** пользователя BrittaSimon. 
   
    c. В текстовое поле **First Name** (Имя) введите **имя пользователя**, **Britta**. 
   
    d. В текстовое поле **Last Name** (Фамилия) введите **фамилию** пользователя, **Simon**.
   
    д. В текстовое поле **Company** (Компания) введите **название компании** пользователя. 
   
    Е. В текстовом поле **Country** (Страна) выберите **страну или регион** пользователя.
  
    ж. В текстовое поле **ZIP** (Почтовый индекс) введите **почтовый индекс** города.
   
    h. В текстовое поле **City** (Город) введите **название города** пользователя.

    i. В поле **Язык**укажите язык.
   
    j. Нажмите кнопку **Создать**.

>[!NOTE]
>Вы можете использовать любые другие инструменты создания учетных записей пользователя Picturepark или API, предоставляемые Picturepark для подготовки учетных записей пользователя Azure Active Directory.
>

### <a name="test-single-sign-on"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "Picturepark" на Панели доступа, вы автоматически войдете в приложение Picturepark, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

