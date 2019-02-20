---
title: Руководство. Интеграция Azure Active Directory с GlassFrog | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и GlassFrog.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7cf5dae6-32d6-418e-8ef2-b2041e686086
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6bdc9f2214f8a23ffd1a126905faf00f2fc4264
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56199646"
---
# <a name="tutorial-azure-active-directory-integration-with-glassfrog"></a>Руководство по Интеграция Azure Active Directory с GlassFrog

В этом руководстве описано, как интегрировать GlassFrog с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением GlassFrog обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к GlassFrog.
- Вы можете включить автоматический вход пользователей в GlassFrog (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с GlassFrog, вам потребуется:

- подписка Azure AD;
- подписка GlassFrog с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. добавление GlassFrog из коллекции;
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-glassfrog-from-the-gallery"></a>добавление GlassFrog из коллекции;
Чтобы настроить интеграцию GlassFrog с Azure AD, необходимо добавить GlassFrog из коллекции в список управляемых приложений SaaS.

**Чтобы добавить GlassFrog из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![изображение](./media/glassfrog-tutorial/selectazuread.png)

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![изображение](./media/glassfrog-tutorial/a_select_app.png)
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![изображение](./media/glassfrog-tutorial/a_new_app.png)

4. В поле поиска введите **GlassFrog**, выберите **GlassFrog** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![изображение](./media/glassfrog-tutorial/tutorial_glassfrog_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в GlassFrog с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходимо знать, какой пользователь в GlassFrog соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в GlassFrog.

Чтобы настроить и проверить единый вход Azure AD в GlassFrog, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя приложения GlassFrog](#create-a-glassfrog-test-user)** требуется для того, чтобы в GlassFrog существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении GlassFrog.

**Чтобы настроить единый вход Azure AD в GlassFrog, выполните следующие действия:**

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **GlassFrog** выберите **Единый вход**.

    ![изображение](./media/glassfrog-tutorial/b1_b2_select_sso.png)

2. В диалоговом окне **Выбрать метод единого входа** щелкните **Выбрать** для режима **SAML**, чтобы включить единый вход.

    ![изображение](./media/glassfrog-tutorial/b1_b2_saml_sso.png)

3. На странице **Настройка единого входа с помощью SAML** нажмите кнопку **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![изображение](./media/glassfrog-tutorial/b1-domains_and_urlsedit.png)

4. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    В текстовом поле **URL-адрес входа** введите URL-адрес в формате `https://app.glassfrog.com/people/sso?org_id=<ORGANIZATIONID>`.

    ![изображение](./media/glassfrog-tutorial/tutorial_glassfrog_url.png)

    > [!NOTE] 
    > Значение URL-адреса входа приведено для примера. Вместо него необходимо указать фактический URL-адрес входа. Чтобы получить это значение, обратитесь к [группе поддержки GlassFrog](https://support.glassfrog.com/support/solutions/9000107654).
 
5. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** нажмите кнопку **Скачать**, чтобы скачать **XML метаданных федерации** и сохранить его на компьютере.

    ![изображение](./media/glassfrog-tutorial/tutorial_glassfrog_certificate.png) 

6. Чтобы настроить единый вход на стороне **GlassFrog**, нужно отправить скачанный **XML метаданных федерации** [группе поддержки GlassFrog](mailto:support@alchemy.fr). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![изображение](./media/glassfrog-tutorial/d_users_and_groups.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![изображение](./media/glassfrog-tutorial/d_adduser.png)

3. В разделе свойств пользователя сделайте следующее:

    ![изображение](./media/glassfrog-tutorial/d_userproperties.png)

    a. В поле **Имя** введите **BrittaSimon**.
  
    б) В поле **Имя пользователя** введите **brittasimon@yourcompanydomain.extension**.  
    Например BrittaSimon@contoso.com.

    c. Выберите **Свойства**, установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-glassfrog-test-user"></a>Создание тестового пользователя GlassFrog

В этом разделе описано, как создать пользователя Britta Simon в приложении GlassFrog. Обратитесь в  [группу поддержки GlassFrog](https://support.glassfrog.com/support/solutions/9000107654), чтобы добавить пользователей на платформу GlassFrog. Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как предоставить пользователю Britta Simon доступ к GlassFrog, чтобы он мог использовать единый вход Azure.

1. На портале Azure перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![изображение](./media/glassfrog-tutorial/d_all_applications.png)

2. В списке приложений выберите **GlassFrog**.

    ![изображение](./media/glassfrog-tutorial/tutorial_glassfrog_app.png)

3. В меню слева выберите **Пользователи и группы**.

    ![изображение](./media/glassfrog-tutorial/d_leftpaneusers.png)

4. Нажмите кнопку **Добавить**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![изображение](./media/glassfrog-tutorial/d_assign_user.png)

4. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

5. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент GlassFrog на панели доступа, вы автоматически войдете в приложение GlassFrog.
Дополнительные сведения о панели доступа см. в статье с [общими сведениями о панели доступа](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


