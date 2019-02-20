---
title: Руководство. Интеграция Azure Active Directory с Adaptive Insights | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Adaptive Insights.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e334795a63ff298ff8f0695340f35fdff1ad03e0
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56166527"
---
# <a name="tutorial-azure-active-directory-integration-with-adaptive-insights"></a>Руководство. Интеграция Azure Active Directory с Adaptive Insights

В этом руководстве описано, как интегрировать Adaptive Insights с Azure Active Directory (Azure AD).

Интеграция приложения Adaptive Insights с Azure AD обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Adaptive Insights.
- Вы можете обеспечить автоматический вход пользователей в Adaptive Insights (единый вход) под учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Adaptive Insights, вам потребуется следующее:

- подписка Azure AD;
- подписка Adaptive Insights с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Adaptive Insights из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-adaptive-insights-from-the-gallery"></a>Добавление Adaptive Insights из коллекции
Чтобы настроить интеграцию приложения Adaptive Insights с Azure AD, вам нужно добавить это приложение из коллекции в список управляемых приложений SaaS.

**Добавление приложения Adaptive Insights из коллекции**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![изображение](./media/adaptivesuite-tutorial/selectazuread.png)

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![изображение](./media/adaptivesuite-tutorial/a_select_app.png)
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![изображение](./media/adaptivesuite-tutorial/a_new_app.png)

4. В поле поиска введите **Adaptive Insights**, выберите **Adaptive Insights** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![изображение](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Adaptive Insights с использованием тестового пользователя Britta Simon.

Для работы единого входа службе Azure AD нужно знать, какой пользователь в Adaptive Insights соответствует пользователю в Azure AD. Иными словами, нужно установить связь между пользователем Azure AD и соответствующим пользователем в Adaptive Insights.

Чтобы настроить и проверить единый вход Azure AD в Adaptive Insights, выполните следующие действия.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Adaptive Insights](#create-an-adaptive-insights-test-user)** нужно для того, чтобы в Adaptive Insights также существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе мы включим на портале Azure единый вход Azure AD и настроим его в приложении Adaptive Insights.

**Настройка единого входа Azure AD в Adaptive Insights**

1. Откройте [портал Azure](https://portal.azure.com/) и на странице интеграции с приложением **Adaptive Insights** выберите **Единый вход**.

    ![изображение](./media/adaptivesuite-tutorial/B1_B2_Select_SSO.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML**, чтобы включить единый вход.

    ![изображение](./media/adaptivesuite-tutorial/b1_b2_saml_sso.png)

3. На странице **Настройка единого входа с помощью SAML** нажмите кнопку **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![изображение](./media/adaptivesuite-tutorial/b1-domains_and_urlsedit.png)

4. Чтобы настроить приложение в режиме, инициируемом **IDP**, в разделе **Базовая конфигурация SAML** выполните указанные ниже действия.

    ![изображение](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_url.png)

    a. В текстовое поле **Identifier (Entity ID)** (Идентификатор (ИД сущности)) введите URL-адрес в следующем формате: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    б) В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`.

    >[!NOTE]
    > Идентификатор сущности и URL-адрес ответа можно получить на странице **Параметры единого входа SAML** Adaptive Insights.
 
5. На странице **Настройка единого входа с помощью SAML** откройте раздел **Сертификат подписи SAML** и щелкните ссылку **Скачать** рядом с пунктом **Сертификат (Base64)**, затем сохраните этот сертификат на компьютере.

    ![изображение](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_certficate.png) 

6. В разделе **Настройка Adaptive Insights** скопируйте соответствующий требуемый URL-адрес.

    Обратите внимание, что URL-адрес может выглядеть следующим образом:

    a. URL-адрес входа.

    б) Идентификатор Azure AD.

    в) URL-адрес выхода.

    ![изображение](./media/adaptivesuite-tutorial/d1_samlsonfigure.png) 

7. В другом окне веб-браузера войдите на корпоративный сайт Adaptive Insights с правами администратора.

8. Откройте страницу **Администратор**.

    ![Администратор](./media/adaptivesuite-tutorial/IC805644.png "Администратор")

9. В разделе **Users and Roles** (Пользователи и роли) щелкните **Manage SAML SSO Settings** (Управление параметрами единого входа SAML).

    ![Управление параметрами единого входа SAML](./media/adaptivesuite-tutorial/IC805645.png "Управление параметрами единого входа SAML")

10. На странице **SAML SSO Settings** (Параметры единого входа SAML) выполните следующие действия.

    ![Параметры единого входа SAML](./media/adaptivesuite-tutorial/IC805646.png "Параметры единого входа SAML")

    a. Введите имя конфигурации в текстовое поле **Identity provider name** (Имя поставщика удостоверений).

    б) Вставьте значение поля **Идентификатор Azure AD**, скопированное на портале Azure, в текстовое поле **Идентификатор сущности поставщика удостоверений**.

    c. Вставьте значение поля **URL-адрес входа**, скопированное на портале Azure, в текстовое поле **URL-адрес единого входа поставщика удостоверений**.

    4.3. Вставьте значение поля **URL-адрес выхода**, скопированное на портале Azure, в текстовое поле **Пользовательский URL-адрес для выхода**.

    д. Чтобы отправить загруженный сертификат, нажмите кнопку **Выбрать файл**.

    f. Выберите следующие параметры:

    * Для параметра **SAML user id** (Идентификатор пользователя SAML) выберите значение **User’s Adaptive Insights user name** (Имя пользователя Adaptive Insights).

    * Для параметра **SAML user id location** (Расположение идентификатора пользователя SAML) выберите значение **User id in NameID of Subject** (Идентификатор пользователя из NameID в Subject).

    * Для параметра **SAML NameID format** (Формат NameID SAML) выберите значение **Адрес электронной почты**.

    * Для параметра **Включить SAML** выберите значение **Allow SAML SSO and direct Adaptive Insights login** (Разрешить единый вход SAML и прямой вход Adaptive Insights).

    ж. Скопируйте значение **Adaptive Insights SSO URL** (URL-адрес единого входа Adaptive Insights) и вставьте его в текстовые поля **Идентификатор сущности** и **URL-адрес ответа** в разделе **Домены и URL-адреса приложения Adaptive Insights** на портале Azure.

    h. Выберите команду **Сохранить**.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![изображение](./media/adaptivesuite-tutorial/d_users_and_groups.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![изображение](./media/adaptivesuite-tutorial/d_adduser.png)

3. В разделе свойств пользователя сделайте следующее:

    ![изображение](./media/adaptivesuite-tutorial/d_userproperties.png)

    a. В поле **Имя** введите **BrittaSimon**.
  
    б) В поле **Имя пользователя** введите **brittasimon@yourcompanydomain.extension**.  
    Например BrittaSimon@contoso.com.

    c. Выберите **Свойства**, установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.
 
### <a name="create-an-adaptive-insights-test-user"></a>Создание тестового пользователя Adaptive Insights

Чтобы пользователи Azure AD могли выполнять вход в Adaptive Insights, их следует подготовить в Adaptive Insights. В случае Adaptive Insights подготовка выполняется вручную.

**Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.** 

1. Выполните вход на корпоративный веб-сайт **Adaptive Insights** в качестве администратора.
2. Откройте страницу **Администратор**.

   ![Администратор](./media/adaptivesuite-tutorial/IC805644.png "Администратор")

3. В разделе **Users and Roles** (Пользователи и роли) щелкните **Добавить пользователя**.

   ![Добавление пользователя](./media/adaptivesuite-tutorial/IC805648.png "Добавление пользователя")
   
4. В разделе **New User** (Новый пользователь) выполните следующие действия.

   ![Отправка](./media/adaptivesuite-tutorial/IC805649.png "Отправка")

   a. Введите в текстовые поля **Name** (Имя), **Login** (Имя для входа), **Email** (Адрес электронной почты) и **Password** (Пароль) соответствующие данные действующего пользователя Azure Active Directory, для которого выполняется подготовка.

   б) Выберите **Роль**.

   c. Нажмите кнопку **Submit**(Отправить).

>[!NOTE]
>Вы можете использовать любые другие средства создания учетной записи пользователя Adaptive Insights или API, предоставляемые Adaptive Insights для подготовки учетных записей пользователя AAD.
>

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе вы разрешите пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Adaptive Insights.

1. На портале Azure перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![изображение](./media/adaptivesuite-tutorial/d_all_applications.png)

2. Из списка приложений выберите **Adaptive Insights**.

    ![изображение](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_app.png)

3. В меню слева выберите **Пользователи и группы**.

    ![изображение](./media/adaptivesuite-tutorial/d_leftpaneusers.png)

4. Нажмите кнопку **Добавить**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![изображение](./media/adaptivesuite-tutorial/d_assign_user.png)

4. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

5. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "Adaptive Insights" на панели доступа, вы автоматически войдете в приложение Adaptive Insights.
Дополнительные сведения о панели доступа см. в статье с [общими сведениями о панели доступа](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
