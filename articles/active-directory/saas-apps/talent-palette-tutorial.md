---
title: Руководство по интеграции Azure Active Directory с Talent Palette | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Talent Palette.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 839dbf54-b636-477b-9cf8-157374c78e7a
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/30/2018
ms.author: jeedes
ms.openlocfilehash: 7362b3279afc365db2a303de1b450c4e35e48874
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52883973"
---
# <a name="tutorial-azure-active-directory-integration-with-talent-palette"></a>Руководство по интеграции Azure Active Directory с Talent Palette

В этом руководстве описано, как интегрировать Talent Palette с Azure Active Directory (Azure AD).
Интеграция Talent Palette с Azure AD обеспечивает следующие преимущества.

* C помощью Azure AD можно контролировать доступ к Talent Palette.
* Можно включить автоматический вход пользователей в Talent Palette (единый вход) с помощью учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Talent Palette, вам потребуется следующее:

* подписка Azure AD; Если у вас нет среды Azure AD, можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).
* Подписка с поддержкой единого входа в Talent Palette

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства проводится настройка и проверка единого входа Azure AD в тестовой среде.

* Folloze поддерживает единый вход, инициированный **IDP**
* Folloze поддерживает подготовку пользователей **JIT**

## <a name="adding-talent-palette-from-the-gallery"></a>Добавление Talent Palette из коллекции

Чтобы настроить интеграцию Talent Palette с Azure AD, необходимо добавить Talent Palette из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Talent Palette из коллекции, выполните описанные ниже шаги.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](common/select_azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise_applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"](common/add_new_app.png)

4. В поле поиска введите **Talent Palette**, на панели результатов выберите **Talent Palette** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![Talent Palette в списке результатов](common/search_new_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Talent Palette с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Talent Palette.

Чтобы настроить и проверить единый вход Azure AD в Talent Palette, необходимо выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в Talent Palette](#configure-talent-palette-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Создание тестового пользователя Talent Palette](#create-talent-palette-test-user)** требуется для того, чтобы в Talent Palette существовал соответствующий пользователь, связанный с пользователем Britta Simon в Azure AD.
5. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в Talent Palette, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Talent Palette** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select_sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select_saml_option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit_urls.png)

4. Если вы хотите настроить приложение в режиме, инициируемом **IDP**, в разделе **Базовая конфигурация SAML** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа в Talent Palette](common/both_replyurl.png)

    В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://talent-p.net/saml/acs/<tenantID>`.

5. Чтобы настроить приложение для работы в режиме, инициируемом **поставщиком услуг**, щелкните **Задать дополнительные URL-адреса** и выполните следующее действие:

    ![Сведения о домене и URL-адресах единого входа в Talent Palette](common/both_signonurl.png)
   
    В текстовом поле **URL-адрес входа** введите URL-адрес в следующем формате: `https://talent-p.net/saml/sso/<tenantID>`.

    > [!NOTE]
    > Эти значения приведены в качестве примера. Измените их на фактические значения URL-адреса ответа и URL-адреса входа. Для получения этих значений обратитесь в [службу поддержки клиентов Talent Palette](mailto:talent-support@pa-consul.co.jp). Можно также обратиться к шаблонам, указанным в разделе **Базовая конфигурация SAML** на портале Azure.

6. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Загрузить**, чтобы загрузить требуемый **сертификат (необработанный)** из предложенных вариантов и сохранить его на компьютере.

    ![Ссылка для скачивания сертификата](common/certificateraw.png)

7. Скопируйте соответствующий вашим требованиям URL-адрес из раздела **Настройка Talent Palette**.

    ![Копирование URL-адресов настройки](common/copy_configuration_urls.png)

    a. URL-адрес входа.

    b. Идентификатор Azure AD.

    c. URL-адрес выхода

### <a name="configure-talent-palette-single-sign-on"></a>Настройка единого входа в Talent Palette

Чтобы настроить единый вход на стороне **Talent Palette**, нужно отправить скачанный **сертификат (необработанный)** и соответствующие URL-адреса из портала Azure в [службу поддержки Talent Palette](mailto:talent-support@pa-consul.co.jp). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD 

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new_user.png)

3. В разделе свойств пользователя сделайте следующее.

    ![Диалоговое окно "Пользователь"](common/user_properties.png)

    a. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon@yourcompanydomain.extension**.  
    Например, BrittaSimon@contoso.com

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Talent Palette.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем **Talent Palette**.

    ![Колонка "Корпоративные приложения"](common/enterprise_applications.png)

2. В списке приложений введите и выберите **Talent Palette**.

    ![Ссылка на Talent Palette в списке приложений](common/all_applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users_groups_blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add_assign_user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-talent-palette-test-user"></a>Создание тестового пользователя Talent Palette

В этом разделе описано, как создать пользователя Britta Simon в Talent Palette. Чтобы добавить пользователей на платформу Talent Palette, обратитесь в  [службу поддержки Talent Palette](mailto:talent-support@pa-consul.co.jp). Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

При нажатии на плитку Talent Palette на панели доступа вы автоматически войдете в Talent Palette, для которой настроили единый вход. Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)