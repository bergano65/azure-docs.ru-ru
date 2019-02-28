---
title: Руководство. Интеграция Azure Active Directory с Cherwell | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Cherwell.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: ad891f99-179e-4487-834d-35f3bc01c1ec
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c3cb855977f741da13f21a30aca6eb3361f48f8
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56878733"
---
# <a name="tutorial-azure-active-directory-integration-with-cherwell"></a>Руководство. Интеграция Azure Active Directory с Cherwell

В этом руководстве описано, как интегрировать Cherwell с Azure Active Directory (Azure AD).
Интеграция Azure AD с приложением Cherwell обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к Cherwell.
* Вы можете включить автоматический вход пользователей в Cherwell (единый вход) с помощью учетных записей Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Cherwell, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* Подписка Cherwell с поддержкой единого входа

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Cherwell поддерживает единый вход инициированного **пакета обновления**.

## <a name="adding-cherwell-from-the-gallery"></a>Добавление Cherwell из коллекции

Чтобы настроить интеграцию Cherwell с Azure AD, необходимо добавить Cherwell из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Cherwell из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Cherwell**, выберите **Cherwell** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![Cherwell в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Cherwell с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Cherwell.

Чтобы настроить и проверить единый вход Azure AD в Cherwell, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в Cherwell](#configure-cherwell-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя Cherwell](#create-cherwell-test-user)** нужно для того, чтобы в Cherwell также существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в Cherwell, выполните следующие действия:

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Cherwell** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    ![Сведения о домене и URL-адресах единого входа приложения Cherwell](common/sp-signonurl.png)

    В текстовом поле **URL-адрес входа** введите URL-адрес в формате `https://<companyname>.cherwellondemand.com/cherwellclient`.

    > [!NOTE]
    > Это значение приведено для примера. Вместо него необходимо указать фактический URL-адрес входа. Чтобы получить это значение, обратитесь к [группе поддержки Cherwell](https://csm.cherwell.com/contact). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

5. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Загрузить**, чтобы загрузить требуемый **сертификат (Base64)** из предложенных вариантов, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

6. Требуемый URL-адрес можно скопировать из раздела **Настройка Cherwell**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    б) Идентификатор Azure AD.

    в) URL-адрес выхода.

### <a name="configure-cherwell-single-sign-on"></a>Настройка единого входа в Cherwell

Чтобы настроить единый вход на стороне **Cherwell**, нужно отправить скачанный **Сертификат (Base64)** и соответствующие URL-адреса, скопированные на портале Azure, [группе поддержки Cherwell](https://csm.cherwell.com/contact). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

> [!NOTE]
> Настройка единого входа должна выполняться службой поддержки Cherwell. Как только единый вход для вашей подписки будет включен, вы получите уведомление.

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

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Cherwell.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **Cherwell**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. Из списка приложений выберите **Cherwell**.

    ![Ссылка на Cherwell в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-cherwell-test-user"></a>Создание тестового пользователя Cherwell

Чтобы пользователи Azure AD могли выполнить вход в Cherwell, они должны быть подготовлены в Cherwell. В случае Cherwell учетные записи пользователей должны быть созданы [группой поддержки Cherwell](https://csm.cherwell.com/contact).

> [!NOTE]
> Вы можете использовать любые другие инструменты создания учетных записей пользователя Cherwell или API, предоставляемые Cherwell для подготовки учетных записей пользователя Azure Active Directory.

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку "Cherwell" на панели доступа, вы автоматически войдете в приложение Cherwell, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
