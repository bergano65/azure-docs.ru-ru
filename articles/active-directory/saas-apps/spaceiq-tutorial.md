---
title: Руководство по Интеграция Azure Active Directory с приложением SpaceIQ | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и SpaceIQ.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5b55ae29-491f-401f-9299-d3a6b64a1b99
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: b3358ad473ede9e8d78a835e8c68e690e5340638
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67090089"
---
# <a name="tutorial-azure-active-directory-integration-with-spaceiq"></a>Руководство по Интеграция Azure Active Directory со SpaceIQ

В этом руководстве описано, как интегрировать SpaceIQ с Azure Active Directory (Azure AD).
Интеграция Azure AD с приложением SpaceIQ обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к SpaceIQ.
* Вы можете включить автоматический вход пользователей в SpaceIQ (единый вход) с помощью учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с приложением SpaceIQ, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/));
* Подписка с поддержкой единого входа SpaceIQ

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* SpaceIQ поддерживает единый вход, инициированный **IDP**.

## <a name="adding-spaceiq-from-the-gallery"></a>Добавление SpaceIQ из коллекции

Чтобы настроить интеграцию SpaceIQ с Azure AD, необходимо добавить SpaceIQ из коллекции в список управляемых приложений SaaS.

**Чтобы добавить SpaceIQ из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **SpaceIQ**, выберите **SpaceIQ** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![SpaceIQ в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описаны настройка и проверка единого входа Azure AD в SpaceIQ с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в SpaceIQ.

Чтобы настроить и проверить единый вход Azure AD в SpaceIQ, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в SpaceIQ](#configure-spaceiq-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя приложения SpaceIQ](#create-spaceiq-test-user)** требуется для того, чтобы в SpaceIQ существовал пользователь Britta Simon, связанный с представлением этого же пользователя в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в SpaceIQ, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **SpaceIQ** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. На странице **Настройка единого входа с помощью SAML** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа приложения SpaceIQ](common/idp-intiated.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес `https://api.spaceiq.com`.

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в формате `https://api.spaceiq.com/saml/<instanceid>/callback`.

    > [!NOTE]
    > Замените эти значения фактическими URL-адресом ответа и идентификатором, как описано позже в этом руководстве.

5. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Загрузить**, чтобы загрузить требуемый **сертификат (Base64)** из предложенных вариантов, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

6. Скопируйте требуемые URL-адреса из раздела **Настройка SpaceIQ**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    b. Идентификатор Azure AD

    c. URL-адрес выхода.

### <a name="configure-spaceiq-single-sign-on"></a>Настройка единого входа SpaceIQ

1. Откройте новое окно браузера и войдите в среду SpaceIQ в качестве администратора.

1. После входа щелкните значок головоломки в правом верхнем углу, а затем **Интеграции**.

    ![Параметры учетной записи](./media/spaceiq-tutorial/setting1.png) 

1. В разделе **All PROVISIONING & SSO** (Все подготовки и единый вход), щелкните плитку **Azure**, чтобы добавить экземпляр Azure в качестве поставщика удостоверений.

    ![Значок SAML](./media/spaceiq-tutorial/setting2.png)

1. В диалоговом окне **единого входа** сделайте следующее.

    ![Параметры проверки подлинности SAML](./media/spaceiq-tutorial/setting3.png)

    a. В текстовом поле **SAML Issuer URL** (URL-адрес издателя SAML) вставьте значение **Идентификатор Azure AD** из окна настройки приложения Azure AD.

    b. Скопируйте значение **SAML CallBack Endpoint URL (read-only)** (URL-адрес конечной точки обратного вызова SAML (только для чтения)) и вставьте его в поле **URL-адрес ответа** на портале Azure в разделе **Базовая конфигурация SAML**.

    c. Скопируйте значение **SAML Audience URI (read-only)** (URL аудитории SAML (только для чтения)) и вставьте его в поле **URL-адрес ответа** на портале Azure в разделе **Базовая конфигурация SAML**.

    d. Откройте загруженный сертификат в блокноте, скопируйте его содержимое и вставьте его в текстовое поле **Сертификат X.509**.

    д. Выберите команду **Сохранить**.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    а. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите `brittasimon@yourcompanydomain.extension`.  
    Например BrittaSimon@contoso.com.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к SpaceIQ.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем **SpaceIQ**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **SpaceIQ**.

    ![Ссылка на SpaceIQ в списке "Приложения"](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-spaceiq-test-user"></a>Создание тестового пользователя SpaceIQ

В этом разделе описано, как создать пользователя Britta Simon в приложении SpaceIQ. Чтобы добавить пользователей на платформу SpaceIQ, обратитесь к[группе поддержки SpaceIQ](mailto:eng@spaceiq.com) . Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку SpaceIQ на панели доступа, вы автоматически войдете в SpaceIQ, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что такое условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

