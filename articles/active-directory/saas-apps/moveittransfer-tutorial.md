---
title: Руководство по Интеграция Azure Active Directory с приложением MOVEit Transfer — Azure AD integration | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в MOVEit Transfer - Azure AD integration.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8ff7102d-be73-4888-ae81-d8e3d01dd534
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.openlocfilehash: 76f1f0d99b47b0a57f97f4fcdf721a78a77ba9b4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67096897"
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>Руководство по Интеграция Azure Active Directory с MOVEit Transfer — Azure AD integration

В этом руководстве, вы узнаете, как интегрировать приложение MOVEit Transfer - Azure AD integration с Azure Active Directory (Azure AD).
Интеграция MOVEit Transfer - Azure AD integration с Azure AD обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к MOVEit Transfer - Azure AD integration.
* Вы можете включить автоматический вход пользователей в MOVEit Transfer - Azure AD integration (единый вход) с учетной записью Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с MOVEit Transfer - Azure AD integration, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* подписка MOVEit Transfer - Azure AD integration с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* MOVEit Transfer - Azure AD integration поддерживает единый вход, инициируемый **поставщиком услуг**.

## <a name="adding-moveit-transfer---azure-ad-integration-from-the-gallery"></a>Добавление приложения MOVEit Transfer - Azure AD integration из коллекции

Чтобы настроить интеграцию MOVEit Transfer - Azure AD integration с Azure AD, необходимо добавить MOVEit Transfer - Azure AD integration из коллекции в список управляемых приложений SaaS.

**Чтобы добавить MOVEit Transfer - Azure AD integration из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **MOVEit Transfer - Azure AD integration**, выберите **MOVEit Transfer - Azure AD integration** на панели результатов и щелкните **Добавить**, чтобы добавить приложение.

     ![Приложение MOVEit Transfer - Azure AD integration в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе показано, как настроить и проверить единый вход Azure AD в приложение MOVEit Transfer - Azure AD integration с использованием тестового пользователя **Britta Simon**.
Чтобы единый вход функционировал, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в MOVEit Transfer - Azure AD integration.

Чтобы настроить и проверить единый вход Azure AD в MOVEit Transfer - Azure AD integration, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в MOVEit Transfer - Azure AD integration](#configure-moveit-transfer---azure-ad-integration-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя MOVEit Transfer - Azure AD integration](#create-moveit-transfer---azure-ad-integration-test-user)** требуется для создания в MOVEit Transfer - Azure AD integration пользователя Britta Simon, связанного с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в MOVEit Transfer - Azure AD integration, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **MOVEit Transfer - Azure AD integration** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. Если у вас есть **файл метаданных поставщика служб**, выполните следующие действия в разделе **Базовая конфигурация SAML**:

    a. Щелкните **Отправить файл метаданных**.

    ![Передача файла метаданных](common/upload-metadata.png)

    b. Щелкните **значок папки**, выберите файл метаданных и нажмите кнопку **Отправить**.

    ![Выбор файла метаданных](common/browse-upload-metadata.png)

    c. После успешной передачи файла метаданных значения **Идентификатор** и **URL-адрес ответа** в разделе **Базовая конфигурация SAML** заполнятся автоматически.

    ![Сведения о домене и URL-адресах единого входа для MOVEit Transfer - Azure AD integration](common/sp-identifier-reply.png)

    В текстовом поле **URL-адрес входа** введите URL-адрес в формате `https://contoso.com`.

    > [!NOTE]
    > Значение **URL-адрес входа** приведено для примера. Вместо него необходимо указать фактический URL-адрес входа. Обратитесь к [группе поддержки клиентов MOVEit Transfer - Azure AD integration](https://community.ipswitch.com/s/support), чтобы получить это значение. Вы можете скачать **файл метаданных поставщика услуг**, воспользовавшись **URL-адресом метаданных поставщика услуг**. Это описано далее в разделе **Настройка единого входа в MOVEit Transfer - Azure AD integration** этого руководства. Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

4. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать нужный вам **XML метаданных федерации**, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

6. Скопируйте требуемые URL-адреса из раздела **Настройка MOVEit Transfer - Azure AD integration**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    b. Идентификатор Azure AD

    c. URL-адрес выхода.

### <a name="configure-moveit-transfer---azure-ad-integration-single-sign-on"></a>Настройка единого входа в MOVEit Transfer - Azure AD integration

1. Войдите в клиент MOVEit Transfer с правами администратора.

2. В левой панели навигации щелкните **Settings**(Параметры).

    ![Раздел Settings (Параметры) на стороне приложения](./media/moveittransfer-tutorial/tutorial_moveittransfer_000.png)

3. Щелкните ссылку **Single Signon** (Единый вход), которая расположена в разделе **Security Policies -> User Auth** (Политики безопасности -> Проверка подлинности пользователя).

    ![Политики безопасности на стороне приложения](./media/moveittransfer-tutorial/tutorial_moveittransfer_001.png)

4. Щелкните ссылку с URL-адресом метаданных, чтобы скачать документ метаданных.

    ![URL-адрес метаданных поставщика службы](./media/moveittransfer-tutorial/tutorial_moveittransfer_002.png)
    
   * Проверьте, соответствует ли **entityID** значению **Идентификатор** в разделе **Базовая конфигурация SAML**.
   * Убедитесь, что URL-адрес расположения **AssertionConsumerService** совпадает со значением **URL-адрес ответа** в разделе **Базовая конфигурация SAML**.
    
     ![Настройка единого входа на стороне приложения](./media/moveittransfer-tutorial/tutorial_moveittransfer_007.png)

5. Щелкните кнопку **Add Identity Provider** (Добавить поставщик удостоверений), чтобы добавить новый федеративный поставщик удостоверений.

    ![Добавление поставщика удостоверений](./media/moveittransfer-tutorial/tutorial_moveittransfer_003.png)

6. Щелкните **Browse...** (Обзор...), чтобы выбрать файл метаданных, который вы скачали на портале Azure, затем нажмите кнопку **Add Identity Provider** (Добавить поставщик удостоверений) и отправьте скачанный файл.

    ![Поставщик удостоверений SAML](./media/moveittransfer-tutorial/tutorial_moveittransfer_004.png)

7. Выберите значение **Yes** (Да) для параметра **Enabled** (Включено) в разделе **Edit Federated Identity Provider Settings...** (Изменение параметров федеративного поставщика удостоверений) и нажмите кнопку **Save** (Сохранить).

    ![Параметры федеративного поставщика удостоверений](./media/moveittransfer-tutorial/tutorial_moveittransfer_005.png)

8. На странице **Edit Federated Identity Provider User Settings** (Изменение параметров федеративного поставщика удостоверений) выполните следующие действия:
    
    ![Изменение параметров федеративного поставщика удостоверений](./media/moveittransfer-tutorial/tutorial_moveittransfer_006.png)
    
    a. Выберите значение **SAML NameID** для параметра **Login name** (Имя для входа).
    
    b. Выберите **Other** (Другое) в качестве **полного имени** и в тестовом поле **Attribute name** (Имя атрибута) введите значение: `http://schemas.microsoft.com/identity/claims/displayname`.
    
    c. Выберите **Other** (Другое) в качестве **электронной почты** и в тестовом поле **Attribute name** (Имя атрибута) введите значение: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
    
    d. Выберите значение **Yes** (Да) для параметра **Auto-create account on signon** (Автоматическое создание учетной записи при первом входе).
    
    д. Нажмите кнопку **Сохранить** .

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

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к MOVEit Transfer - Azure AD integration.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **MOVEit Transfer - Azure AD integration**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **MOVEit Transfer - Azure AD integration**.

    ![Ссылка на MOVEit Transfer - Azure AD integration в списке "Приложения"](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-moveit-transfer---azure-ad-integration-test-user"></a>Создание тестового пользователя MOVEit Transfer - Azure AD integration

Цель этого раздела — создать пользователя с именем Britta Simon в MOVEit Transfer - Azure AD integration. Приложение MOVEit Transfer - Azure AD integration поддерживает JIT-подготовку, и вы уже включили ее. В этом разделе никакие действия с вашей стороны не требуются. Пользователь будет создан при попытке получить доступ к MOVEit Transfer - Azure AD integration (если он еще не создан).

>[!NOTE]
>Если вам нужно вручную создать пользователя, необходимо обратиться в [службу поддержки клиентов MOVEit Transfer - Azure AD integration](https://community.ipswitch.com/s/support).

### <a name="test-single-sign-on"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "MOVEit Transfer - Azure AD integration" на Панели доступа, вы автоматически войдете в приложение MOVEit Transfer - Azure AD integration, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что такое условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

