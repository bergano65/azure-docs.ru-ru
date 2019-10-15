---
title: Руководство по интеграции единого входа Azure Active Directory с Sonarqube | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Sonarqube.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b01665e7-c3d0-4393-9286-d5bcf8cf6add
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 571d8849fd7cae5c872a56182858848dbb43ef42
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72026684"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sonarqube"></a>Руководство по интеграции единого входа Azure Active Directory с Sonarqube

В этом руководстве описано, как интегрировать Sonarqube с Azure Active Directory (Azure AD). Интеграция Sonarqube с Azure AD обеспечивает следующие возможности:

* С помощью Azure AD вы можете контролировать доступ к Sonarqube.
* Автоматический вход пользователей в Sonarqube с использованием учетных записей Azure AD.
* Централизованное управление учетными записями через портал Azure.

Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее:

* подписка Azure AD Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* подписка Sonarqube с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Sonarqube поддерживает единый вход, инициированный **поставщиком службы**.

> [!NOTE]
> Идентификатор этого приложения — фиксированное строковое значение, поэтому в одном клиенте можно настроить только один экземпляр.

## <a name="adding-sonarqube-from-the-gallery"></a>Добавление Sonarqube из коллекции

Чтобы настроить интеграцию Sonarqube с Azure AD, необходимо добавить Sonarqube из коллекции в список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в поле поиска введите **Sonarqube**.
1. Выберите **Sonarqube** в области результатов и добавьте это приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sonarqube"></a>Настройка и проверка единого входа Azure AD в Sonarqube

Настройте и проверьте единый вход Azure AD в Sonarqube с помощью тестового пользователя **B.Simon**. Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Sonarqube.

Чтобы настроить и проверить единый вход Azure AD в Sonarqube, выполните действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
    1. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD с помощью пользователя B.Simon.
    1. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить пользователю B.Simon использовать единый вход Azure AD.
1. **[Настройка единого входа в Sonarqube](#configure-sonarqube-sso)** необходима, чтобы настроить параметры единого входа на стороне приложения.
    1. **[Создание тестового пользователя в Sonarqube](#create-sonarqube-test-user)** требуется для того, чтобы в Sonarqube существовал пользователь B.Simon, связанный с одноименным пользователем в Azure AD.
1. **[Проверка единого входа](#test-sso)** необходима, чтобы убедиться в корректной работе конфигурации.

## <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Sonarqube** найдите раздел **Управление** и выберите **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

   ![Правка базовой конфигурации SAML](common/edit-urls.png)

1. На странице **Базовая конфигурация SAML** введите значения следующих полей.

    В текстовом поле **URL-адрес входа** введите URL-адрес:

    * **Для рабочей среды**:

        `https://servicessonar.corp.microsoft.com/`

    * **Для среды разработки**:

        `https://servicescode-dev.westus.cloudapp.azure.com`

1. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** найдите пункт **Сертификат (Base64)** и щелкните **Скачать**, чтобы скачать сертификат. Сохраните этот сертификат на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

1. Требуемые URL-адреса можно скопировать из раздела **Настройка Sonarqube**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

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

В этом разделе описано, как включить единый вход Azure для пользователя B.Simon, предоставив ему доступ к приложению Sonarqube.

1. На портале Azure выберите **Корпоративные приложения**, а затем —**Все приложения**.
1. Из списка приложений выберите **Sonarqube**.
1. На странице "Обзор" приложения найдите раздел **Управление** и выберите **Пользователи и группы**.

   ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Ссылка "Добавить пользователя"](common/add-assign-user.png)

1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
1. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.
1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

## <a name="configure-sonarqube-sso"></a>Настройка единого входа в Sonarqube

1. Откройте новое окно веб-браузера и войдите на корпоративный сайт Sonarqube с правами администратора.

2. Установите подключаемый модуль SAML из Marketplace для Sonarqube.

3. В верхней части страницы щелкните **ADMIN** (Администрирование) и выберите **SAML**.

4. На странице **SAML** выполните следующие действия.

    ![Настройка Sonarqube](./media/sonarqube-tutorial/config01.png)

    a. Для параметра **Enabled** (Включено) переключите значение на **yes** (да).

    b. В текстовом поле **Application ID** (Идентификатор приложения) введите имя, например **sonarqube**.

    c. В текстовом поле **Provider Name** (Имя поставщика) введите имя, например **SAML**.

    d. В текстовое поле **Provider ID** (Идентификатор поставщика) вставьте значение **идентификатора Azure AD**, скопированное на портале Azure.

    д. В текстовое поле **SAML login URL** (URL-адрес входа SAML) вставьте **URL-адрес входа**, скопированный на портале Azure.

    Е. Откройте сертификат в кодировке Base64 в Блокноте, скопируйте его содержимое и вставьте его в текстовое поле **Provider certificate** (Сертификат поставщика).

    ж. В текстовом поле **SAML user login attribute** (Атрибут имени для входа пользователя SAML) введите значение `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    h. В текстовом поле **SAML user name attribute** (Атрибут имени пользователя SAML) введите значение `http://schemas.microsoft.com/identity/claims/displayname`.

    i. В текстовом поле **SAML user email attribute** (Атрибут электронной почты пользователя SAML) введите значение `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    j. Выберите команду **Сохранить**.

### <a name="create-sonarqube-test-user"></a>Создание тестового пользователя Sonarqube

На этом шаге вы создадите пользователя с именем B.Simon в приложении Sonarqube. Обратитесь в  [службу поддержки Sonarqube](https://www.sonarsource.com/support/), чтобы добавить пользователей на платформу Sonarqube. Перед использованием единого входа необходимо создать и активировать пользователей. 

## <a name="test-sso"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Sonarqube на Панели доступа, вы автоматически войдете в приложение Sonarqube, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Проверьте работу Sonarqube с Azure AD](https://aad.portal.azure.com/)

