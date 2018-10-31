---
title: Руководство по интеграции Azure Active Directory с Phraseanet | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Phraseanet.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 078d84ce-e054-4ae1-a52e-46a94a6959a7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/09/2018
ms.author: jeedes
ms.openlocfilehash: cfeadbf1d46d9e36f8619cafe29d9dd69aad6eec
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49118326"
---
# <a name="tutorial-azure-active-directory-integration-with-phraseanet"></a>Руководство по интеграции Azure Active Directory с Phraseanet

В этом руководстве описано, как интегрировать Phraseanet с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Phraseanet обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Phraseanet.
- Вы можете включить автоматический вход пользователей в Phraseanet (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Phraseanet, вам потребуется:

- подписка Azure AD;
- подписка Phraseanet с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Phraseanet из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-phraseanet-from-the-gallery"></a>Добавление Phraseanet из коллекции
Чтобы настроить интеграцию Phraseanet с Azure AD, необходимо добавить Phraseanet из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Phraseanet из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![изображение](./media/phraseanet-tutorial/selectazuread.png)

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![изображение](./media/phraseanet-tutorial/a_select_app.png)
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![изображение](./media/phraseanet-tutorial/a_new_app.png)

4. В поле поиска введите **Phraseanet**, выберите **Phraseanet** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![изображение](./media/phraseanet-tutorial/tutorial_phraseanet_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Phraseanet с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Phraseanet соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Phraseanet.

Чтобы настроить и проверить единый вход Azure AD в Phraseanet, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Phraseanet](#create-a-phraseanet-test-user)** требуется для того, чтобы в Phraseanet существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Phraseanet.

**Чтобы настроить единый вход Azure AD в Phraseanet, выполните следующие действия.**

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Phraseanet** выберите **Единый вход**.

    ![изображение](./media/phraseanet-tutorial/b1_b2_select_sso.png)

2. В диалоговом окне **Выбрать метод единого входа** щелкните **Выбрать** для режима **SAML**, чтобы включить единый вход.

    ![изображение](./media/phraseanet-tutorial/b1_b2_saml_sso.png)

3. На странице **Set up Single Sign-On with SAML** (Настройка единого входа с помощью SAML) нажмите кнопку **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![изображение](./media/phraseanet-tutorial/b1-domains_and_urlsedit.png)

4. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    В текстовом поле **URL-адрес для входа** введите URL-адрес в формате `https://<SUBDOMAIN>.alchemyasp.com`.

    ![изображение](./media/phraseanet-tutorial/tutorial_phraseanet_url.png)

    > [!NOTE] 
    > Значение URL-адреса входа приведено для примера. Вместо него необходимо указать фактический URL-адрес входа. Чтобы получить это значение, обратитесь к [группе поддержки Phraseanet](mailto:support@alchemy.fr).
 
5. На странице **Set up Single Sign-On with SAML** (Настройка единого входа с помощью SAML), в разделе **Сертификат подписи SAML** нажмите кнопку **Скачать**, чтобы скачать **XML-файл метаданных федерации** и сохранить его на компьютере.

    ![изображение](./media/phraseanet-tutorial/tutorial_phraseanet_certificate.png) 

6. Чтобы настроить единый вход на стороне **Phraseanet**, нужно отправить скачанный **XML-файл метаданных федерации** [группе поддержки Phraseanet](mailto:support@alchemy.fr). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем выберите **All users** (Все пользователи).

    ![изображение](./media/phraseanet-tutorial/d_users_and_groups.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![изображение](./media/phraseanet-tutorial/d_adduser.png)

3. В разделе свойств пользователя сделайте следующее.

    ![изображение](./media/phraseanet-tutorial/d_userproperties.png)

    a. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon@yourcompanydomain.extension**.  
    Например, BrittaSimon@contoso.com

    c. Выберите **Свойства**, установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-phraseanet-test-user"></a>Создание тестового пользователя Phraseanet

В этом разделе описано, как создать пользователя Britta Simon в приложении Phraseanet. Обратитесь к [группе поддержки Phraseanet](mailto:support@alchemy.fr), чтобы добавить пользователей на платформу Phraseanet. Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Phraseanet.

1. На портале Azure перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![изображение](./media/phraseanet-tutorial/d_all_applications.png)

2. Из списка приложений выберите **Phraseanet**.

    ![изображение](./media/phraseanet-tutorial/tutorial_phraseanet_app.png)

3. В меню слева выберите **Пользователи и группы**.

    ![изображение](./media/phraseanet-tutorial/d_leftpaneusers.png)

4. Нажмите кнопку **Добавить**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![изображение](./media/phraseanet-tutorial/d_assign_user.png)

4. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

5. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "Phraseanet" на панели доступа, вы автоматически войдете в приложение Phraseanet.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


