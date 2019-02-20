---
title: Руководство. Интеграция Azure Active Directory с Workspot Control | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Workspot Control.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3ea8e4e9-f61f-4f45-b635-b0e306eda3d1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8716d018756d1e6eadcd6ebeeaf4f67ad0bc4741
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56211161"
---
# <a name="tutorial-azure-active-directory-integration-with-workspot-control"></a>Руководство. Интеграция Azure Active Directory с Workspot Control

В этом руководстве описано, как интегрировать Workspot Control с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Workspot Control обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Workspot Control.
- Вы можете включить автоматический вход пользователей в Workspot Control (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD со Workspot Control, вам потребуется:

- подписка Azure AD;
- подписка Workspot Control с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Workspot Control из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-workspot-control-from-the-gallery"></a>Добавление Workspot Control из коллекции
Чтобы настроить интеграцию Workspot Control с Azure AD, необходимо добавить приложение Workspot Control из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Workspot Control из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![изображение](./media/workspotcontrol-tutorial/selectazuread.png)

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![изображение](./media/workspotcontrol-tutorial/a_select_app.png)
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![изображение](./media/workspotcontrol-tutorial/a_new_app.png)

4. В поле поиска введите **Workspot Control**, выберите **Workspot Control** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![изображение](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Workspot Control с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Workspot Control соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Workspot Control.

Чтобы настроить и проверить единый вход Azure AD в Workspot Control, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Workspot Control](#create-a-workspot-control-test-user)** требуется для того, чтобы в Workspot Control существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Workspot Control.

**Чтобы настроить единый вход Azure AD в Workspot Control, выполните следующие действия.**

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Workspot Control** выберите **Единый вход**.

    ![изображение](./media/workspotcontrol-tutorial/B1_B2_Select_SSO.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML**, чтобы включить единый вход.

    ![изображение](./media/workspotcontrol-tutorial/b1_b2_saml_sso.png)

3. На странице **Настройка единого входа с помощью SAML** нажмите кнопку **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![изображение](./media/workspotcontrol-tutorial/b1-domains_and_urlsedit.png)

4. Чтобы настроить приложение в режиме, инициируемом **IDP**, в разделе **Базовая конфигурация SAML** выполните указанные ниже действия.

    ![изображение](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в формате `https://<INSTANCENAME>-saml.workspot.com/saml/metadata`.

    б) В текстовом поле **URL-адрес ответа** введите URL-адрес в формате `https://<INSTANCENAME>-saml.workspot.com/saml/assertion`.

    c. Чтобы настроить приложение для работы в режиме, инициируемом **поставщиком услуг**, щелкните **Задать дополнительные URL-адреса** и выполните следующие действия.

     ![изображение](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_url1.png)

    В текстовом поле **URL-адрес входа** введите URL-адрес в формате `https://<INSTANCENAME>-saml.workspot.com/`.

    > [!NOTE]
    > Эти значения приведены для примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Чтобы получить их, обратитесь к [группе поддержки клиентов Workspot Control](mailto:support@workspot.com). 

5. На странице **Настройка единого входа с помощью SAML** откройте раздел **Сертификат подписи SAML** и щелкните ссылку **Скачать** рядом с пунктом **Сертификат (Base64)**, затем сохраните этот сертификат на компьютере.

    ![изображение](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_certficate.png) 

6. Требуемый URL-адрес можно скопировать из раздела **Настройка Workspot Control**.

    Обратите внимание, что URL-адрес может выглядеть следующим образом:

    a. URL-адрес входа.

    б) Идентификатор Azure AD.

    в) URL-адрес выхода.

    ![изображение](./media/workspotcontrol-tutorial/d1_samlsonfigure.png) 

7. В другом окне браузера войдите в приложение Workspot Control с правами администратора безопасности.

8. На панели инструментов в верхней части страницы щелкните  **Setup** (Настройка) и выберите  **SAML**.

    ![изображение](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_setup.png)

9. На странице **Security Assertion Markup Language Configuration** (Конфигурация SAML) выполните следующие действия.
 
    ![изображение](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_saml.png)

    a. В текстовое поле **Entity ID** (Идентификатор сущности) вставьте значение **Идентификатор Azure AD**, скопированное на портале Azure.   

    В текстовое поле **Signon Service URL** (URL-адрес службы входа) вставьте значение **URL-адрес входа**, скопированное на портале Azure.

    c. В текстовое поле **Logout Service URL** (URL-адрес службы выхода) вставьте значение **URL-адрес выхода**, скопированное на портале Azure. 

    4.3. Нажмите кнопку **Update File** (Обновить файл), чтобы передать сертификат в кодировке Base64, который был скачан с портала Azure, в сертификат X.509.

    д. Выберите команду **Сохранить**.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![изображение](./media/workspotcontrol-tutorial/d_users_and_groups.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![изображение](./media/workspotcontrol-tutorial/d_adduser.png)

3. В разделе свойств пользователя сделайте следующее:

    ![изображение](./media/workspotcontrol-tutorial/d_userproperties.png)

    a. В поле **Имя** введите **BrittaSimon**.
  
    б) В поле **Имя пользователя** введите **brittasimon@yourcompanydomain.extension**.  
    Например BrittaSimon@contoso.com.

    c. Выберите **Свойства**, установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-workspot-control-test-user"></a>Создание тестового пользователя Workspot Control

Чтобы пользователи Azure AD могли выполнять вход в Workspot Control, их следует подготовить в Workspot Control. В Workspot Control подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Войдите в Workspot Control с правами администратора безопасности.

2. На панели инструментов в верхней части страницы щелкните  **Users** (Пользователи) и выберите  **Add User** (Добавить пользователя).

    ![изображение](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_adduser.png)

3. На странице **Add a New User** (Добавление нового пользователя) выполните следующие действия.

    ![изображение](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addnewuser.png)

    a. В текстовое поле **First Name** (Имя) введите имя пользователя, например **Britta**.

    б) В текстовое поле **Last Name** (Фамилия) введите фамилию пользователя, например **Simon**.

    c. В текстовое поле **Email** (Адрес электронной почты) введите адрес электронной почты пользователя, например **Brittasimon@contoso.com**.

    4.3. Выберите соответствующую роль пользователя из раскрывающегося списка **Role** (Роль).

    д. Выберите соответствующую группу пользователя из раскрывающегося списка **Group** (Группа).

    Е. Нажмите кнопку **Add User**(Добавить пользователя).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Workspot Control.

1. На портале Azure перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![изображение](./media/workspotcontrol-tutorial/d_all_applications.png)

2. Из списка приложений выберите **Workspot Control**.

    ![изображение](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_app.png)

3. В меню слева выберите **Пользователи и группы**.

    ![изображение](./media/workspotcontrol-tutorial/d_leftpaneusers.png)

4. Нажмите кнопку **Добавить**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![изображение](./media/workspotcontrol-tutorial/d_assign_user.png)

4. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

5. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "Workspot Control" на панели доступа, вы автоматически войдете в приложение Workspot Control.
Дополнительные сведения о панели доступа см. в статье с [общими сведениями о панели доступа](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
