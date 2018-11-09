---
title: Руководство по интеграции Azure Active Directory с LINE WORKS | Документация Майкрософт
description: Сведения о настройке единого входа Azure Active Directory в LINE WORKS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 725f32fd-d0ad-49c7-b137-1cc246bf85d7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: jeedes
ms.openlocfilehash: 0437bd46537d8b7024618706aba12660abca5512
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50414698"
---
# <a name="tutorial-azure-active-directory-integration-with-line-works"></a>Руководство по интеграции Azure Active Directory с приложением LINE WORKS

В этом руководстве описано, как интегрировать LINE WORKS с Azure Active Directory (Azure AD).

Интеграция LINE WORKS с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к LINE WORKS.
- Вы можете включить автоматический вход пользователей в LINE WORKS (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с LINE WORKS, требуется:

- подписка Azure AD;
- подписка LINE WORKS с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление LINE WORKS из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-line-works-from-the-gallery"></a>Добавление LINE WORKS из коллекции.
Чтобы настроить интеграцию LINE WORKS с Azure AD, необходимо добавить это решение из коллекции в список управляемых приложений SaaS.

**Чтобы добавить LINE WORKS из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![изображение](./media/worksmobile-tutorial/selectazuread.png)

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![изображение](./media/worksmobile-tutorial/a_select_app.png)
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![изображение](./media/worksmobile-tutorial/a_new_app.png)

4. В поле поиска введите **LINE WORKS**, выберите **LINE WORKS** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![изображение](./media/worksmobile-tutorial/tutorial_lineworks_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описано, как настроить и проверить единый вход Azure AD в приложение LINE WORKS для тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходимо знать, какой пользователь в LINE WORKS соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в LINE WORKS.

Чтобы настроить и проверить единый вход Azure AD в LINE WORKS, потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя LINE WORKS](#create-a-line-works-test-user)** требуется, чтобы в LINE WORKS существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении LINE WORKS.

**Чтобы настроить единый вход Azure AD в LINE WORKS, выполните следующие действия:**

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **LINE WORKS** щелкните **Единый вход**.

    ![изображение](./media/worksmobile-tutorial/b1_b2_select_sso.png)

2. В диалоговом окне **Выбрать метод единого входа** щелкните **Выбрать** для режима **SAML**, чтобы включить единый вход.

    ![изображение](./media/worksmobile-tutorial/b1_b2_saml_sso.png)

3. На странице **Настройка единого входа с помощью SAML** нажмите кнопку **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![изображение](./media/worksmobile-tutorial/b1-domains_and_urlsedit.png)

4. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    a. В текстовое поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://auth.worksmobile.com/d/login/<domain>/?userId=<ID@domain>`.

    b. В текстовом поле **Идентификатор** введите URL-адрес: `worksmobile.com`

    ![изображение](./media/worksmobile-tutorial/tutorial_lineworks_url.png)

5. На странице **Настройка единого входа с помощью SAML** откройте раздел **Сертификат подписи SAML** и щелкните ссылку **Скачать** рядом с пунктом **Сертификат (Без форматирования)**, затем сохраните этот сертификат на компьютере.

    ![изображение](./media/worksmobile-tutorial/tutorial_lineworks_certficate.png)

6. Требуемый URL-адрес можно скопировать из раздела **Настройки LINE WORKS**.

    a. URL-адрес входа.

    b. Идентификатор Azure AD

    c. URL-адрес выхода

    ![изображение](./media/worksmobile-tutorial/d1_samlsonfigure.png) 

7. Чтобы настроить единый вход на боковой панели **LINE WORKS**, изучите [документацию SSO WORKS](https://developers.worksmobile.com/jp/document/1001080101) и настройте параметр LINE WORKS.

> [!NOTE]
> Необходимо преобразовать скачанный файл сертификата .cert в .pem.


### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![изображение](./media/worksmobile-tutorial/d_users_and_groups.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![изображение](./media/worksmobile-tutorial/d_adduser.png)

3. В разделе свойств пользователя сделайте следующее.

    ![изображение](./media/worksmobile-tutorial/d_userproperties.png)

    a. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon@yourcompanydomain.extension**.  
    Например, BrittaSimon@contoso.com

    c. Выберите **Свойства**, установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-line-works-test-user"></a>Создание тестового пользователя LINE WORKS

В этом разделе описано, как создать пользователя Britta Simon в приложении LINE WORKS. Получите доступ к [странице администрирования LINE WORKS](https://admin.worksmobile.com) и добавьте пользователей на платформу LINE WORKS.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как предоставить пользователю Britta Simon доступ к LINE WORKS, чтобы он мог использовать единый вход Azure.

1. На портале Azure перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![изображение](./media/worksmobile-tutorial/d_all_applications.png)

2. В списке приложений выберите **LINE WORKS**.

    ![изображение](./media/worksmobile-tutorial/tutorial_lineworks_app.png)

3. В меню слева выберите **Пользователи и группы**.

    ![изображение](./media/worksmobile-tutorial/d_leftpaneusers.png)

4. Нажмите кнопку **Добавить**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![изображение](./media/worksmobile-tutorial/d_assign_user.png)

4. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

5. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку LINE WORKS на панели доступа, вы автоматически войдете в приложение LINE WORKS.
Дополнительные сведения о панели доступа см. в статье с [общими сведениями о панели доступа](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


