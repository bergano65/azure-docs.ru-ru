---
title: Руководство по Интеграция Azure Active Directory с MyWorkDrive | Документация Майкрософт
description: Сведения о том, как настроить единый вход между Azure Active Directory и MyWorkDrive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4d049778-3c7b-46c0-92a4-f2633a32334b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: jeedes
ms.openlocfilehash: d16aa8442f71845e7b46377c6c290212f9c400a3
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59280508"
---
# <a name="tutorial-azure-active-directory-integration-with-myworkdrive"></a>Руководство по Интеграция Azure Active Directory с MyWorkDrive

В этом руководстве описано, как интегрировать MyWorkDrive с Azure Active Directory (Azure AD).
Интеграция Azure AD с приложением MyWorkDrive обеспечивает следующие преимущества.

* C помощью Azure AD вы можете контролировать доступ к MyWorkDrive.
* Вы можете включить автоматический вход пользователей в MyWorkDrive (единый вход) с помощью учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с MyWorkDrive, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* подписка MyWorkDrive с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* MyWorkDrive поддерживает единый вход, инициируемый **поставщиком услуг** и **поставщиком удостоверений**.

## <a name="adding-myworkdrive-from-the-gallery"></a>Добавление MyWorkDrive из коллекции

Чтобы настроить интеграцию MyWorkDrive с Azure AD, вам потребуется добавить MyWorkDrive из коллекции в список управляемых приложений SaaS.

**Чтобы добавить MyWorkDrive из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **MyWorkDrive**, выберите **MyWorkDrive** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![MyWorkDrive в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в MyWorkDrive с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в MyWorkDrive.

Чтобы настроить и проверить единый вход Azure AD в MyWorkDrive, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в MyWorkDrive](#configure-myworkdrive-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя приложения MyWorkDrive](#create-myworkdrive-test-user)** требуется для того, чтобы в MyWorkDrive существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в MyWorkDrive, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **MyWorkDrive** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. Если вы хотите настроить приложение в режиме, инициируемом **поставщиком удостоверений**, в разделе **Базовая конфигурация SAML** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа приложения MyWorkDrive](common/both-replyurl.png)

    В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<SERVER.DOMAIN.COM>/SAML/AssertionConsumerService.aspx`.

5. Чтобы настроить приложение для работы в режиме, инициируемом **поставщиком услуг**, щелкните **Задать дополнительные URL-адреса** и выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа приложения MyWorkDrive](common/both-signonurl.png)

    В текстовом поле **URL-адрес входа** введите URL-адрес в формате `https://<SERVER.DOMAIN.COM>/Account/Login-saml`.

    > [!NOTE]
    > Эти значения приведены для примера. Измените их на фактические значения URL-адреса ответа и URL-адреса входа. Введите имя узла корпоративного сервера MyWorkDrive, например так:
    > 
    > URL-адрес ответа: `https://yourserver.yourdomain.com/SAML/AssertionConsumerService.aspx`
    > 
    > URL-адрес для входа:`https://yourserver.yourdomain.com/Account/Login-saml`
    > 
    > Если вы не знаете, как указать в этих параметрах имя узла и SSL-сертификат, обратитесь к [группе поддержки MyWorkDrive](mailto:support@myworkdrive.com).

6. На странице **Настройка единого входа с помощью SAML**, в разделе **Сертификат подписи SAML** нажмите кнопку "Копировать", чтобы копировать **URL-адрес метаданных федерации приложений** и сохранить его на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

### <a name="configure-myworkdrive-single-sign-on"></a>Настройка единого входа в MyWorkDrive

1. В другом окне браузера войдите в MyWorkDrive с правами администратора безопасности.

2. На сервере MyWorkDrive на панели администрирования щелкните **ENTERPRISE** (Корпоративное приложение) и выполните следующие действия:

    ![Администратор](./media/myworkdrive-tutorial/tutorial_myworkdrive_admin.png)

    a. Включите **SAML/ADFS SSO** (единый вход в SAML и распределенную файловую систему Azure).

    b. Выберите **SAML – Azure AD**

    c. В текстовое поле **Azure App Federation Metadata Url** (URL-адрес метаданных федерации приложения Azure) вставьте **URL-адрес метаданных федерации приложения**, скопированный на портале Azure.

    d. Нажмите кнопку **Сохранить**

    >[!NOTE]
    >Дополнительные сведения см. в справочной статье об [интеграции MyWorkDrive с Azure AD](https://www.myworkdrive.com/support/saml-single-sign-on-azure-ad/).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD 

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    а. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon\@домен_вашей_компании.доменная_зона**.  
    Например BrittaSimon@contoso.com.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к MyWorkDrive.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **MyWorkDrive**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **MyWorkDrive**.

    ![Ссылка на MyWorkDrive в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-myworkdrive-test-user"></a>Создание тестового пользователя MyWorkDrive

В этом разделе описано, как создать пользователя Britta Simon в приложении MyWorkDrive. Обратитесь в [службу поддержки MyWorkDrive](mailto:support@myworkdrive.com), чтобы добавить пользователей на платформу MyWorkDrive. Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="test-single-sign-on"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "MyWorkDrive" на панели доступа, вы автоматически войдете в приложение MyWorkDrive, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

