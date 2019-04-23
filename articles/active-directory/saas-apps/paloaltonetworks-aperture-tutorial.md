---
title: Руководство по Интеграция Azure Active Directory с Palo Alto Networks - Aperture | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Palo Alto Networks - Aperture.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a5ea18d3-3aaf-4bc6-957c-783e9371d0f1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: jeedes
ms.openlocfilehash: 375b58f47454a7f5904bcdbd132b8318091e9ebd
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59275085"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---aperture"></a>Руководство по Интеграция Azure Active Directory с Palo Alto Networks - Aperture

В этом руководстве описано, как интегрировать Azure Active Directory (Azure AD) с Palo Alto Networks - Aperture.
Интеграция Azure AD с Palo Alto Networks - Aperture обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к Palo Alto Networks - Aperture.
* Вы можете включить автоматический вход пользователей (единый вход) в Palo Alto Networks — Aperture с использованием учетных записей Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Palo Alto Networks - Aperture, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* подписка Palo Alto Networks — Aperture с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Palo Alto Networks — Aperture поддерживает единый вход, инициированный **поставщиком услуг** и **поставщиком удостоверений**.

## <a name="adding-palo-alto-networks---aperture-from-the-gallery"></a>Добавление Palo Alto Networks - Aperture из коллекции

Чтобы настроить интеграцию Palo Alto Networks - Aperture с Azure AD, необходимо добавить Palo Alto Networks - Aperture из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Palo Alto Networks - Aperture из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Palo Alto Networks - Aperture** и выберите **Palo Alto Networks - Aperture** на панели результатов, а затем нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![Palo Alto Networks - Aperture в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Palo Alto Networks — Aperture с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Palo Alto Networks — Aperture.

Чтобы настроить и проверить единый вход Azure AD в Palo Alto Networks - Aperture, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в Palo Alto Networks — Aperture](#configure-palo-alto-networks---aperture-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя Palo Alto Networks — Aperture](#create-palo-alto-networks---aperture-test-user)** требуется для того, чтобы в Palo Alto Networks — Aperture существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в Palo Alto Networks — Aperture, сделайте следующее.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Palo Alto Networks — Aperture** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. Если вы хотите настроить приложение в режиме, инициируемом **поставщиком удостоверений**, в разделе **Базовая конфигурация SAML** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения Palo Alto Networks - Aperture](common/idp-intiated.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в формате `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/metadata`.

    б) В текстовом поле **URL-адрес ответа** введите URL-адрес в формате `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/auth`.

5. Чтобы настроить приложение для работы в режиме, инициируемом **поставщиком услуг**, щелкните **Задать дополнительные URL-адреса** и выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения Palo Alto Networks - Aperture](common/metadata-upload-additional-signon.png)

    В текстовом поле **URL-адрес входа** введите URL-адрес в формате `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/sign_in`.

    > [!NOTE]
    > Эти значения приведены для примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Чтобы получить эти значения, обратитесь к [группе поддержки Palo Alto Networks - Aperture](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

6. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Загрузить**, чтобы загрузить требуемый **сертификат (Base64)** из предложенных вариантов, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

7. Скопируйте требуемый URL-адрес из раздела **Настройка Palo Alto Networks — Aperture**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    b. Идентификатор Azure AD

    c. URL-адрес выхода.

### <a name="configure-palo-alto-networks---aperture-single-sign-on"></a>Настройка единого входа в Palo Alto Networks — Aperture

1. В другом окне веб-браузера войдите на веб-сайт Palo Alto Networks - Aperture в качестве администратора.

2. В верхней строке меню щелкните **SETTINGS** (Параметры).

    ![Вкладка "SETTINGS" (Параметры)](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_settings.png)

3. Перейдите в раздел **APPLICATION** (Приложение) и щелкните форму **Authentication** (Аутентификация) в левой части меню.

    ![Вкладка "Authentication" (Аутентификация)](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_auth.png)
    
4. На странице **Authentication** (Аутентификация) выполните следующие действия.
    
    ![Вкладка "Authentication" (Аутентификация)](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_singlesignon.png)

    a. Установите флажок **Enable Single Sign-On (Supported SSP Providers are Okta, One login)** (Включить единый вход (поддерживаемые поставщики общих служб: Okta, One login)) в поле **Single Sign-On** (Единый вход).

    b. В текстовое поле **Identity Provider ID** (Идентификатор поставщика удостоверений) вставьте значение **идентификатора Azure AD**, скопированное на портале Azure.

    c. Щелкните **Choose File** (Выбор файла), чтобы передать сертификат, скачанный из Azure AD, и указать его в поле **Identity Provider Certificate** (Сертификат поставщика удостоверений).

    d. В текстовое поле **Identity provider SSO url** (URL-адрес единого входа поставщика удостоверений) вставьте **URL-адрес входа**, скопированный на портале Azure.

    д. Просмотрите сведения о поставщике удостоверений в разделе **Aperture Info** (Сведения об Aperture) и скачайте сертификат из поля **Aperture Key** (Ключ Aperture).

    Е. Выберите команду **Сохранить**.

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

В этом разделе описано, как предоставить пользователю Britta Simon доступ к Palo Alto Networks - Aperture, чтобы этот пользователь мог использовать единый вход Azure.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **Palo Alto Networks — Aperture**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. Из списка приложений выберите **Palo Alto Networks - Aperture**.

    ![Ссылка на Palo Alto Networks - Aperture в списке "Приложения"](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-palo-alto-networks---aperture-test-user"></a>Создание тестового пользователя в Palo Alto Networks — Aperture

В этом разделе описано, как создать пользователя Britta Simon в приложении Palo Alto Networks - Aperture. Обратитесь к [группе поддержки клиентов Palo Alto Networks - Aperture](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) для добавления пользователей на платформу Palo Alto Networks - Aperture. Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="test-single-sign-on"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Palo Alto Networks — Aperture на Панели доступа, вы автоматически войдете в приложение Palo Alto Networks — Aperture, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

