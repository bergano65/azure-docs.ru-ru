---
title: Руководство по Интеграция Azure Active Directory с InsideView | Документация Майкрософт
description: Из этого руководства вы узнаете, как настроить единый вход Azure Active Directory в InsideView.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c489a7ab-6b1f-4efb-8a66-8bc13bca78c3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: jeedes
ms.openlocfilehash: 0fdabd237fa128326673d84e889387d03f184b00
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236577"
---
# <a name="tutorial-azure-active-directory-integration-with-insideview"></a>Руководство по Интеграция Azure Active Directory с InsideView

В этом руководстве описано, как интегрировать InsideView с Azure Active Directory (Azure AD).
Такая интеграция обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к InsideView.
* Вы можете включить автоматический вход пользователей в InsideView (единый вход) с помощью учетных записей Azure Active Directory.
* Вы можете управлять учетными записями централизованно — через портал Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с InsideView, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/));
* подписка InsideView с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure Active Directory в тестовой среде.

* InsideView поддерживает единый вход, инициируемый поставщиком удостоверений.

## <a name="add-insideview-from-the-gallery"></a>Добавление InsideView из коллекции

Чтобы настроить интеграцию InsideView с Azure Active Directory, нужно добавить InsideView из коллекции в список управляемых приложений SaaS.

1. На [портале Azure](https://portal.azure.com) в области слева щелкните **Azure Active Directory**.

    ![Выберите Azure Active Directory.](common/select-azuread.png)

2. Выберите **Корпоративные приложения** > **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, выберите **Новое приложение** в верхней части окна.

    ![Выбор элемента "Новое приложение"](common/add-new-app.png)

4. В поле поиска введите **InsideView**. В результатах поиска выберите **InsideView**, а затем щелкните **Добавить**.

    ![Результаты поиска](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в InsideView с использованием тестового пользователя Britta Simon.
Для единого входа необходимо установить связь между пользователем в Azure AD и соответствующим пользователем в InsideView.

Чтобы настроить и проверить единый вход Azure AD в InsideView, потребуется выполнить следующие действия.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в InsideView](#configure-insideview-single-sign-on)** на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить ему использовать единый вход Azure AD.
5. **[Создание тестового пользователя InsideView](#create-an-insideview-test-user)** , связанного с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться, что конфигурация работает правильно.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в InsideView, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением InsideView выберите **Единый вход**.

    ![Выбор параметра "Единый вход"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Выбор метода единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Значок "Изменить"](common/edit-urls.png)

4. В диалоговом окне **Базовая конфигурация SAML** выполните следующие действия.

    ![Диалоговое окно "Базовая конфигурация SAML"](common/idp-reply.png)

    В поле **URL-адрес ответа** введите URL-адрес в следующем формате:

    `https://my.insideview.com/iv/<STS Name>/login.iv`

    > [!NOTE]
    > Это значение является заполнителем. Необходимо указать фактический URL-адрес ответа. Чтобы получить это значение, обратитесь к [группе поддержки InsideView](mailto:support@insideview.com). Можно также ознакомиться с шаблонами в диалоговом окне **Базовая конфигурация SAML** на портале Azure.

5. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните ссылку **Скачать** рядом с нужным элементом **Сертификат (необработанный)** и сохраните сертификат на компьютере.

    ![Ссылка для скачивания сертификата](common/certificateraw.png)

6. Требуемые URL-адреса можно скопировать из раздела **Настройка InsideView**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    1. **URL-адрес входа**.

    1. **Идентификатор Azure AD**

    1. **URL-адрес выхода**.

### <a name="configure-insideview-single-sign-on"></a>Настройка единого входа в InsideView

1. В новом окне веб-браузера войдите на свой корпоративный сайт InsideView с правами администратора.

1. В верхней части окна, выберите **Admin** (Администрирование), **SingleSignOn Settings** (Параметры единого входа) и затем — **Add SAML** (Добавить конфигурацию SAML).
   
   ![Параметры единого входа SAML](./media/insideview-tutorial/ic794135.png "Параметры единого входа SAML")

1. В разделе **Add a New SAML** (Добавление конфигурации SAML) сделайте следующее.

    ![Раздел "Add a New SAML" (Добавление конфигурации SAML)](./media/insideview-tutorial/ic794136.png "Раздел \"Add a New SAML\" (Добавление конфигурации SAML)")

    1. В поле **STS Name** (Имя службы токенов безопасности) введите имя конфигурации.

    1. В поле **SamlP/WS-Fed Unsolicited EndPoint** (Незапрашиваемая конечная точка SamlP/WS-Fed) вставьте значение **URL-адрес входа**, скопированное на портале Azure.

    1. Откройте необработанный сертификат, скачанный с портала Azure. Скопируйте содержимое сертификата в буфер обмена, а затем вставьте его в поле **STS Certificate** (Сертификат службы токенов безопасности).

    1. В поле **Crm User Id Mapping** (Сопоставление ИД пользователя CRM) введите **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** .

    1. В поле **Crm Email Mapping** (Сопоставление электронной почты в CRM) введите **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** .

    1. В поле **Crm First Name Mapping** (Сопоставление имени в CRM) введите **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname** .

    1. В поле **Crm lastName Mapping** (Сопоставление фамилии в CRM) введите **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname** .  

    1. Щелкните **Сохранить**.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

В этом разделе описано, как создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Выбор "Все пользователи"](common/users.png)

2. В верхней части окна выберите **Новый пользователь**.

    ![Выбор "Новый пользователь"](common/new-user.png)

3. В диалоговом окне **Пользователь** сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    1. В поле **Имя** введите **BrittaSimon**.
  
    1. В поле **Имя пользователя** введите **BrittaSimon @\<ваш_домен>.\<доменная_зона>** . (Например, BrittaSimon@contoso.com).

    1. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    1. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к InsideView.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **InsideView**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. Из списка приложений выберите **InsideView**.

    ![Список приложений](common/all-applications.png)

3. В области слева выберите **Пользователи и группы**.

    ![Выбор параметра "Пользователи и группы"](common/users-groups-blade.png)

4. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** — **Пользователи и группы**.

    ![Выбор элемента "Добавить пользователя"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** выберите **Britta Simon** из списка пользователей, а затем в нижней части окна нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка. В нижней части окна нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** выберите **Назначить**.

### <a name="create-an-insideview-test-user"></a>Создание тестового пользователя InsideView

Чтобы разрешить пользователям Azure AD входить в InsideView, их нужно добавить в InsideView. Необходимо добавить их вручную.

Чтобы создать пользователей или контакты в InsideView, обратитесь к [группе поддержки InsideView](mailto:support@insideview.com).

> [!NOTE]
> Вы можете использовать любые другие инструменты или API, предоставленные InsideView, для подготовки учетных записей пользователей Azure AD.

### <a name="test-single-sign-on"></a>Проверка единого входа

Теперь необходимо проверить конфигурацию единого входа Azure AD с помощью Панели доступа.

Щелкнув элемент "InsideView" на Панели доступа, вы автоматически войдете в экземпляр InsideView, для которого настроили единый вход. Дополнительные сведения о Панели доступа см. в статье [Доступ и использование приложений на портале Мои приложения](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
