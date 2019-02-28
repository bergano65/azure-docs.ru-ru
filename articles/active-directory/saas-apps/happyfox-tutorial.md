---
title: Руководство. Интеграция Azure Active Directory с HappyFox | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и HappyFox.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8204ee77-f64b-4fac-b64a-25ea534feac0
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 039f00f1a61a2f06374fea144181ab6205828122
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56874738"
---
# <a name="tutorial-azure-active-directory-integration-with-happyfox"></a>Руководство. Интеграция Azure Active Directory с HappyFox

В этом руководстве описано, как интегрировать HappyFox с Azure Active Directory (Azure AD).
Интеграция приложения HappyFox с Azure AD обеспечивает следующие преимущества.

* С помощью Azure AD можно контролировать доступ к HappyFox.
* Вы можете включить автоматический вход пользователей в HappyFox (единый вход) с помощью учетных записей Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с HappyFox, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* подписка на HappyFox с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* HappyFox поддерживает единый вход, инициированный **поставщиком услуг**.


* HappyFox поддерживает **JIT**-подготовку пользователей.


## <a name="adding-happyfox-from-the-gallery"></a>Добавление HappyFox из коллекции

Чтобы настроить интеграцию приложения HappyFox с Azure AD, вам нужно добавить это приложение из коллекции в свой список управляемых приложений SaaS.

**Добавление приложения HappyFox из коллекции**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **HappyFox**, выберите **HappyFox** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![HappyFox в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в HappyFox с использованием тестового пользователя **Britta Simon**.
Чтобы обеспечить работу единого входа, нужно установить связь между пользователем Azure AD и одноименным пользователем в HappyFox.

Чтобы настроить и проверить единый вход Azure AD в HappyFox, выполните следующие действия:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в HappyFox](#configure-happyfox-single-sign-on)** требуется для того, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя HappyFox](#create-happyfox-test-user)** требуется для того, чтобы в HappyFox существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в HappyFox, выполните следующие действия:

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **HappyFox** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    ![Сведения о домене и URL-адресах единого входа для приложения HappyFox](common/sp-identifier.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<subdomain>.happyfox.com/`.

    b. В текстовом поле **Идентификатор (сущности)** введите URL-адрес в следующем формате: `https://<subdomain>.happyfox.com/saml/metadata/`.

    > [!NOTE]
    > Эти значения приведены для примера. Необходимо обновить эти значения действующим URL-адресом для входа и идентификатором. Чтобы получить их, обратитесь в [службу поддержки клиентов HappyFox](https://support.happyfox.com/home). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

4. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Загрузить**, чтобы загрузить требуемый **сертификат (Base64)** из предложенных вариантов, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

6. В разделе **Set up HappyFox** (Настройка HappyFox) скопируйте требуемый URL-адрес.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    б) Идентификатор Azure AD.

    в) URL-адрес выхода.

### <a name="configure-happyfox-single-sign-on"></a>Настройка единого входа в HappyFox

1. В другом окне веб-браузера войдите в свой клиент HappyFox с правами администратора.

2. Перейдите в разделе **Manage** (Управление) и щелкните вкладку **Integrations** (Интеграция).

    ![Настройка единого входа](./media/happyfox-tutorial/header.png) 

3. На вкладке Integrations (Интеграция) щелкните **Configure** (Настройка) в разделе **SAML Integration** (Интеграция SAML), чтобы открыть параметры единого входа.

    ![Настройка единого входа](./media/happyfox-tutorial/configure.png)

4. В разделе конфигурации SAML вставьте **URL-адрес для входа**, скопированный на портале Azure, в текстовое поле **SSO Target URL** (Целевой URL-адрес единого входа).

    ![Настройка единого входа](./media/happyfox-tutorial/targeturl.png)

5. Откройте в Блокноте сертификат, загруженный с портала Azure, и вставьте его содержимое в раздел **IdP Signature** (Подпись поставщика удостоверений).

    ![Настройка единого входа](./media/happyfox-tutorial/cert.png)

6. Нажмите кнопку **Save Settings** (Сохранить параметры).

    ![Настройка единого входа](./media/happyfox-tutorial/savesettings.png)

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    а. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon@yourcompanydomain.extension**.  
    Например BrittaSimon@contoso.com.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе мы разрешим пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к HappyFox.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения** и **HappyFox**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **HappyFox**.

    ![Ссылка на HappyFox в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-happyfox-test-user"></a>Создание тестового пользователя HappyFox

В этом разделе объясняется, как создать в HappyFox пользователя Britta Simon. Приложение HappyFox поддерживает JIT-подготовку пользователей, которая включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Если пользователь еще не существует в HappyFox, он создается после проверки подлинности.

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

1. Когда вы нажмете плитку HappyFox на панели доступа, должна появиться страница входа в приложение HappyFox. На этой странице входа вы увидите кнопку **SAML**.

    ![Подключаемый модуль](./media/happyfox-tutorial/saml.png)

2. Нажмите кнопку **SAML**, чтобы войти в HappyFox с помощью учетной записи Azure AD.

См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)