---
title: Руководство по Интеграция единого входа Azure Active Directory с 8x8 | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и 8x8.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b34a6edf-e745-4aec-b0b2-7337473d64c5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/20/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c598222978a1c831be6f5e9db9eb87b2d6b6b96
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2020
ms.locfileid: "78968639"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-8x8"></a>Руководство по Интеграция единого входа Azure Active Directory с 8x8

В этом руководстве описано, как интегрировать 8x8 с Azure Active Directory (Azure AD). Интеграция 8x8 с Azure AD обеспечивает следующие возможности.

* Управление доступом к 8x8 с помощью Azure AD.
* Автоматический вход пользователей в 8x8 с помощью учетных записей Azure AD.
* Централизованное управление учетными записями через портал Azure.

Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее.

* Подписка Azure AD. Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* Подписка 8x8.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* 8x8 поддерживает вход, инициированный **поставщиком услуг или поставщиком удостоверений**.

* После настройки 8x8 можете применить функцию управления сеансом, которая защищает от хищения конфиденциальных данных вашей организации и несанкционированного доступа к ним в реальном времени. Управление сеансом является расширением функции условного доступа. [Узнайте, как применять управление сеансами с помощью Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

> [!NOTE]
> Идентификатор этого приложения — фиксированное строковое значение, поэтому в одном клиенте можно настроить только один экземпляр.

## <a name="adding-8x8-from-the-gallery"></a>Добавление 8x8 из коллекции

Чтобы настроить интеграцию 8x8 с Azure AD, необходимо добавить 8x8 из коллекции в список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в поле поиска введите **8x8**.
1. Выберите **8x8** в области результатов и добавьте это приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.

## <a name="configure-and-test-azure-ad-single-sign-on-for-8x8"></a>Настройка и проверка единого входа Azure AD для 8x8

Настройте и проверьте единый вход Azure AD в 8x8 с помощью тестового пользователя **B.Simon**. Чтобы обеспечить работу единого входа, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в 8x8.

Чтобы настроить и проверить единый вход Azure AD в 8x8, выполните действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
    1. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD с помощью пользователя B.Simon.
    1. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить пользователю B.Simon использовать единый вход Azure AD.
1. **[Настройка единого входа в 8x8](#configure-8x8-sso)** необходима, чтобы настроить параметры единого входа на стороне приложения.
    1. **[Создание тестового пользователя в 8x8](#create-8x8-test-user)** нужно для того, чтобы в 8x8 существовал пользователь B.Simon, связанный с одноименным пользователем в Azure AD.
1. **[Проверка единого входа](#test-sso)** позволяет убедиться в правильности конфигурации.

## <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **8x8** найдите раздел **Управление** и выберите **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

   ![Изменение базовой конфигурации SAML](common/edit-urls.png)

1. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    а. В текстовом поле **Идентификатор** введите URL-адрес: `https://sso.8x8.com/saml2`

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес `https://sso.8x8.com/saml2`

1. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** найдите пункт **Сертификат (Base64)** и щелкните **Скачать**, чтобы скачать сертификат. Сохраните этот сертификат на компьютере. Этот сертификат вам потребуется позже, при выполнении инструкций **по настройке единого входа на стороне 8x8**.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

1. В разделе **Настройка 8x8** скопируйте URL-адреса, которые будут использоваться позже в этом руководстве.

    ![Копирование URL-адресов настройки](./media/8x8virtualoffice-tutorial/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

В этом разделе описано, как на портале Azure создать тестового пользователя с именем B.Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.
1. В верхней части экрана выберите **Новый пользователь**.
1. В разделе **Свойства пользователя** выполните следующие действия.
   1. В поле **Имя** введите `B.Simon`.  
   1. В поле **Имя пользователя** введите username@companydomain.extension. Например, `B.Simon@contoso.com`.
   1. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.
   1. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как включить единый вход в Azure для пользователя B.Simon, предоставив этому пользователю доступ к 8x8.

1. На портале Azure выберите **Корпоративные приложения**, а затем — **Все приложения**.
1. Из списка приложений выберите **8x8**.
1. На странице "Обзор" приложения найдите раздел **Управление** и выберите **Пользователи и группы**.

   ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Ссылка "Добавить пользователя"](common/add-assign-user.png)

1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

## <a name="configure-8x8-sso"></a>Настройка единого входа на стороне 8x8

Действия в этой части руководства зависят от того, какой тип подписки 8x8 вы используете.

* Если вы используете 8x8 Editions и X Series и управление через Configuration Manager, перейдите к разделу [Настройка с помощью 8x8 Configuration Manager](#configure-8x8-configuration-manager).
* Если вы используете Virtual Office и управление через Account Manager, перейдите к разделу [Настройка с помощью 8x8 Account Manager](#configure-8x8-account-manager).

### <a name="configure-8x8-configuration-manager"></a>Настройка с помощью 8x8 Configuration Manager

1. Войдите в [8x8 Configuration Manager](https://vo-cm.8x8.com/).

1. На домашней странице щелкните **Identity Management** (Управление идентификаторами).

    ![8x8 Configuration Manager](./media/8x8virtualoffice-tutorial/configure1.png)

1. Установите флажок **Single Sign On (SSO)** (Единый вход) и выберите **Microsoft Azure AD**.

    ![8x8 Configuration Manager](./media/8x8virtualoffice-tutorial/configure2.png)

1. Скопируйте три URL-адреса и сертификат подписи со страницы **Настройка единого входа с помощью SAML** в Azure AD в раздел **Microsoft Azure AD SAML Settings** (Параметры SAML для Microsoft Azure AD) в 8x8 Configuration Manager.

    ![8x8 Configuration Manager](./media/8x8virtualoffice-tutorial/configure3.png)

    а. Скопируйте **URL-адрес входа** в поле **URL-адреса входа поставщика удостоверений**.

    b. Скопируйте **идентификатор Azure AD** в поле **URL-адреса или URN издателя поставщика удостоверений**.

    c. Скопируйте **URL-адрес выхода** в поле **IDP Logout URL** (URL-адрес выхода поставщика удостоверений).

    d. Скачайте **Сертификат (Base64)** и отправьте его в раздел **Certificate** (Сертификат).

    д) Выберите команду **Сохранить**.

### <a name="configure-8x8-account-manager"></a>Настройка с помощью 8x8 Account Manager

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

    а. В текстовое поле **Sign In URL** (URL-адрес входа) вставьте значение **URL-адрес входа**, скопированное на портале Azure.

    b. В текстовое поле **Sign Out URL** (URL-адрес выхода) вставьте значение **URL-адрес выхода**, скопированное на портале Azure.

    c. В текстовое поле **Issuer URL** (URL-адрес издателя) вставьте значение **Идентификатор Azure AD**, скопированное на портале Azure.

    d. Нажмите кнопку **Browse** (Обзор), чтобы передать сертификат, скачанный с портала Azure.

    д) Нажмите кнопку **Сохранить** .

### <a name="create-8x8-test-user"></a>Создание тестового пользователя 8x8

В этом разделе описано, как создать пользователя Britta Simon в приложении 8x8. Обратитесь к [группе поддержки 8x8](https://www.8x8.com/about-us/contact-us), чтобы добавить пользователей на платформу 8x8. Перед использованием единого входа необходимо создать и активировать пользователей.

## <a name="test-sso"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку 8x8 на Панели доступа, вы автоматически войдете в приложение 8x8, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Проверьте работу 8x8 с Azure AD](https://aad.portal.azure.com/)

- [Что такое управление сеансами в Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Защита приложения 8x8 с помощью функции управления настройками условного доступа](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)