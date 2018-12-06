---
title: Учебник. Интеграция Azure Active Directory с EverBridge | Документы Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в EverBridge.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 58d7cd22-98c0-4606-9ce5-8bdb22ee8b3e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2018
ms.author: jeedes
ms.openlocfilehash: 75bd4325063ff90e4a065c0cf854fa375e58fa95
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/29/2018
ms.locfileid: "52584797"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Руководство по интеграции Azure Active Directory с Everbridge

В этом руководстве описано, как интегрировать EverBridge с Azure Active Directory (Azure AD).

Интеграция EverBridge с Azure AD обеспечивает следующие преимущества.

- Вы можете управлять доступом к EverBridge в Azure AD.
- Вы можете включить автоматический вход пользователей в EverBridge (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с EverBridge, вам потребуется:

- подписка Azure AD;
- подписка EverBridge с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление EverBridge из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-everbridge-from-the-gallery"></a>Добавление EverBridge из коллекции

Чтобы настроить интеграцию EverBridge с Azure AD, необходимо добавить EverBridge из коллекции в список управляемых приложений SaaS.

**Чтобы добавить EverBridge из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **EverBridge**, выберите **EverBridge** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![EverBridge в списке результатов](./media/everbridge-tutorial/tutorial_everbridge_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в приложение EverBridge с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в EverBridge соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в EverBridge.

Чтобы настроить и проверить единый вход Azure AD в EverBridge, необходимо выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя EverBridge](#creating-an-everbridge-test-user)** требуется для создания в EverBridge пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении EverBridge.

**Чтобы настроить единый вход Azure AD в EverBridge, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **EverBridge** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Выбрать метод единого входа** щелкните **Выбрать** для режима **SAML**, чтобы включить единый вход.

    ![Настройка единого входа](common/tutorial_general_301.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Настройка единого входа](common/editconfigure.png)

    >[!NOTE]
    >Необходимо выполнить настройки приложения в качестве портала диспетчера или портала участника на обоих концах, т. е. на портале Azure и портале Everbridge.

4. Чтобы настроить приложение **EverBridge** в качестве **портала диспетчера EverBridge** в разделе **Базовая конфигурация SAML** сделайте следующее:

    ![Сведения о домене и URL-адресах единого входа приложения EverBridge](./media/everbridge-tutorial/tutorial_everbridge_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://sso.everbridge.net/<API_Name>`

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias`.

    > [!NOTE]
    > Эти значения приведены в качестве примера. Измените их на фактические значения идентификатора и URL-адреса ответа. Чтобы получить эти значения, обратитесь в [службу поддержки EverBridge](mailto:support@everbridge.com).

5. Чтобы настроить приложение **EverBridge** в качестве **портала участника EverBridge** в разделе **Базовая конфигурация SAML** сделайте следующее:

    * Если вы хотите настроить приложение в режиме, инициируемом **IDP**, сделайте следующее:

        ![Сведения о домене и URL-адресах единого входа приложения EverBridge](./media/everbridge-tutorial/tutorial_everbridge_url1.png)

        * В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://sso.everbridge.net/<API_Name>/<Organization_ID>`

        * В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias`.

    * Чтобы настроить приложение для работы в режиме, инициируемом **поставщиком услуг**, щелкните **Задать дополнительные URL-адреса** и выполните следующее действие:

        ![Сведения о домене и URL-адресах единого входа приложения EverBridge](./media/everbridge-tutorial/tutorial_everbridge_url2.png)

        * В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true`

    > [!NOTE]
    > Эти значения приведены в качестве примера. Измените их на фактические значения идентификатора и URL-адресов ответа и входа. Чтобы получить эти значения, обратитесь в [службу поддержки EverBridge](mailto:support@everbridge.com).

6. На странице **сертификата подписи SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать **XML метаданных федерации**, а затем сохраните метаданные на компьютере.

    ![Ссылка для скачивания сертификата](./media/everbridge-tutorial/tutorial_everbridge_certificate.png) 

7. Требуемый URL-адрес можно скопировать из раздела **Настройка EverBridge**.

    a. URL-адрес входа.

    b. Идентификатор Azure AD

    c. URL-адрес выхода

    ![Конфигурация EverBridge](common/configuresection.png)

8. Чтобы настроить единый вход для **EverBridge** в качестве приложения **портала диспетчера EverBridge**, сделайте следующее: 
 
9. В другом окне веб-браузера войдите в приложение EverBridge с правами администратора.

9. В меню в верхней части страницы откройте вкладку **Settings** (Параметры) и выберите пункт **Single Sign-On** (Единый вход) в разделе **Security** (Безопасность).
   
    ![Настройка единого входа](./media/everbridge-tutorial/tutorial_everbridge_002.png)
   
    a. В текстовом поле **Name** (Имя) введите имя поставщика идентификаторов (например, название своей компании).
   
    b. В текстовом поле **API Name** (Имя API) введите имя API.
   
    c. Чтобы отправить файл метаданных, скачанный на портале Azure, нажмите кнопку **Выбрать файл**.
   
    d. В поле "Расположение удостоверения SAML" выберите значение **Identity is in the NameIdentifier element of the Subject statement** (Удостоверение находится в элементе NameIdentifier оператора Subject).
   
    д. В текстовое поле **Identity Provider Login URL** (URL-адрес для входа поставщика удостоверений) вставьте **URL-адрес входа**, скопированный на портале Azure.
   
    Е. В поле "Service Provider Initiated Request Binding" (Связывание запросов, инициированных поставщиком услуг) выберите **HTTP Redirect** (Перенаправление HTTP).

    ж. Нажмите кнопку **Сохранить**

10. Чтобы настроить единый вход в приложении **EverBridge** в качестве **портала участника EverBridge**, нужно отправить скачанный файл **XML метаданных федерации** в [службу поддержки EverBridge](mailto:support@everbridge.com). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Создание пользователя Azure AD][100]

2. В верхней части экрана выберите **Новый пользователь**.

    ![Создание тестового пользователя Azure AD](common/create_aaduser_01.png) 

3. В разделе свойств пользователя сделайте следующее.

    ![Создание тестового пользователя Azure AD](common/create_aaduser_02.png)

    a. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon@yourcompanydomain.extension**.  
    Например, BrittaSimon@contoso.com

    c. Выберите **Свойства**, установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.
  
### <a name="creating-an-everbridge-test-user"></a>Создание тестового пользователя EverBridge

В этом разделе описано, как создать пользователя Britta Simon в приложении Everbridge. Чтобы добавить пользователей на платформу EverBridge, обратитесь в [службу поддержки Everbridge](mailto:support@everbridge.com).

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к EverBridge.

1. На портале Azure перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Назначение пользователя][201]

2. В списке приложений выберите **EverBridge**.

    ![Настройка единого входа](./media/everbridge-tutorial/tutorial_everbridge_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку EverBridge на панели доступа, вы автоматически войдете в приложение EverBridge.
Дополнительные сведения о панели доступа см. в статье с [общими сведениями о панели доступа](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
