---
title: Руководство по Интеграция Azure Active Directory с Silverback | Документы Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Silverback.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 32cfc96f-2137-49ff-818b-67feadcd73b7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 3c4eab02ed0c7c09fe9b5893bbaaf7cbe1c8028f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67090911"
---
# <a name="tutorial-azure-active-directory-integration-with-silverback"></a>Руководство по Интеграция Azure Active Directory с Silverback

В этом руководстве описано, как интегрировать Silverback с Azure Active Directory (Azure AD).
Интеграция Azure AD с Silverback обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к Silverback.
* Вы можете включить автоматический вход пользователей в Silverback (единый вход) с помощью учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Silverback, вам потребуется следующее:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* подписка Silverback с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Silverback поддерживает единый вход, инициированный **поставщиком услуг**.

## <a name="adding-silverback-from-the-gallery"></a>добавление Silverback из коллекции;

Чтобы настроить интеграцию Silverback с Azure AD, необходимо добавить Silverback из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Silverback из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Silverback**, выберите **Silverback** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![Silverback в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Silverback с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Silverback.

Чтобы настроить и проверить единый вход Azure AD в Silverback, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в Silverback](#configure-silverback-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя Silverback](#create-silverback-test-user)** требуется для того, чтобы в приложении Silverback существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в Silverback, сделайте следующее:

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Silverback** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    ![Сведения о домене и URL-адресах единого входа для приложения Silverback](common/sp-identifier-reply.png)

    a. В текстовое поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<YOURSILVERBACKURL>.com/ssp`.

    b. В поле **Идентификатор** введите URL-адрес в следующем формате: `<YOURSILVERBACKURL>.com`.

    c. В текстовом поле **URL-адрес ответа** введите URL-адрес в формате `https://<YOURSILVERBACKURL>.com/sts/authorize/login`.

    > [!NOTE]
    > Эти значения приведены для примера. Укажите вместо них фактические значения URL-адреса для входа, идентификатора и URL-адреса ответа. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов Silverback](mailto:helpdesk@matrix42.com). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

5. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** нажмите кнопку "Копировать", чтобы копировать **URL-адрес метаданных федерации приложений** и сохранить его на компьютере.

    ![Ссылка для скачивания сертификата](common/copy-metadataurl.png)

### <a name="configure-silverback-single-sign-on"></a>Настройка единого входа Silverback

1. Войдите в свой сервер Silverback от имени администратора в другом браузере.

2. Перейдите к разделу **Admin** > **Authentication Provider** (Администрирование > Поставщик проверки подлинности).

3. На странице **Authentication Provider Settings** (Параметры поставщика проверки подлинности) выполните следующие действия:

    ![Администратор](./media/silverback-tutorial/tutorial_silverback_admin.png)

    a.  Выберите **Import from URL** (Импорт из URL-адреса).

    b.  Вставьте скопированный URL-адрес метаданных и нажмите кнопку **ОК**.

    c.  После того как значения заполнятся автоматически, нажмите **ОК** для подтверждения.

    d.  Включите параметр **Show on Login Page** (Отображать на странице входа).

    д.  Включите параметр **Dynamic User Creation** (Динамическое создание пользователя), если хотите автоматически добавлять авторизованных пользователей Azure AD (необязательно).

    Е.  Задайте **Title** (название) для кнопки на портале самообслуживания.

    ж.  Загрузите **Icon** (значок), щелкнув кнопку **Choose File** (выбрать файл).

    h.  Выберите **Color** (цвет) фона для кнопки.

    i.  Выберите команду **Сохранить**.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    а. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon@yourcompanydomain.extension** .  
    Например BrittaSimon@contoso.com.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Silverback.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **Silverback**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. Из списка приложений выберите **Silverback**.

    ![Ссылка на Silverback в списке "Приложения"](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-silverback-test-user"></a>Создание тестового пользователя Silverback

Чтобы пользователи Azure AD могли выполнять вход в Silverback, они должны быть подготовлены в Silverback. В Silverback подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Войдите на сервер Silverback с правами администратора.

2. Перейдите к разделу **Users** (Пользователи) и выберите **add a new device user** (добавить нового пользователя устройства).

3. На странице **Basic** (Основные) выполните следующие действия:

    ![Пользователь](./media/silverback-tutorial/tutorial_silverback_user.png)

    a. В текстовое поле **Username** (Имя пользователя) введите имя пользователя, например **Britta Simon**.

    b. В текстовое поле **First Name** (Имя) введите имя пользователя, например **Britta**.

    c. В текстовое поле **Last Name** (Фамилия) введите фамилию пользователя, например **Simon**.

    d. В текстовое поле **Email address** (Адрес электронной почты) введите адрес электронной почты пользователя, например **Brittasimon@contoso.com** .

    д. Введите пароль в поле **Password** (Пароль).

    Е. В поле **Confirm Password** (Подтверждение пароля) повторно введите пароль и подтвердите.

    ж. Выберите команду **Сохранить**.

> [!NOTE]
> Если вы не хотите создавать каждого пользователя вручную, установите флажок **Dynamic User Creation** (Динамическое создание пользователя) в разделе **Admin** > **Authentication Provider** (Администрирование > Поставщик проверки подлинности).

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Silverback на Панели доступа, вы автоматически войдете в приложение Silverback, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что такое условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

