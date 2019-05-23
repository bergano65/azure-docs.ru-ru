---
title: Руководство по Интеграция Azure Active Directory с BlueJeans | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и BlueJeans.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2830e95f042d5c83d91d6c026d2fa2e70eb3e3b2
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65901086"
---
# <a name="tutorial-azure-active-directory-integration-with-bluejeans"></a>Руководство по Интеграция Azure Active Directory с BlueJeans

В этом руководстве описано, как интегрировать BlueJeans с Azure Active Directory (Azure AD).
Интеграция Azure AD с приложением BlueJeans обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к BlueJeans.
* Вы можете включить автоматический вход пользователей в BlueJeans (единый вход) с помощью учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с BlueJeans, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/));
* Подписка BlueJeans с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* BlueJeans поддерживает единый вход, инициированный **поставщиком услуг**.

* BlueJeans поддерживает [**автоматическую** подготовку пользователей](bluejeans-provisioning-tutorial.md).

## <a name="adding-bluejeans-from-the-gallery"></a>Добавление BlueJeans из коллекции

Чтобы настроить интеграцию BlueJeans с Azure AD, необходимо добавить BlueJeans из коллекции в список управляемых приложений SaaS.

**Чтобы добавить BlueJeans из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **BlueJeans**, выберите **BlueJeans** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![BlueJeans в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в BlueJeans с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в BlueJeans.

Чтобы настроить и проверить единый вход Azure AD в BlueJeans, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в BlueJeans](#configure-bluejeans-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя BlueJeans](#create-bluejeans-test-user)** нужно для того, чтобы в BlueJeans также существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в BlueJeans, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **BlueJeans** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](media/bluejeans-tutorial/edit-urls-bluejeans.png)

4. В диалоговом окне **Базовая конфигурация SAML** введите следующие значения.

    ![Сведения о домене и URL-адресах единого входа для приложения BlueJeans](media/bluejeans-tutorial/tutorial_bluejeans-basic-configuration.png)

   - В поле **Идентификатор** введите следующее: `http://samlsp.bluejeans.com`
    
   - В текстовом поле **URL-адрес входа** введите URL-адрес целевой страницы, предоставленный BlueJeans (для получения этого значения вы можете обратиться к [группе поддержки Bluejeans](https://support.bluejeans.com/contact)): `https://<companyname>.bluejeans.com`
    
   - Выберите команду **Сохранить**.

5. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Загрузить**, чтобы загрузить требуемый **сертификат (Base64)** из предложенных вариантов, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

6. Скопируйте требуемый URL-адрес из раздела **Настройка BlueJeans**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    b. Идентификатор Azure AD

    c. URL-адрес выхода.

### <a name="configure-bluejeans-single-sign-on"></a>Настройка единого входа в BlueJeans

1. В другом окне веб-браузера войдите на свой корпоративный веб-сайт **BlueJeans** с правами администратора.

2. Последовательно щелкните **ADMIN \> GROUP SETTINGS \> SECURITY** (Администратор > Параметры группы > Безопасность).

    ![Администратор](./media/bluejeans-tutorial/ic785868.png "Администратор")

3. В разделе **SECURITY** (Безопасность) выполните следующие действия:

    ![Единый вход SAML](./media/bluejeans-tutorial/ic785869.png "Единый вход SAML")

    a. Выберите параметр **SAML Single Sign On**(Единый вход SAML).

    b. Установите флажок **Enable automatic provisioning**(Включить автоматическую подготовку).

4. После этого выполните следующие действия.

    ![Путь к сертификату](./media/bluejeans-tutorial/ic785870.png "Путь к сертификату")

    a. Щелкните **Choose a File** (Выбрать файл), чтобы отправить сертификат в кодировке Base64, скачанный с портала Azure.

    b. В текстовое поле **Login URL** (URL-адрес входа) вставьте **URL-адрес входа**, скопированный на портале Azure.

    c. В текстовое поле **Password Change URL** (URL-адрес для изменения пароля) вставьте **URL-адрес изменения пароля**, скопированный на портале Azure.

    d. В текстовое поле **Logout URL** (URL-адрес выхода) вставьте **URL-адрес выхода**, скопированный на портале Azure.

5. После этого выполните следующие действия.

    ![Сохранение изменений](./media/bluejeans-tutorial/ic785874.png "Сохранение изменений")

    a. В текстовое поле **User Id** (Идентификатор пользователя) введите `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    b. В текстовое поле **Email** (Электронная почта) введите `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    c. Нажмите кнопку **Сохранить изменения**.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    а. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите `brittasimon\@yourcompanydomain.extension`. Например, BrittaSimon@contoso.com.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к BlueJeans.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем **BlueJeans**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. Из списка приложений выберите **BlueJeans**.

    ![Ссылка на BlueJeans в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-bluejeans-test-user"></a>Создание тестового пользователя BlueJeans

Цель этого раздела — создать пользователя с именем Britta Simon в BlueJeans. BlueJeans поддерживает автоматическую подготовку пользователей, которая по умолчанию включена. Дополнительные сведения о настройке автоматической подготовки пользователей можно найти [здесь](bluejeans-provisioning-tutorial.md).

**Если необходимо создать пользователя вручную, выполните следующие действия:**

1. Войдите на свой корпоративный веб-сайт **BlueJeans** с правами администратора.

2. Последовательно выберите пункты **ADMIN \> MANAGE USERS \> ADD USER** (Администратор > Управление пользователями > Добавить пользователя).

    ![Администратор](./media/bluejeans-tutorial/ic785877.png "Администратор")

    > [!IMPORTANT]
    > Вкладка **ADD USER** (Добавить пользователя) доступна, только если на вкладке **SECURITY** (Безопасность) снят флажок **Enable automatic provisioning** (Включить автоматическую подготовку).

3. В разделе **ADD USER** (Добавление пользователя) выполните следующие действия.

    ![Добавление пользователя](./media/bluejeans-tutorial/ic785886.png "Добавление пользователя")

    a. В текстовое поле **First Name** (Имя) введите имя пользователя, например **Britta**.

    b. В текстовое поле **Last Name** (Фамилия) введите фамилию пользователя, например **Simon**.

    c. В текстовом поле **Pick a BlueJeans Username** (Выбрать имя пользователя BlueJeans) введите имя пользователя, например **Brittasimon**

    d. Введите пароль в поле **Create a Password** (Создать пароль).

    д. В текстовое поле **Company** (Компания) введите название компании.

    Е. В текстовое поле **Email Address** (Адрес электронной почты) введите адрес электронной почты пользователя, например `brittasimon\@contoso.com`.

    ж. В текстовое поле **Create a BlueJeans Meeting I.D** (Создать идентификатор собрания BlueJeans) введите идентификатор собрания.

    h. В текстовое поле **Pick a Moderator Passcode** (Выбрать секретный код модератора) введите секретный код.

    i. Щелкните **CONTINUE** (Продолжить).

    ![Добавление пользователя](./media/bluejeans-tutorial/ic785887.png "Добавление пользователя")

    Дж. Нажмите кнопку **Add user** (Добавить пользователя).

> [!NOTE]
> Вы можете использовать для создания учетной записи пользователя BlueJeans любые другие средства или API, предоставленные BlueJeans для подготовки учетных записей пользователей AAD.

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку BlueJeans на панели доступа, вы автоматически войдете в приложение BlueJeans, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
