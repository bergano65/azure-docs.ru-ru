---
title: Руководство по интеграции Azure Active Directory с SAML SSO for Jira by resolution GmbH | Документация Майкрософт
description: Узнайте, как настроить единый вход для Azure Active Directory и SAML SSO for Jira by resolution GmbH.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 20e18819-e330-4e40-bd8d-2ff3b98e035f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/03/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 957fee48397bc0b23737157dec0e74cf6505fab5
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160132"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Руководство по Интеграция Azure Active Directory с SAML SSO for Jira by resolution GmbH

В этом руководстве объясняется, как настроить SAML SSO for Jira by resolution GmbH для использования с Azure Active Directory (Azure AD).
Интеграция SAML SSO for Jira by resolution GmbH с Azure AD предоставляет следующие преимущества.

* С помощью Azure AD вы можете управлять доступом пользователей к Jira, используя подключаемый модуль единого входа SAML SSO for Jira by resolution GmbH.
* Вы можете включить автоматический вход пользователей в Jira с помощью учетных записей Azure AD, используя SAML SSO for Jira by resolution GmbH (единый вход).
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с SAML SSO for Jira by resolution GmbH, вам потребуется следующее:

* подписка Azure AD Если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).
* Подписка SAML SSO for Jira by resolution GmbH с поддержкой единого входа

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* SAML SSO for Jira by resolution GmbH поддерживает единый вход, инициированный **поставщиком службы** и **поставщиком удостоверений**.

## <a name="adding-an-enterprise-application-for-single-sign-on"></a>Добавление корпоративного приложения для единого входа

Чтобы настроить единый вход в Azure AD, необходимо добавить новое корпоративное приложение. Для этого в коллекции есть предварительно настроенное приложение **SAML SSO for Jira by resolution GmbH**.

**Чтобы добавить SAML SSO for Jira by resolution GmbH из коллекции, выполните следующее.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в раздел **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Новое приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. Чтобы добавить приложение, в поле поиска введите **SAML SSO for Jira by resolution GmbH**, затем на панели результатов выберите **SAML SSO for Jira by resolution GmbH** и нажмите кнопку **Добавить**. Можно также изменить имя корпоративного приложения.

     ![Список результатов SAML SSO for Jira by resolution GmbH](common/search-new-app.png)

## <a name="configure-and-test-single-sign-on-with-the-saml-sso-plugin-and-azure-ad"></a>Настройка и проверка единого входа, выполняемого с помощью подключаемого модуля единого входа SAML и Azure AD

В этом разделе описано, как настроить и проверить единый вход Azure AD в Jira для тестового пользователя **Britta Simon**.
Чтобы единый вход работал, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в SAML SSO for Jira by resolution GmbH.

Чтобы настроить и проверить единый вход, вам потребуется выполнить следующие действия:

1. **[Настроить корпоративное приложение Azure AD для единого входа](#configure-the-azure-ad-enterprise-application-for-single-sign-on)** .
2. **[Настроить подключаемый модуль единого входа SAML для экземпляра Jira](#configure-the-saml-sso-plugin-of-your-jira-instance)** , чтобы настроить параметры единого входа на стороне приложения.
3. **[Создать тестового пользователя Azure AD](#create-an-azure-ad-test-user)** .
1. **[Назначить тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** и разрешить ему использовать единый вход на стороне Azure.
1. **[Создать тестового пользователя в Jira](#create-the-test-user-also-in-jira)** , чтобы в Jira существовал тестовый пользователь, связанный с одноименным пользователем в Azure AD.
1. **[Проверить единый вход](#test-single-sign-on)** , чтобы убедиться в корректной работе конфигурации.

### <a name="configure-the-azure-ad-enterprise-application-for-single-sign-on"></a>Настройка корпоративного приложения Azure AD для единого входа

В этом разделе вы настроите единый вход на портале Azure.

Чтобы настроить единый вход Azure AD в SAML SSO for Jira by resolution GmbH, выполните следующее:

1. На [портале Azure](https://portal.azure.com/) в только что созданном корпоративном приложении **SAML SSO for Jira by resolution GmbH** выберите **Единый вход** на панели слева.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. После этого щелкните значок **правки**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. Если вы хотите настроить приложение в режиме, инициируемом **поставщиком удостоверений**, в разделе **Базовая конфигурация SAML** выполните следующие действия:

    ![Информация о едином входе домена и URL-адресов приложения SAML SSO for Jira by resolution GmbH](common/idp-intiated.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в формате `https://<server-base-url>/plugins/servlet/samlsso`.

    б) В текстовом поле **URL-адрес ответа** введите URL-адрес в формате `https://<server-base-url>/plugins/servlet/samlsso`.

    c. Чтобы настроить приложение для работы в режиме, инициируемом **поставщиком службы**, щелкните **Задать дополнительные URL-адреса** и выполните следующие действия.

    ![Информация о едином входе домена и URL-адресов приложения SAML SSO for Jira by resolution GmbH](common/metadata-upload-additional-signon.png)

    В текстовом поле **URL-адрес входа** введите URL-адрес в формате `https://<server-base-url>/plugins/servlet/samlsso`.

    > [!NOTE]
    > В полях идентификатора, URL-адреса ответа и URL-адреса входа замените **\<server-base-url>** базовым URL-адресом своего экземпляра Jira. Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure. Если у вас возникли проблемы, обратитесь в [службу поддержку SAML SSO for Jira by resolution GmbH](https://www.resolution.de/go/support).

4. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** скачайте файл **XML метаданных федерации** и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

### <a name="configure-the-saml-sso-plugin-of-your-jira-instance"></a>Настройка подключаемого модуля единого входа SAML для экземпляра Jira 

1. В другом окне веб-браузера войдите в свой экземпляр Jira с правами администратора.

2. Наведите указатель мыши на значок шестеренки справа и щелкните **Manage apps** (Управление приложениями).
    
    ![Настройка единого входа](./media/samlssojira-tutorial/addon1.png)

3. Если вы будете перенаправлены на страницу доступа для администратора, введите **пароль** и нажмите кнопку **Confirm** (Подтвердить).

    ![Настройка единого входа](./media/samlssojira-tutorial/addon2.png)

4. В Jira обычно выполняется перенаправление на сайт Atlassian Marketplace. Если этого не произошло, на панели слева щелкните **Find new apps** (Найти новые приложения). Выполните поиск по запросу **SAML Single Sign On (SSO) for JIRA** и нажмите кнопку **Install** (Установить), чтобы установить подключаемый модуль SAML.

    ![Настройка единого входа](./media/samlssojira-tutorial/store.png)

5. Начнется установка подключаемого модуля. По окончании установки нажмите кнопку **Close** (Закрыть).

    ![Настройка единого входа](./media/samlssojira-tutorial/store-2.png)

    ![Настройка единого входа](./media/samlssojira-tutorial/store-3.png)

6. Нажмите кнопку **Manage** (Управлять).

    ![Настройка единого входа](./media/samlssojira-tutorial/store-4.png)
    
8. После этого щелкните **Configure** (Настроить), чтобы настроить установленный подключаемый модуль.

    ![Настройка единого входа](./media/samlssojira-tutorial/store-5.png)

9. В мастере **SAML SingleSignOn Plugin Configuration** (Конфигурация подключаемого модуля единого входа SAML) щелкните **Add new IdP** (Добавить новый поставщик удостоверений), чтобы настроить Azure AD в качестве нового поставщика удостоверений.

    ![Настройка единого входа](./media/samlssojira-tutorial/addon4.png) 

10. На странице **Choose your SAML Identity Provider** (Выбор поставщика удостоверений SAML) выполните следующие действия:

    ![Настройка единого входа](./media/samlssojira-tutorial/addon5a.png)
 
    a. Для типа поставщика удостоверений выберите значение **Azure AD**.
    
    b. Добавьте **имя** поставщика удостоверений (например, Azure AD).
    
    c. При необходимости добавьте **описание** поставщика удостоверений (например, Azure AD).
    
    d. Щелкните **Далее**.
    
11. На странице **настройки поставщика удостоверений** нажмите кнопку **Далее**.
 
    ![Настройка единого входа](./media/samlssojira-tutorial/addon5b.png)

12. На странице **Import SAML IdP Metadata** (Импорт метаданных поставщика удостоверений SAML) выполните следующие действия:

    ![Настройка единого входа](./media/samlssojira-tutorial/addon5c.png)

    a. Нажмите кнопку **Select Metadata XML File** (Выбрать XML-файл метаданных) и выберите ранее скачанный файл **XML метаданных федерации**.

    b. Нажмите кнопку **Import** (Импортировать).
     
    c. Дождитесь завершения операции.  
     
    d. Нажмите кнопку **Далее**.
    
13. На странице **User ID attribute and transformation** (Атрибут и преобразование идентификатора пользователя) нажмите кнопку **Next** (Далее).

    ![Настройка единого входа](./media/samlssojira-tutorial/addon5d.png)
    
14. На странице **User creation and update** (Создание и изменение пользователя) нажмите кнопку **Save & Next** (Сохранить и продолжить), чтобы сохранить параметры.
    
    ![Настройка единого входа](./media/samlssojira-tutorial/addon6a.png)
    
15. На странице **Test your settings** (Проверка параметров) нажмите кнопку **Skip test & configure manually** (Пропустить проверку и настроить вручную), чтобы не выполнять проверку на этом этапе. Проверка будет выполнена на одном из следующих этапов, и для этого потребуется выполнить некоторые настройки на портале Azure.
    
    ![Настройка единого входа](./media/samlssojira-tutorial/addon6b.png)
    
16. Нажмите кнопку **ОК**, чтобы пропустить предупреждение.
    
    ![Настройка единого входа](./media/samlssojira-tutorial/addon6c.png)

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon. С помощью этого пользователя вы проверите единый вход.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе **Свойства пользователя** сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    a. В поле **Имя** введите **Britta Simon**.
  
    b. В поле **Имя пользователя** введите <b>BrittaSimon@contoso.com</b>.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

Из этого раздела вы узнаете, как добавить пользователя Britta Simon в корпоративное приложение, которое позволит ему применять единый вход.

1. На портале Azure выберите **Корпоративные приложения**, а затем —**Все приложения**. 

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений найдите корпоративное приложение, которое вы создали в начале этого руководства. Если вы следовали инструкциям из руководства, оно называется **SAML SSO for Jira by resolution GmbH**. Если вы задали приложению другое имя, найдите его по этому имени.

    ![Ссылка на SAML SSO for Jira by resolution GmbH в списке приложений](common/all-applications.png)

3. На панели слева щелкните **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** — **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** выберите **Britta Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-the-test-user-also-in-jira"></a>Создание тестового пользователя в Jira

Чтобы пользователи Azure AD могли выполнять вход в SAML SSO for Jira by resolution GmbH, их необходимо подготовить в этом приложении. В этом руководстве показано, как выполнять подготовку вручную. Но для этого подключаемого модуля единого входа SAML также доступны другие модели подготовки, например **JIT**-подготовка пользователей. См. документацию по [SAML SSO by resolution GmbH](https://wiki.resolution.de/doc/saml-sso/latest/all). Если у вас возникли вопросы, обратитесь в [службу поддержку](https://www.resolution.de/go/support).

**Чтобы подготовить учетную запись пользователя вручную, выполните следующие действия:**

1. Войдите в Jira с правами администратора.

2. Наведите указатель мыши на значок шестеренки и выберите **User management** (Управление пользователями).

   ![Добавление сотрудника](./media/samlssojira-tutorial/user1.png)

3. Если вы будете перенаправлены на страницу доступа для администратора, введите **пароль** и нажмите кнопку **Confirm** (Подтвердить).

    ![Добавление сотрудника](./media/samlssojira-tutorial/user2.png) 

4. На вкладке **User management** (Управление пользователями) щелкните **Create user** (Создать пользователя).

    ![Добавление сотрудника](./media/samlssojira-tutorial/user3-new.png) 

5. На странице **Create New User** (Создание пользователя) выполните следующие действия. Вам нужно создать пользователя точно так же, как и в Azure AD.

    ![Добавление сотрудника](./media/samlssojira-tutorial/user4-new.png) 

    a. В текстовом поле **Email Address** (Адрес электронной почты) введите адрес электронной почты пользователя — <b>BrittaSimon@contoso.com</b>.

    b. В текстовом поле **Full Name** (Полное имя) введите полное имя пользователя — **Britta Simon**.

    c. В текстовом поле **Username** (Имя пользователя) введите адрес электронной почты пользователя — <b>BrittaSimon@contoso.com</b>. 

    d. В текстовом поле **Password** (Пароль) введите пароль пользователя.

    д. Нажмите кнопку **Create user** (Создать пользователя), чтобы завершить процедуру.

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "SAML SSO for Jira by resolution GmbH" на панели доступа, вы должны автоматически войти в SAML SSO for Jira by resolution GmbH, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

Единый вход можно также проверить, перейдя по адресу [https://\<server-base-url>/plugins/servlet/samlsso](https://\<server-base-url>/plugins/servlet/samlsso). Замените **\<server-base-url>** базовым URL-адресом своего экземпляра Jira.


## <a name="enable-single-sign-on-redirection-for-jira"></a>Включение перенаправления для единого входа в Jira

Как отмечалось в предыдущем разделе, сейчас существует два способа активации единого входа: с помощью **портала Azure** или **специальной ссылки на экземпляр Jira**. SAML SSO plugin by resolution GmbH также позволяет активировать единый вход путем простого **перехода по любому URL-адресу, указывающему на ваш экземпляр Jira**.

По сути, все пользователи, выполняющие доступ к Jira, будут перенаправляться для единого входа после включения этой функции в подключаемом модуле.

Чтобы включить перенаправление SSO, выполните следующие действия **в своем экземпляре Jira**:

1. Перейдите на страницу конфигурации подключаемого модуля единого входа SAML в Jira.
1. На панели слева щелкните**Redirection** (Перенаправление).
![](./media/samlssojira-tutorial/ssore1.png)

1. Установите флажок **Enable SSO Redirect** (Включить перенаправление для единого входа).
![](./media/samlssojira-tutorial/ssore2.png) 

1. Нажмите кнопку **Save Settings** (Сохранить параметры) в правом верхнем углу.

После включения этой функции вы по-прежнему сможете выполнять запрос имени пользователя и пароля. Для этого перейдите по адресу [https://\<server-base-url>/login.jsp?nosso](https://\<server-base-url>/login.jsp?nosso) и установите флажок **Enable nosso** (Включить запрет SSO). Как и ранее, замените **\<server-base-url>** базовым URL-адресом своего экземпляра Jira.


## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

