---
title: Руководство по интеграции Azure Active Directory с MyWorkDrive | Документация Майкрософт
description: Сведения о том, как настроить единый вход между Azure Active Directory и MyWorkDrive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4d049778-3c7b-46c0-92a4-f2633a32334b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: jeedes
ms.openlocfilehash: 7310d300c68399c31d9580f070602aa3adbc75e3
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50094062"
---
# <a name="tutorial-azure-active-directory-integration-with-myworkdrive"></a>Руководство по интеграции Azure Active Directory с MyWorkDrive

В этом руководстве описано, как интегрировать MyWorkDrive с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением MyWorkDrive обеспечивает следующие преимущества.

- C помощью Azure AD вы можете контролировать доступ к MyWorkDrive.
- Вы можете включить автоматический вход пользователей в MyWorkDrive (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с MyWorkDrive, вам потребуется:

- подписка Azure AD;
- подписка с поддержкой единого входа MyWorkDrive.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление MyWorkDrive из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-myworkdrive-from-the-gallery"></a>Добавление MyWorkDrive из коллекции
Чтобы настроить интеграцию MyWorkDrive с Azure AD, вам потребуется добавить MyWorkDrive из коллекции в список управляемых приложений SaaS.

**Чтобы добавить MyWorkDrive из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![изображение](./media/myworkdrive-tutorial/selectazuread.png)

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![изображение](./media/myworkdrive-tutorial/a_select_app.png)
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![изображение](./media/myworkdrive-tutorial/a_new_app.png)

4. В поле поиска введите **MyWorkDrive**, выберите **MyWorkDrive** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![изображение](./media/myworkdrive-tutorial/tutorial_myworkdrive_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в MyWorkDrive с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в MyWorkDrive соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в MyWorkDrive.

Чтобы настроить и проверить единый вход Azure AD в MyWorkDrive, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя приложения MyWorkDrive](#create-a-myworkdrive-test-user)** требуется для того, чтобы в MyWorkDrive существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении MyWorkDrive.

**Чтобы настроить единый вход Azure AD в MyWorkDrive, выполните следующие действия:**

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **MyWorkDrive** щелкните **Единый вход**.

    ![изображение](./media/myworkdrive-tutorial/B1_B2_Select_SSO.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML**, чтобы включить единый вход.

    ![изображение](./media/myworkdrive-tutorial/b1_b2_saml_sso.png)

3. На странице **Настройка единого входа с помощью SAML** нажмите кнопку **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![изображение](./media/myworkdrive-tutorial/b1-domains_and_urlsedit.png)

4. Если вы хотите настроить приложение в режиме, инициируемом **поставщиком удостоверений**, в разделе **Базовая конфигурация SAML** выполните следующие действия.

    ![изображение](./media/myworkdrive-tutorial/tutorial_myworkdrive_url.png)

    В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<SERVER.DOMAIN.COM>/SAML/AssertionConsumerService.aspx`.

5. Чтобы настроить приложение для работы в режиме, инициируемом **поставщиком услуг**, щелкните **Задать дополнительные URL-адреса** и выполните следующее.

    ![изображение](./media/myworkdrive-tutorial/tutorial_myworkdrive_url1.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<SERVER.DOMAIN.COM>/Account/Login-saml` 

    > [!NOTE]
    > Эти значения приведены в качестве примера. Измените их на фактические значения URL-адреса ответа и URL-адреса входа.  Введите имя узла корпоративного сервера MyWorkDrive, например так:
    > 
    > URL-адрес ответа: `https://yourserver.yourdomain.com/SAML/AssertionConsumerService.aspx`.
    > 
    > URL-адрес для входа: `https://yourserver.yourdomain.com/Account/Login-saml`.
    > 
    > Если вы не знаете, как указать в этих параметрах имя узла и SSL-сертификат, обратитесь к группе поддержки клиентов MyWorkDrive.

6. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **значок** копирования, чтобы копировать **URL-адрес метаданных федерации приложений**, а затем щелкните **Скачать**, чтобы получить **Сертификат в формате Base64** и сохранить его на локальном компьютере.

    ![изображение](./media/myworkdrive-tutorial/tutorial_myworkdrive_certficate.png) 

7. Требуемый URL-адрес можно скопировать из раздела **Set up MyWorkDrive** (Настройка MyWorkDrive).

    Обратите внимание, что URL-адрес может выглядеть следующим образом:

    a. URL-адрес входа.

    b. Идентификатор Azure AD.

    c. URL-адрес выхода

    ![изображение](./media/myworkdrive-tutorial/d1_samlsonfigure.png) 

8. Чтобы настроить единый вход на стороне MyWorkDrive, получите **сертификат в формате Base64, URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** и настройте эти параметры вручную на сервере MyWorkDrive или скопируйте в Azure **URL-адрес метаданных федерации приложения** и вставьте его в панель администрирования сервера MyWorkDrive на экране настройки SAML AAD. Дополнительные сведения можно получить в [службе поддержки MyWorkDrive](mailto:support@myworkdrive.com).

    
### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![изображение](./media/myworkdrive-tutorial/d_users_and_groups.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![изображение](./media/myworkdrive-tutorial/d_adduser.png)

3. В разделе свойств пользователя сделайте следующее.

    ![изображение](./media/myworkdrive-tutorial/d_userproperties.png)

    a. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon@yourcompanydomain.extension**.  
    Например, BrittaSimon@contoso.com

    c. Выберите **Свойства**, установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-myworkdrive-test-user"></a>Создание тестового пользователя MyWorkDrive

В этом разделе описано, как создать пользователя Britta Simon в приложении MyWorkDrive. Обратитесь в  [службу поддержки MyWorkDrive](mailto:support@myworkdrive.com), чтобы добавить пользователей на платформу MyWorkDrive. Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к MyWorkDrive.

1. На портале Azure перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![изображение](./media/myworkdrive-tutorial/d_all_applications.png)

2. В списке приложений выберите **MyWorkDrive**.

    ![изображение](./media/myworkdrive-tutorial/tutorial_myworkdrive_app.png)

3. В меню слева выберите **Пользователи и группы**.

    ![изображение](./media/myworkdrive-tutorial/d_leftpaneusers.png)

4. Нажмите кнопку **Добавить**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![изображение](./media/myworkdrive-tutorial/d_assign_user.png)

4. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

5. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент MyWorkDrive на панели доступа, вы автоматически войдете в приложение MyWorkDrive.
Дополнительные сведения о панели доступа см. в статье с [общими сведениями о панели доступа](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
