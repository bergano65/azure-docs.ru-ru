---
title: Руководство. Интеграция Azure Active Directory с join.me | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и join.me.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: cda5ea0d-3270-4ba5-ad81-4df108eaad12
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f61520994bdeeab75b6d26731dee9af15b4ccda6
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56209546"
---
# <a name="tutorial-azure-active-directory-integration-with-joinme"></a>Руководство. Интеграция Azure Active Directory с join.me

В этом руководстве описано, как интегрировать join.me с Azure Active Directory (Azure AD).

Ниже перечислены преимущества интеграции Azure AD с join.me.

- С помощью Azure AD вы можете контролировать доступ к join.me.
- Вы можете включить автоматический вход пользователей в join.me (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с join.me, вам потребуется:

- подписка Azure AD;
- подписка join.me с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление join.me из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-joinme-from-the-gallery"></a>Добавление join.me из коллекции.
Чтобы настроить интеграцию join.me с Azure AD, необходимо добавить join.me из коллекции в список управляемых приложений SaaS.

**Чтобы добавить join.me из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![изображение](./media/joinme-tutorial/selectazuread.png)

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![изображение](./media/joinme-tutorial/a_select_app.png)
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![изображение](./media/joinme-tutorial/a_new_app.png)

4. В поле поиска введите **join.me**, выберите **join.me** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![изображение](./media/joinme-tutorial/tutorial_joinme_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в join.me с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в join.me соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в join.me.

Чтобы настроить и проверить единый вход Azure AD в join.me, необходимо выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя приложения join.me](#create-a-joinme-test-user)** требуется для того, чтобы в join.me существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении join.me.

**Чтобы настроить единый вход Azure AD в join.me, выполните описанные ниже действия.**

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **join.me** выберите **Единый вход**.

    ![изображение](./media/joinme-tutorial/b1_b2_select_sso.png)

2. В верхней части экрана щелкните **Изменить режим единого входа**, чтобы выбрать режим **SAML**.

      ![изображение](./media/joinme-tutorial/b1_b2_saml_ssso.png)

3. В диалоговом окне **Выбрать метод единого входа** щелкните **Выбрать** для режима **SAML**, чтобы включить единый вход.

    ![изображение](./media/joinme-tutorial/b1_b2_saml_sso.png)

4. На странице **Настройка единого входа с помощью SAML** нажмите кнопку **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![изображение](./media/joinme-tutorial/b1-domains_and_urlsedit.png)

5. В разделе **Базовая конфигурация SAML** не нужно выполнять никаких действий, так как приложение уже предварительно интегрировано с Azure.

     ![изображение](./media/joinme-tutorial/tutorial_joinme_url.png)
 
6. На странице **Настройка единого входа с помощью SAML**, в разделе **Сертификат подписи SAML** нажмите кнопку "Копировать", чтобы копировать **URL-адрес метаданных федерации приложений** и сохранить его на компьютере.

    ![изображение](./media/joinme-tutorial/certificatebase64.png) 

7. Чтобы настроить единый вход на стороне приложения **join.me**, отправьте **URL-адрес метаданных федерации приложений**, скопированный ранее с портала Azure, в [службу поддержки join.me](https://help.join.me/s/?language). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![изображение](./media/joinme-tutorial/d_users_and_groups.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![изображение](./media/joinme-tutorial/d_adduser.png)

3. В разделе свойств пользователя сделайте следующее:

    ![изображение](./media/joinme-tutorial/d_userproperties.png)

    a. В поле **Имя** введите **BrittaSimon**.
  
    б) В поле **Имя пользователя** введите **brittasimon@yourcompanydomain.extension**.  
    Например BrittaSimon@contoso.com.

    c. Выберите **Свойства**, установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.
  
### <a name="create-a-joinme-test-user"></a>Создание тестового пользователя join.me

В этом разделе описано, как создать пользователя Britta Simon в приложении join.me. Обратитесь в  [группу поддержки join.me](https://help.join.me/s/?language), чтобы добавить пользователей на платформу join.me. Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к join.me.

1. На портале Azure перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![изображение](./media/joinme-tutorial/d_all_applications.png)

2. В списке приложений выберите **join.me**.

    ![изображение](./media/joinme-tutorial/tutorial_joinme_app.png)

3. В меню слева выберите **Пользователи и группы**.

    ![изображение](./media/joinme-tutorial/d_leftpaneusers.png)

4. Нажмите кнопку **Добавить**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![изображение](./media/joinme-tutorial/d_assign_user.png)

4. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

5. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент join.me на панели доступа, вы автоматически войдете в приложение join.me.
Дополнительные сведения о панели доступа см. в статье с [общими сведениями о панели доступа](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

