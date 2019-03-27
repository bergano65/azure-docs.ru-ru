---
title: Руководство. Интеграция Azure Active Directory с 8x8 Virtual Office | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и 8x8 Virtual Office.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: b34a6edf-e745-4aec-b0b2-7337473d64c5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9385ec6a86c24e619ffafdae67bc66f66e099f3b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57842742"
---
# <a name="tutorial-azure-active-directory-integration-with-8x8-virtual-office"></a>Руководство по Интеграция Azure Active Directory с 8x8 Virtual Office

В этом руководстве описано, как интегрировать 8x8 Virtual Office с Azure Active Directory (Azure AD).
Интеграция 8x8 Virtual Office с Azure AD обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к 8x8 Virtual Office.
* Вы можете включить автоматический вход пользователей в 8x8 Virtual Office (единый вход) с помощью учетных записей Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с 8x8 Virtual Office, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* Подписка 8x8 Virtual Office с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.


* 8x8 Virtual Office поддерживает единый вход инициированной **выдающей точки распространения**.

* 8x8 Virtual Office поддерживает **JIT**-подготовку пользователей.

## <a name="adding-8x8-virtual-office-from-the-gallery"></a>Добавление 8x8 Virtual Office из коллекции.

Чтобы настроить интеграцию приложения 8x8 Virtual Office с Azure AD, необходимо добавить это приложение из коллекции в список управляемых приложений SaaS.

**Чтобы добавить 8x8 Virtual Office из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **8x8 Virtual Office**, выберите **8x8 Virtual Office** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![8x8 Virtual Office в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в приложение 8x8 Virtual Office с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в 8x8 Virtual Office.

Чтобы настроить и проверить единый вход Azure AD в 8x8 Virtual Office, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в 8x8 Virtual Office](#configure-8x8-virtual-office-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя 8x8 Virtual Office](#create-8x8-virtual-office-test-user)** нужно для того, чтобы в 8x8 Virtual Office также существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в 8x8 Virtual Office, сделайте следующее.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **8x8 Virtual Office** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. В диалоговом окне **Базовая конфигурация SAML** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения 8x8 Virtual Office](common/idp-intiated.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в формате `https://sso.8x8.com/saml2`.

    б) В текстовом поле **URL-адрес ответа** введите URL-адрес в формате `https://sso.8x8.com/saml2`.

4. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Загрузить**, чтобы загрузить требуемый **сертификат (необработанный)** из предложенных вариантов и сохранить его на компьютере.

    ![Ссылка для скачивания сертификата](common/certificateraw.png)

6. Скопируйте требуемый URL-адрес(а) в разделе **Настройка 8x8 Virtual Office**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    б) Идентификатор Azure AD.

    в) URL-адрес выхода.

### <a name="configure-8x8-virtual-office-single-sign-on"></a>Настройка единого входа 8x8 Virtual Office.

1. Войдите в клиент 8x8 Virtual Office от имени администратора.

1. На панели приложения выберите **Virtual Office Account Mgr** (Диспетчер учетных записей Virtual Office).

    ![Настройка на стороне приложения](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)

1. Выберите в качестве учетной записи, которой нужно управлять, **Business** (Рабочая) и нажмите кнопку **Sign In** (Вход).

    ![Настройка на стороне приложения](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)

1. В списке меню щелкните вкладку **ACCOUNTS** (Учетные записи).

   ![Настройка на стороне приложения](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)

1. В списке Accounts (Учетные записи) щелкните **Single Sign On** (Единый вход).
  
   ![Настройка на стороне приложения](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)

1. В разделе "Authentication methods" (Методы аутентификации) установите флажок **Signle Sign On** (Единый вход), а затем щелкните **SAML**.

   ![Настройка на стороне приложения](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)

1. В разделе **SAML Single Sign on** (Единый вход SAML) выполните следующие действия.

   ![Настройка на стороне приложения](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)

   a. В текстовое поле **Sign In URL** (URL-адрес входа) вставьте значение **URL-адрес входа**, скопированное на портале Azure.

   b. В текстовое поле **Sign Out URL** (URL-адрес выхода) вставьте значение **URL-адрес выхода**, скопированное на портале Azure.

   c. В текстовое поле **Issuer URL** (URL-адрес издателя) вставьте значение **Идентификатор Azure AD**, скопированное на портале Azure.

   d. Нажмите кнопку **Browse** (Обзор), чтобы передать сертификат, скачанный с портала Azure.

   д. Нажмите кнопку **Сохранить** .

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD 

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    а. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon\@<домен_вашей_компании>.<доменная_зона>**.  
    Например BrittaSimon@contoso.com.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как позволить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к 8x8 Virtual Office.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем **8x8 Virtual Office**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений введите и выберите **8x8 Virtual Office**.

    ![Выбор 8x8 Virtual Office в списке "Приложения"](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-8x8-virtual-office-test-user"></a>Создание тестового пользователя 8x8 Virtual Office

В этом разделе вы создадите в 8x8 Virtual Office пользователя с именем Britta Simon. Приложение 8x8 Virtual Office поддерживает **JIT-подготовку пользователей**, которая включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Если пользователь еще не существует в 8x8 Virtual Office, он создается после проверки подлинности.

> [!NOTE]
> Чтобы создать пользователя вручную, необходимо обратиться к [группе поддержки 8x8 Virtual Office](https://www.8x8.com/about-us/contact-us).

### <a name="test-single-sign-on"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку "8x8 Virtual Office" на панели доступа, вы автоматически войдете в приложение 8x8 Virtual Office, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

