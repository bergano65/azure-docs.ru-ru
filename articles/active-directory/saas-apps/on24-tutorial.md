---
title: Руководство по интеграции Azure Active Directory с ON24 Virtual Environment SAML Connection | Документация Майкрософт
description: Сведения о настройке единого входа между Azure Active Directory и ON24 Virtual Environment SAML Connection.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d4028fb5-b2ad-4c5d-b123-7b675c509d64
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: jeedes
ms.openlocfilehash: 1ec18f0013a7fa640395a8b8bedd9df8b0924c3a
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/10/2018
ms.locfileid: "49071117"
---
# <a name="tutorial-azure-active-directory-integration-with-on24-virtual-environment-saml-connection"></a>Руководство по интеграции Azure Active Directory с ON24 Virtual Environment SAML Connection

В этом руководстве описано, как интегрировать ON24 Virtual Environment SAML Connection с Azure Active Directory (Azure AD).

Интеграция ON24 Virtual Environment SAML Connection с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к приложению ON24 Virtual Environment SAML Connection.
- Вы можете включить автоматический вход пользователей в ON24 Virtual Environment SAML Connection (единый вход) под учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с ON24 Virtual Environment SAML Connection, вам потребуется:

- подписка Azure AD;
- подписка ON24 Virtual Environment SAML Connection с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление ON24 Virtual Environment SAML Connection из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-on24-virtual-environment-saml-connection-from-the-gallery"></a>Добавление ON24 Virtual Environment SAML Connection из коллекции
Чтобы настроить интеграцию приложения ON24 Virtual Environment SAML Connection с Azure AD, необходимо добавить это приложении из коллекции в список управляемых приложений SaaS.

**Чтобы добавить ON24 Virtual Environment SAML Connection из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![изображение](./media/on24-tutorial/selectazuread.png)

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![изображение](./media/on24-tutorial/a_select_app.png)
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![изображение](./media/on24-tutorial/a_new_app.png)

4. В поле поиска введите **ON24 Virtual Environment SAML Connection**, выберите **ON24 Virtual Environment SAML Connection** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить приложение.

     ![изображение](./media/on24-tutorial/tutorial_on24_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в ON24 Virtual Environment SAML Connection с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в ON24 Virtual Environment SAML Connection соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в ON24 Virtual Environment SAML Connection.

Чтобы настроить и проверить единый вход Azure AD в ON24 Virtual Environment SAML Connection, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя ON24 Virtual Environment SAML Connection](#create-an-on24-virtual-environment-saml-connection-test-user)** требуется для того, чтобы в ON24 Virtual Environment SAML Connection существовал пользователь Britta Simon, связанный с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении ON24 Virtual Environment SAML Connection.

**Чтобы настроить единый вход Azure AD в ON24 Virtual Environment SAML Connection, сделайте следующее:**

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **ON24 Virtual Environment SAML Connection** щелкните **Единый вход**.

    ![изображение](./media/on24-tutorial/B1_B2_Select_SSO.png)

2. В верхней части экрана щелкните **Изменить режим единого входа**, чтобы выбрать режим **SAML**.

      ![изображение](./media/on24-tutorial/b1_b2_saml_ssso.png)

3. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML**, чтобы включить единый вход.

    ![изображение](./media/on24-tutorial/b1_b2_saml_sso.png)

4. На странице **Настройка единого входа с помощью SAML** нажмите кнопку **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![изображение](./media/on24-tutorial/b1-domains_and_urlsedit.png)

5. Если вы хотите настроить приложение в режиме, инициированном **поставщиком удостоверений**, в разделе **Базовая конфигурация SAML** выполните следующие действия:

    ![изображение](./media/on24-tutorial/tutorial_on24_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес:

     **URL-адрес рабочей среды**
    
    `SAML-VSHOW.on24.com`

    `SAML-Gateway.on24.com` 

    `SAP PROD SAML-EliteAudience.on24.com` 
                
     **URL-адрес среды контроля качества**
    
    `SAMLQA-VSHOW.on24.com` 

    `SAMLQA-Gateway.on24.com` 

    `SAMLQA-EliteAudience.on24.com`
 
    b. В текстовом поле **URL-адрес ответа** введите URL-адрес:
    
     **URL-адрес рабочей среды**
    
    `https://federation.on24.com/sp/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1WU2hvdy5vbjI0LmNvbSJ9/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1HYXRld2F5Lm9uMjQuY29tIn0/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1FbGl0ZUF1ZGllbmNlLm9uMjQuY29tIn0/ACS.saml2`

     **URL-адрес среды контроля качества**
    
    `https://qafederation.on24.com/sp/ACS.saml2`

    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLVZzaG93Lm9uMjQuY29tIn0/ACS.saml2`

    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUdhdGV3YXkub24yNC5jb20ifQ/ACS.saml2`
     
    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUVsaXRlQXVkaWVuY2Uub24yNC5jb20ifQ/ACS.saml2` 

    c. Щелкните **Задать дополнительные URL-адреса**. 

    d. В текстовом поле **Состояние ретранслятора** введите такой URL-адрес: `https://vshow.on24.com/vshow/ms_azure_saml_test?r=<ID>`

    д. Если вы хотите настроить приложение в **режиме, инициированном поставщиком услуг**, тогда в текстовом поле **URL-адрес входа** введите URL-адрес: `https://vshow.on24.com/vshow/<INSTANCENAME>`

6. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Загрузить**, чтобы загрузить нужный вам сертификат, и сохраните его на компьютере.

    ![изображение](./media/on24-tutorial/tutorial_on24_certificate.png) 

7. Чтобы настроить единый вход на стороне приложения **ON24 Virtual Environment SAML Connection**, отправьте [группе поддержки ON24 Virtual Environment SAML Connection](https://www.on24.com/about-us/support/) сертификат и метаданные, которые вы загрузили с портала Azure. Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![изображение](./media/on24-tutorial/d_users_and_groups.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![изображение](./media/on24-tutorial/d_adduser.png)

3. В разделе свойств пользователя сделайте следующее.

    ![изображение](./media/on24-tutorial/d_userproperties.png)

    a. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon@yourcompanydomain.extension**.  
    Например, BrittaSimon@contoso.com

    c. Выберите **Свойства**, установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.
 
### <a name="create-an-on24-virtual-environment-saml-connection-test-user"></a>Создание тестового пользователя в ON24 Virtual Environment SAML Connection

В этом разделе вы создадите пользователя с именем Britta Simon в ON24 Virtual Environment SAML Connection. Обратитесь в [группу поддержки ON24 Virtual Environment SAML Connection](https://www.on24.com/about-us/support/), чтобы добавить пользователей на платформу ON24 Virtual Environment SAML Connection. Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как позволить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к ON24 Virtual Environment SAML Connection.

1. На портале Azure перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![изображение](./media/on24-tutorial/d_all_applications.png)

2. В списке приложений выберите **ON24 Virtual Environment SAML Connection**.

    ![изображение](./media/on24-tutorial/tutorial_on24_app.png)

3. В меню слева выберите **Пользователи и группы**.

    ![изображение](./media/on24-tutorial/d_leftpaneusers.png)

4. Нажмите кнопку **Добавить**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![изображение](./media/on24-tutorial/d_assign_user.png)

4. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

5. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку ON24 Virtual Environment SAML Connection на панели доступа, вы автоматически войдете в приложение ON24 Virtual Environment SAML Connection.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

