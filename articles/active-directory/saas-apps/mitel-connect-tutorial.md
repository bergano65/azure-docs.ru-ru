---
title: Руководство по Интеграция Azure Active Directory с Mitel Connect | Документация Майкрософт
description: Из этой статьи вы узнаете, как настроить единый вход с помощью Azure Active Directory в Mitel Connect.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 204f540b-09f1-452b-a52f-78143710ef76
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/03/2019
ms.author: jeedes
ms.openlocfilehash: aa3548a71e403728cbec4b8df2b0dce1cf6abde7
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164478"
---
# <a name="tutorial-azure-active-directory-integration-with-mitel-micloud-connect"></a>Руководство по Интеграция Azure Active Directory с Mitel MiCloud Connect

В этом руководстве описано, как интегрировать Mitel MiCloud Connect с Azure Active Directory (Azure AD). Интеграция Azure AD с MiCloud Connect обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ пользователей к приложениям MiCloud Connect, используя корпоративные учетные данные этих пользователей.
* Из своей учетной записи вы можете включить автоматический вход пользователей в MiCloud Connect (единый вход) с использованием учетных записей Azure AD.


Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с MiCloud Connect, вам потребуется:

* подписка Azure AD;

  (если у вас нет среды Azure AD, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/));
* учетная запись Mitel MiCloud Connect.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD.

* Mitel Connect поддерживает единый вход, инициированный **поставщиком служб**.

## <a name="adding-mitel-connect-from-the-gallery"></a>Добавление Mitel Connect из коллекции

Чтобы настроить интеграцию Mitel Connect с Azure AD, вам необходимо добавить Mitel Connect из коллекции в список управляемых приложений SaaS на портале Azure.

**Чтобы добавить Mitel Connect из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Щелкните **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Щелкните **Новое приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Mitel Connect**, выберите **Mitel Connect** на панели результатов и щелкните **Добавить**.

     ![Mitel Connect в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в MiCloud Connect с использованием тестового пользователя **Britta Simon**. Чтобы обеспечить работу единого входа, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в MiCloud Connect.

Чтобы настроить и проверить единый вход Azure AD в MiCloud Connect, вам потребуется выполнить следующие действия:

1. **[Настройка единого входа для MiCloud Connect с помощью Azure AD](#configure-micloud-connect-for-sso-with-azure-ad)** требуется для того, чтобы разрешить пользователям применять эту функцию и настроить параметры единого входа на стороне приложения.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
4. **[Создание тестового пользователя Mitel MiCloud Connect](#create-a-mitel-micloud-connect-test-user)** требуется для того, чтобы в MiCloud Connect существовала учетная запись пользователя Britta Simon, связанного с одноименным пользователем в Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-micloud-connect-for-sso-with-azure-ad"></a>Настройка единого входа для MiCloud Connect с помощью Azure AD

В этом разделе описано, как включить единый вход Azure AD для MiCloud Connect на портале Azure и настроить учетную запись в MiCloud Connect для использования единого входа с помощью Azure AD.

Настроить MiCloud Connect с поддержкой единого входа для Azure AD проще всего, параллельно открыв портал Azure и портал Mitel Account. Вам потребуется скопировать некоторые сведения с портала Azure на портал Mitel Account и наоборот.


1. Чтобы открыть страницу конфигураций на [портале Azure](https://portal.azure.com/), сделайте следующее:

    a. На странице интеграции с приложением **Mitel Connect** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

    b. В диалоговом окне **Выбрать метод единого входа** выберите **SAML**.

    ![Режим выбора единого входа](common/select-saml-option.png)
    
    Появится страница настройки единого входа на базе SAML.

2. Чтобы открыть диалоговое окно конфигурации на портале Mitel Account, сделайте следующее:

    a. В меню **Phone System** (Телефонная система) щелкните **Add-On Features** (Функции надстройки).

    b. Справа от элемента **Single Sign-On** (Единый вход) щелкните **Activate** (Активировать) или **Settings** (Параметры).
    
    Откроется диалоговое окно Connect Single Sign-On Settings (Параметры подключения единого входа).
    
3. Установите флажок **Enable Single Sign-On** (Включить единый вход).
    ![image](./media/mitel-connect-tutorial/Mitel_Connect_Enable.png)


4. На портале Azure щелкните значок **Изменить** в разделе **Базовая конфигурация SAML**.
    ![image](common/edit-urls.png)

    Появится диалоговое окно "Базовая конфигурация SAML".

5.  На портале Mitel Account скопируйте URL-адрес в поле **Mitel Identifier (Entity ID)** (Идентификатор Mitel (сущности)) и вставьте его в поле **Идентификатор (сущности)** на портале Azure.

6. На портале Mitel Account скопируйте URL-адрес в поле **Reply URL (Assertion Consumer Service URL)** (URL-адрес ответа (URL-адрес службы обработчика утверждений)) и вставьте его в поле **URL-адрес ответа (URL-адрес службы обработчика утверждений)** на портале Azure.  
   ![image](./media/mitel-connect-tutorial/Mitel_Azure_BasicConfig.png)

7. В текстовом поле **URL-адрес входа** введите любой из следующих URL-адресов:

    * [https://portal.shoretelsky.com]( **https://portal.shoretelsky.com** ) — для использования портала Mitel Account как приложения Mitel по умолчанию;
    * [https://teamwork.shoretel.com]( **https://teamwork.shoretel.com** ) — для использования Teamwork как приложения Mitel по умолчанию.

    **Примечание.** Приложение Mitel по умолчанию — это приложение, на которое пользователь переходит, когда щелкает плитку Mitel Connect на Панели доступа. Также это приложение, к которому предоставляется доступ при выполнении тестовой настройки из Azure AD.

8. Нажмите кнопку **Сохранить** в диалоговом окне **Базовая конфигурация SAML** на портале Azure.

9. На портале Azure на странице **Вход на основе SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать** рядом с полем **Сертификат (Base64)** , чтобы скачать **сертификат для подписи** и сохранить его на компьютер.
    ![image](./media/mitel-connect-tutorial/Azure_SigningCert.png)

10. Откройте файл сертификата для подписи в текстовом редакторе, скопируйте все данные в файле и вставьте их в поле **Signing Certificate** (Сертификат для подписи) на портале Mitel Account. 
    ![image](./media/mitel-connect-tutorial/Mitel_Connect_SigningCert.png)

11. На портале Azure на странице **Вход на основе SAML** в разделе **Настройка Mitel Connect** сделайте следующее:

    a. Скопируйте URL-адрес в поле **URL-адрес входа** и вставьте его в поле **Sign-in URL** (URL-адрес входа) на портале Mitel Account.

    b. Скопируйте URL-адрес в поле **Идентификатор Azure AD** и вставьте его в поле **Entity ID** (Идентификатор сущности) на портале Mitel Account.
    ![image](./media/mitel-connect-tutorial/Mitel_Azure_SetupConnect.png)

12. Нажмите кнопку **Save** (Сохранить) в диалоговом окне **Connect Single Sign-On Settings** (Параметры подключения единого входа).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD 

В этом разделе описано, как создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, щелкните **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана щелкните **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В диалоговом окне "Свойства пользователя" сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    a. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите brittasimon@\<домен_вашей_компании\>.\<доменная_зона\>.  
Например, BrittaSimon@contoso.com.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Mitel Connect.

1. На портале Azure щелкните **Корпоративные приложения** и **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений щелкните **Mitel Connect**.

    ![Ссылка на Mitel Connect в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** щелкните **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** выберите **Britta Simon** в списке **Пользователи**, а затем в нижней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем щелкнуть **Выбрать** в нижней части экрана.

7. В диалоговом окне **Добавление назначения** щелкните **Назначить**.

### <a name="create-a-mitel-micloud-connect-test-user"></a>Создание тестового пользователя Mitel MiCloud Connect

В этом разделе описано, как создать пользователя Britta Simon из учетной записи MiCloud Connect. Перед использованием единого входа необходимо создать и активировать пользователей.

Дополнительные сведения о добавлении пользователей на портале Mitel Account см. в статье [Adding a User](https://docs.shoretel.com/connectcloud/Account/Users/AddUser) (Добавление пользователя) базы знаний Mitel.

Создайте пользователя из своей учетной записи MiCloud Connect, указав следующие значения:

  * **Имя.** Britta Simon

* **Business Email Address:** (Рабочий адрес электронной почты) `brittasimon@<yourcompanydomain>.<extension>` .  
(Пример: [brittasimon@contoso.com](mailto:brittasimon@contoso.com).)

* **Username:** (Имя пользователя) `brittasimon@<yourcompanydomain>.<extension>`.  
(Пример: [brittasimon@contoso.com](mailto:brittasimon@contoso.com). Как правило, имя пользователя совпадает с рабочим адресом электронной почты пользователя.)

**Примечание**. Имя пользователя MiCloud Connect должно совпадать с адресом электронной почты пользователя в Azure.

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью Панели доступа.

Щелкнув плитку Mitel Connect на Панели доступа, вы автоматически перейдете на страницу входа в приложение MiCloud Connect, которое вы настроили как приложение по умолчанию в поле **URL-адрес входа**. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что такое условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
