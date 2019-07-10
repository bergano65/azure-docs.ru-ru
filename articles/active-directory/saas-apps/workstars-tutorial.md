---
title: Руководство по Интеграция Azure Active Directory с Workstars | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в приложении Workstars.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 51a4a4e4-ff60-4971-b3f8-a0367b70d220
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: c13c6ab180a172c034d25ac84781f5d3f83ae186
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67086640"
---
# <a name="tutorial-azure-active-directory-integration-with-workstars"></a>Руководство по Интеграция Azure Active Directory с Workstars

В этом учебнике описано, как интегрировать приложение Workstars с Azure Active Directory (Azure AD).
Интеграция Azure AD с приложением Workstars обеспечивает следующие преимущества:

* с помощью Azure AD вы можете контролировать доступ к Workstars;
* Вы можете включить автоматический вход пользователей в Workstars (единый вход) с помощью учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Workstars, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/));
* подписка Workstars с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Workstars поддерживает единый вход, инициированный **поставщиком удостоверений**.

## <a name="adding-workstars-from-the-gallery"></a>Добавление Workstars из коллекции

Чтобы настроить интеграцию Workstars с Azure AD, необходимо добавить Workstars из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Workstars из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Workstars**, выберите **Workstars** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![Workstars в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Workstars с использованием тестового пользователя **Britta Simon**.
Чтобы единый вход функционировал, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Workstars.

Чтобы настроить и проверить единый вход Azure AD в Workstars, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в Workstars](#configure-workstars-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя Workstars](#create-workstars-test-user)** требуется для того, чтобы в Workstars существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в Workstars, выполните следующие действия:

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Workstars** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. На странице **Настройка единого входа с помощью SAML** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа приложения Workstars](common/idp-intiated.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес: `https://workstars.com`

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в формате `https://<subdomain>.workstars.com/saml/login_check`.

    > [!NOTE]
    > Это значение приведено для примера. Вместо него нужно указать фактический URL-адрес ответа. Чтобы получить это значение, обратитесь в [группу поддержки клиентов Workstars](https://support.workstars.com/). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

5. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Загрузить**, чтобы загрузить требуемый **сертификат (Base64)** из предложенных вариантов, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

6. Требуемый URL-адрес можно скопировать из раздела **настройки Workstars**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    b. Идентификатор Azure AD

    c. URL-адрес выхода.

### <a name="configure-workstars-single-sign-on"></a>Настройка единого входа в Workstars

1. В другом окне браузера войдите на корпоративный сайт Workstars с правами администратора.

2. На главной панели инструментов щелкните **Settings**(Настройки).

    ![Workstars, раздел "Настройки"](./media/workstars-tutorial/tutorial_workstars_sett.png)

3. Откройте элементы **Sign On** (Вход)  > **Settings** (Настройки).

    ![Workstars, раздел "Вход"](./media/workstars-tutorial/tutorial_workstars_signon.png)

    ![Workstars, раздел "Настройки"](./media/workstars-tutorial/tutorial_workstars_settings.png)

4. На странице **Single Sign On (SAML) - Settings** (Параметры единого входа (SAML)) выполните следующие действия.
    
    ![Настройки SAML в Workstars](./media/workstars-tutorial/tutorial_workstars_saml.png)

    a. В текстовом поле **Identity Provider Name** (Имя поставщика удостоверений) введите **Office 365**.

    b. В текстовое поле **Identity Provider Entity ID** (Идентификатор сущности поставщика удостоверений) вставьте значение **идентификатора Azure AD**, скопированное на портале Azure.

    c. Скопируйте в блокнот содержимое скачанного сертификата, а затем вставьте его в текстовое поле **x509 Certificate** (Сертификат X.509). 

    d. В текстовое поле **SAML SSO URL** (URL-адрес единого входа SAML) вставьте **URL-адрес входа**, скопированный на портале Azure.
    
    д. В текстовое поле **URL-адрес удаленного выхода** вставьте **URL-адрес выхода**, скопированный на портале Azure. 

    Е. Для параметра **Name ID** (Идентификатор имени) выберите значение **Email (Default)** (Адрес электронной почты (по умолчанию)).

    ж. Щелкните **Confirm** (Подтвердить).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD 

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    а. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите brittasimon@yourcompanydomain.extension. Например BrittaSimon@contoso.com.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Workstars.

1. На портале Azure последовательно выберите **Корпоративные приложения**, **Все приложения** и **Workstars**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Workstars**.

    ![Ссылка на Workstars в списке "Приложения"](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-workstars-test-user"></a>Создание тестового пользователя Workstars

В этом разделе описано, как создать пользователя Britta Simon в приложении Workstars. Обратитесь в [службу поддержки Workstars](https://support.workstars.com), чтобы добавить пользователей на платформу Workstars.

### <a name="test-single-sign-on"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Workstars на панели доступа, вы автоматически войдете в приложение Workstars, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что такое условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

