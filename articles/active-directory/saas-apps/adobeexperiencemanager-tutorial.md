---
title: Руководство по Интеграция Azure Active Directory с Adobe Experience Manager | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в приложении Adobe Experience Manager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 88a95bb5-c17c-474f-bb92-1f80f5344b5a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f39751f40b32c5da24e13d75d2607d7da0a57ad3
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73154113"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>Руководство по Интеграция Azure Active Directory с Adobe Experience Manager

В этом руководстве описано, как интегрировать Adobe Experience Manager с Azure Active Directory (Azure AD).
Интеграция Azure AD с Adobe Experience Manager обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к Adobe Experience Manager.
* Вы можете включить автоматический вход пользователей в Adobe Experience Manager (единый вход) с использованием учетных записей Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с приложением Adobe Experience Manager, вам потребуется следующее:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* Подписка Adobe Experience Manager с поддержкой единого входа

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Adobe Experience Manager поддерживает единый вход инициированного **пакета обновления и выдающей точки распространения**.

* Adobe Experience Manager поддерживает **JIT**-подготовку пользователей.

## <a name="adding-adobe-experience-manager-from-the-gallery"></a>Добавление Adobe Experience Manager из галереи

Чтобы настроить интеграцию Adobe Experience Manager в Azure AD, необходимо добавить Adobe Experience Manager из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Adobe Experience Manager из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Adobe Experience Manager** и выберите **Adobe Experience Manager** на панели результатов, а затем нажмите кнопку **Добавить**, чтобы добавить приложение.

     ![Adobe Experience Manager в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в [название приложения] с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в [название приложения].

Чтобы настроить и проверить единый вход Azure AD в [название приложения], вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа Adobe Experience Manager](#configure-adobe-experience-manager-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя Adobe Experience Manager](#create-adobe-experience-manager-test-user)** требуется для того, чтобы в Adobe Experience Manager существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в [название приложения], выполните следующие действия.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Adobe Experience Manager** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. Если вы хотите настроить приложение в режиме, инициируемом **поставщиком удостоверений**, в разделе **Базовая конфигурация SAML** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения Adobe Experience Manager](common/idp-intiated.png)

    a. В текстовом поле **Идентификатор** введите уникальное значение, которое определяется на сервере AEM.

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в формате `https://<AEM Server Url>/saml_login`.

    > [!NOTE]
    > Значение URL-адреса ответа приведено для примера. Обновите значение URL-адреса ответа актуальным значением. Чтобы его получить, свяжитесь с [группой поддержки клиента Adobe Experience Manager Client](https://helpx.adobe.com/support/experience-manager.html). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

5. Чтобы настроить приложение для работы в режиме, инициируемом **поставщиком услуг**, щелкните **Задать дополнительные URL-адреса** и выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения Adobe Experience Manager](common/metadata-upload-additional-signon.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес сервера Adobe Experience Manager.

6. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Загрузить**, чтобы загрузить требуемый **сертификат (Base64)** из предложенных вариантов, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

7. Скопируйте требуемый URL-адрес из раздела **Set up Adobe Experience Manager** (Настройка Adobe Experience Manager).

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    б) Идентификатор Azure AD.

    в) URL-адрес выхода.

### <a name="configure-adobe-experience-manager-single-sign-on"></a>Настройка единого входа Adobe Experience Manager

1. Откройте портал администрирования **Adobe Experience Manager** в другом окне браузера.

2. Выберите **Параметры** > **Безопасность** > **Пользователи**.

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user.png)

3. Выберите **Администратора** или любого другого соответствующего пользователя.

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin6.png)

4. Выберите **Account settings** (Параметры учетной записи)  >  **Manage TrustStore** (Управление TrustStore).

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_managetrust.png)

5. Щелкните **Select Certificate File** (Выбрать файл сертификата) в разделе **Add Certificate from CER file** (Добавление сертификата из CER-файла). Найдите и укажите путь к файлу сертификата, скачанному с портала Azure.

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user2.png)

6. Сертификат добавляется в TrustStore. Обратите внимание на псевдоним сертификата.

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin7.png)

7. На странице **Users** (Пользователи) выберите **authentication-service**.

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin8.png)

8. Выберите **Параметры учетной записи** > **Create/Manage KeyStore** (Создание хранилища ключей/Управление хранилищем ключей). Создайте хранилище ключей, указав пароль.

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin9.png)

9. Вернитесь на страницу администрирования. Выберите **Settings** (Параметры)  >  **Operations** (Операции)  >  **Web Console** (Веб-консоль).

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin1.png)

    Откроется страница конфигурации.

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin2.png)

10. Найдите **Adobe Granite SAML 2.0 Authentication Handler**. Щелкните значок **Add** (Добавить).

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin3.png)

11. Выполните следующие действия на этой странице.

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin4.png)

    a. В текстовом поле **Path** (Путь) введите **/** .

    b. В поле **IDP URL** (URL-адрес IDP) вставьте значение **Login URL** (URL-адрес входа), скопированное на портале Azure.

    c. В поле **IDP Certificate Alias** (Псевдоним сертификата IdP) введите значение **Certificate Alias** (Псевдоним сертификата), которое вы добавили в TrustStore.

    d. В поле **Security Provided Entity ID** (Идентификатор сущности системы безопасности) введите уникальное значение **Azure Ad Identifier** (Идентификатор Azure AD), настроенное на портале Azure.

    д. В поле **Assertion Consumer Service URL** (URL-адрес службы обработчика утверждений) введите значение **URL-адрес ответа**, настроенное на портале Azure.

    Е. В поле **Password of Key Store** (Пароль хранилища ключей) введите **пароль**, установленный в хранилище ключей.

    ж. В поле **User Attribute ID** (Идентификатор пользовательского атрибута) введите **идентификатор имени** или другой пользовательский идентификатор, который используется в вашем случае.

    h. Выберите **Autocreate CRX Users** (Автоматическое создание пользователей CRX).

    i. В поле **Logout URL** (URL-адрес выхода) вставьте значение **URL-адрес выхода**, скопированное на портале Azure.

    j. Щелкните **Сохранить**.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    а. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon\@домен_вашей_компании.доменная_зона**.  
    Например BrittaSimon@contoso.com.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Adobe Experience Manager.

1. На портале Azure последовательно выберите **Корпоративные приложения**, **Все приложения**, а затем — **Adobe Experience Manager**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. Из списка приложений выберите **Adobe Experience Manager**.

    ![Ссылка на Adobe Experience Manager в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-adobe-experience-manager-test-user"></a>Создание тестового пользователя Adobe Experience Manager

В этом разделе описано, как создать пользователя Britta Simon в приложении Adobe Experience Manager. Если выбран параметр **Autocreate CRX Users** (Автоматическое создание пользователей CRX), то пользователи будут создаваться автоматически после успешной аутентификации.

Если вы хотите создать пользователей вручную, обратитесь к [группе поддержки Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html)  для добавления пользователей на платформу Adobe Experience Manager.

### <a name="test-single-sign-on"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку "Adobe Experience Manager" на панели доступа, вы автоматически войдете в приложение Adobe Experience Manager, для которого был настроен единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
