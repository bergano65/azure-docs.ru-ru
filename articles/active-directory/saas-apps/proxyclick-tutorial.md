---
title: Руководство по Интеграция Azure Active Directory с Proxyclick | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Proxyclick.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5c58a859-71c2-4542-ae92-e5f16a8e7f18
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: d37e3cc56b4a80fce9dcae6f87ff626867496007
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2019
ms.locfileid: "58578345"
---
# <a name="tutorial-azure-active-directory-integration-with-proxyclick"></a>Руководство по Интеграция Azure Active Directory с Proxyclick

В этом руководстве описано, как интегрировать Proxyclick с Azure Active Directory (Azure AD).
Интеграция Azure AD с приложением Proxyclick обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к Proxyclick.
* Вы можете включить автоматический вход пользователей (единый вход) в Proxyclick с помощью учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Proxyclick, вам потребуется следующее:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* подписка Proxyclick с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Proxyclick поддерживает единый вход, инициированный **поставщиком услуг** и **поставщиком удостоверений**.

## <a name="adding-proxyclick-from-the-gallery"></a>Добавление Proxyclick из коллекции

Чтобы настроить интеграцию Proxyclick с Azure AD, необходимо добавить Proxyclick из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Proxyclick из коллекции, сделайте следующее.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Proxyclick**, выберите **Proxyclick** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![Proxyclick в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Proxyclick с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Proxyclick.

Чтобы настроить и проверить единый вход Azure AD в Proxyclick, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в Proxyclick](#configure-proxyclick-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя Proxyclick](#create-proxyclick-test-user)** требуется для того, чтобы в Proxyclick существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в Proxyclick, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Proxyclick** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. Если вы хотите настроить приложение в режиме, инициируемом **поставщиком удостоверений**, в разделе **Базовая конфигурация SAML** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения Proxyclick](common/idp-intiated.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в формате `https://saml.proxyclick.com/init/<companyId>`.

    б) В текстовом поле **URL-адрес ответа** введите URL-адрес в формате `https://saml.proxyclick.com/consume/<companyId>`.

5. Чтобы настроить приложение для работы в режиме, инициируемом **поставщиком услуг**, щелкните **Задать дополнительные URL-адреса** и выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения Proxyclick](common/metadata-upload-additional-signon.png)

    В текстовом поле **URL-адрес входа** введите URL-адрес в формате `https://saml.proxyclick.com/init/<companyId>`.

    > [!NOTE]
    > Эти значения приведены для примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа, которые описываются далее в этом руководстве.

6. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Загрузить**, чтобы загрузить требуемый **сертификат (Base64)** из предложенных вариантов, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

7. Требуемый URL-адрес можно скопировать из раздела **Настройка Proxyclick**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    b. Идентификатор Azure AD

    c. URL-адрес выхода.

### <a name="configure-proxyclick-single-sign-on"></a>Настройка единого входа в Proxyclick

1. В другом окне веб-браузера войдите на свой корпоративный сайт Proxyclick в качестве администратора.

2. Выберите **Account & Settings** (Учетная запись и параметры).

    ![Конфигурация Proxyclick](./media/proxyclick-tutorial/configure1.png)

3. Прокрутите экран вниз до раздела **Интеграции** и выберите **SAML**.

    ![Конфигурация Proxyclick](./media/proxyclick-tutorial/configure2.png)

4. В разделе **SAML** выполните следующие действия.

    ![Конфигурация Proxyclick](./media/proxyclick-tutorial/configure3.png)

    a. Скопируйте значение **SAML Consumer URL** (URL-адрес объекта-получателя SAML) и вставьте его в текстовое поле **URL-адрес ответа** в разделе **Базовая конфигурация SAML** на портале Azure.

    b. Скопируйте значение **SAML SSO Redirect URL** (URL-адрес перенаправления для единого входа SAML) и вставьте его в текстовые поля **URL-адрес для входа** и **Идентификатор** в разделе **Базовая конфигурация SAML** на портале Azure.

    c. Для параметра **SAML Request Method** (Метод запроса SAML) выберите значение **HTTP Redirect** (Перенаправление HTTP).

    d. В текстовое поле **Issuer** (Издатель) вставьте значение **Идентификатор Azure AD**, скопированное на портале Azure.

    д. В текстовое поле **SAML 2.0 Endpoint URL** (URL-адрес конечной точки SAML 2.0) вставьте **URL-адрес входа**, скопированный на портале Azure.

    Е. Откройте в Блокноте скачанный с портала Azure файл сертификата, а затем скопируйте и вставьте его содержимое в текстовое поле **Certificate** (Сертификат).

    ж. Нажмите кнопку **Сохранить изменения**.

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

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Proxyclick.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **Proxyclick**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. Из списка приложений выберите **Proxyclick**.

    ![Ссылка на Proxyclick в списке "Приложения"](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-proxyclick-test-user"></a>Создание тестового пользователя Proxyclick

Чтобы пользователи Azure AD могли выполнять вход в Proxyclick, они должны быть подготовлены для Proxyclick. В случае с Proxyclick подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Выполните вход на корпоративный сайт Proxyclick в качестве администратора.

1. Щелкните **Colleagues** (Коллеги) на верхней панели навигации.

    ![Добавление сотрудника](./media/proxyclick-tutorial/user1.png)

1. Щелкните **Add Colleague** (Добавить коллегу).

    ![Добавление сотрудника](./media/proxyclick-tutorial/user2.png)

1. В разделе **Add a colleague** (Добавление коллеги) сделайте следующее.

    ![Добавление сотрудника](./media/proxyclick-tutorial/user3.png)

    a. В текстовом поле **Email** (Электронная почта) введите адрес электронной почты пользователя, например brittasimon@contoso.com.

    b. В текстовом поле **First Name** (Имя) введите имя пользователя, например Britta.

    c. В текстовом поле **Last Name** (Фамилия) введите фамилию пользователя, например Simon.

    d. Нажмите кнопку **Add User**(Добавить пользователя).

### <a name="test-single-sign-on"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Proxyclick на Панели доступа, вы автоматически войдете в приложение Proxyclick, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

