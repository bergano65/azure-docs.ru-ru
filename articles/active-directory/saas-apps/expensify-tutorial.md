---
title: Руководство. Интеграция Azure Active Directory с Expensify | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в приложении Expensify.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 1e761484-7a2f-4321-91f4-6d5d0b69344e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2018
ms.author: jeedes
ms.openlocfilehash: 5d61ac27eb5a6e4f546d8c6db66b84f2c204b507
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52840475"
---
# <a name="tutorial-azure-active-directory-integration-with-expensify"></a>Руководство. Интеграция Azure Active Directory с Expensify

В этом руководстве описано, как интегрировать Expensify с Azure Active Directory (Azure AD).

Интеграция Expensify с Azure AD дает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Expensify.
- Вы можете включить автоматический вход пользователей в Expensify (единый вход) с помощью их учетных записей Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Expensify, вам потребуется:

- подписка Azure AD;
- подписка с поддержкой единого входа Expensify.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Expensify из коллекции
1. настройка и проверка единого входа в Azure AD.

## <a name="adding-expensify-from-the-gallery"></a>Добавление Expensify из коллекции

Чтобы настроить интеграцию Expensify с Azure AD, необходимо добавить это приложение из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Expensify из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![изображение](./media/expensify-tutorial/selectazuread.png)

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![изображение](./media/expensify-tutorial/a_select_app.png)
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![изображение](./media/expensify-tutorial/a_new_app.png)

4. В поле поиска введите **Expensify**, выберите **Expensify** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![изображение](./media/expensify-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Expensify с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Expensify соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Expensify.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Expensify.

Чтобы настроить и проверить единый вход Azure AD в Expensify, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
1. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
1. **[Создание тестового пользователя Expensify](#create-an-expensify-test-user)** требуется для создания в Expensify пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
1. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
1. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В данном разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Expensify.

**Чтобы настроить единый вход Azure AD в Expensify, выполните следующие действия:**

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Expensify** щелкните **Единый вход**.

    ![изображение](./media/expensify-tutorial/b1_b2_select_sso.png)

2. В верхней части экрана щелкните **Изменить режим единого входа**, чтобы выбрать режим **SAML**.

      ![изображение](./media/expensify-tutorial/b1_b2_saml_ssso.png)

3. В диалоговом окне **Выбрать метод единого входа** щелкните **Выбрать** для режима **SAML**, чтобы включить единый вход.

    ![изображение](./media/expensify-tutorial/b1_b2_saml_sso.png)

4. На странице **Настройка единого входа с помощью SAML** нажмите кнопку **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![изображение](./media/expensify-tutorial/b1-domains_and_urlsedit.png)

5. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в формате `https://www.expensify.com/authentication/saml/login`.

    b. В текстовом поле **Идентификатор** введите URL-адрес в формате `https://www.<companyname>.expensify.com`.

    ![изображение](./media/expensify-tutorial/b1-domains_and_urls.png)

    > [!NOTE] 
    > Замените часть <companyname> URL-адреса идентификатора доменным именем вашей компании. Ознакомьтесь с примером `https://contoso.expensify.com` выше. В Expensify, это имя вашего домена, как показано в **Параметры > Управление доменами**.

    ![Загрузка сведений о домене Expensify](./media/expensify-tutorial/tutorial_expensify_domain.png)

6. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Загрузить**, чтобы загрузить нужный вам сертификат, и сохраните его на компьютере.

    ![изображение](./media/expensify-tutorial/certificatebase64.png)

7. Чтобы включить единый вход в Expensify, сначала необходимо включить в этом приложении **управление доменами**. Это можно сделать, выполнив действия, указанные [здесь](https://help.expensify.com/domain-control). Для получения дополнительной поддержки обратитесь в [службу поддержки клиентов Expensify](mailto:help@expensify.com). После включения управления доменами сделайте следующее:
   
    ![Настройка единого входа](./media/expensify-tutorial/tutorial_expensify_51.png)
    
    a. Войдите в приложение Expensify.
    
    b. На панели слева щелкните **Параметры** и перейдите к **SAML**.
    
    c. Переключите параметр **входа SAML** как **Включено**.
    
    d. Откройте скачанные метаданные федерации из Azure AD в блокноте, скопируйте содержимое и вставьте его в текстовое поле **Метаданные поставщика удостоверений**.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD 

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![изображение](./media/expensify-tutorial/d_users_and_groups.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![изображение](./media/expensify-tutorial/d_adduser.png)

3. В разделе свойств пользователя сделайте следующее.

    ![изображение](./media/expensify-tutorial/d_userproperties.png)

    a. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon@yourcompanydomain.extension**.  
    Например, BrittaSimon@contoso.com

    c. Выберите **Свойства**, установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.
 
### <a name="create-an-expensify-test-user"></a>Создание тестового пользователя Expensify

В этом разделе описано, как создать пользователя Britta Simon в приложении Expensify. Обратитесь в [службу поддержки клиентов Expensify](mailto:help@expensify.com), чтобы добавить пользователей на платформу Expensify.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как позволить пользователю Britta Simon использовать единый вход Azure, предоставив доступ к Expensify.

1. На портале Azure перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![изображение](./media/expensify-tutorial/d_all_applications.png)

2. В списке приложений выберите **Expensify**.

    ![изображение](./media/expensify-tutorial/d_all_proapplications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![изображение](./media/expensify-tutorial/d_leftpaneusers.png)

4. Нажмите кнопку **Добавить**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![изображение](./media/expensify-tutorial/d_assign_user.png)

4. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

5. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Expensify на панели доступа, вы автоматически войдете в приложение Expensify.
Дополнительные сведения о панели доступа см. в статье с [общими сведениями о панели доступа](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)




