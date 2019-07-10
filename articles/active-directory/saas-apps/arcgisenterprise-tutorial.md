---
title: Руководство по Интеграция Azure Active Directory с ArcGIS Enterprise | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory и ArcGIS Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 24809e9d-a4aa-4504-95a9-e4fcf484f431
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/28/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9642dc67a3707862009efa59d99f0cef54b9b878
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67106689"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-enterprise"></a>Руководство по Интеграция Azure Active Directory с ArcGIS Enterprise

В этом руководстве описано, как интегрировать ArcGIS Enterprise с Azure Active Directory (Azure AD).
Интеграция ArcGIS Enterprise с Azure AD обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к ArcGIS Enterprise.
* Вы можете включить автоматический вход пользователей в ArcGIS Enterprise (единый вход) с помощью учетных записей Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с ArcGIS Enterprise, вам потребуются:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* Подписка ArcGIS Enterprise с поддержкой единого входа

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.



* ArcGIS Enterprise поддерживает единый вход инициированного **пакета обновления и выдающей точки распространения**.
* ArcGIS Enterprise поддерживает **JIT**-подготовку пользователей.


## <a name="adding-arcgis-enterprise-from-the-gallery"></a>Добавление ArcGIS Enterprise из коллекции

Чтобы настроить интеграцию ArcGIS Enterprise в Azure AD, необходимо добавить ArcGIS Enterprise из коллекции в список управляемых приложений SaaS.

**Чтобы добавить ArcGIS Enterprise из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **ArcGIS Enterprise**, выберите **ArcGIS Enterprise** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![ArcGIS Enterprise в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в [название приложения] с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в [название приложения].

Чтобы настроить и проверить единый вход Azure AD в [название приложения], вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в ArcGIS Enterprise](#configure-arcgis-enterprise-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя ArcGIS Enterprise](#create-arcgis-enterprise-test-user)** требуется для того, чтобы в ArcGIS Enterprise существовал пользователь Britta Simon, связанный с соответствующим пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в [название приложения], выполните следующие действия.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **ArcGIS Enterprise** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. Если вы хотите настроить приложение в инициируемом режиме **выдающейся точки распространения**, в разделе **Базовая конфигурация SAML** выполните следующие действия.

    ![Сведения о домене и URL-адресах в приложении ArcGIS Enterprise](common/idp-intiated.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в формате `<EXTERNAL_DNS_NAME>.portal`.

    б) В текстовом поле **URL-адрес ответа** введите URL-адрес в формате `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin2`.

    c. Чтобы настроить приложение для работы в режиме, инициируемом **поставщиком услуг**, щелкните **Задать дополнительные URL-адреса** и выполните следующие действия.

    ![Сведения о домене и URL-адресах в приложении ArcGIS Enterprise](common/metadata-upload-additional-signon.png)

    В текстовом поле **URL-адрес входа** введите URL-адрес в формате `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin`.

    > [!NOTE]
    > Эти значения приведены для примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Чтобы получить эти значения, обратитесь в [группу поддержки клиентов ArcGIS Enterprise](mailto:support@esri.com). Вы получите значение идентификатора из **раздела "Назначить поставщика удостоверений"** , который описан далее в этом руководстве.

5. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** нажмите кнопку "Копировать", чтобы копировать **URL-адрес метаданных федерации приложений** и сохранить его на компьютере.

    ![Ссылка для скачивания сертификата](common/copy-metadataurl.png)

### <a name="configure-arcgis-enterprise-single-sign-on"></a>Настройка единого входа в ArcGIS Enterprise

1. В другом окне веб-браузера войдите на корпоративный сайт ArcGIS Enterprise в качестве администратора.

2. Выберите **Organization > Edit Settings** (Организация > Изменить параметры).

    ![Настройка ArcGIS Enterprise](./media/arcgisenterprise-tutorial/configure1.png)

3. Выберите вкладку **Security** (Безопасность).

    ![Настройка ArcGIS Enterprise](./media/arcgisenterprise-tutorial/configure2.png)

4. Прокрутите вниз до раздела **Enterprise Logins via SAML** (Корпоративный вход через SAML) и выберите **Set Enterprise Login** (Настроить корпоративный вход).

    ![Настройка ArcGIS Enterprise](./media/arcgisenterprise-tutorial/configure3.png)

5. В разделе **Set Identity Provider** (Назначение поставщика удостоверений) выполните следующие действия:

    ![Настройка ArcGIS Enterprise](./media/arcgisenterprise-tutorial/configure4.png)

    a. В текстовом поле **Имя** укажите имя, например **Azure Active Directory Test**.

    b. В текстовое поле **URL** (URL-адрес) вставьте значение **URL-адреса метаданных федерации приложения**, скопированное на портале Azure.

    c. Щелкните ссылку **Show advanced settings** (Показать дополнительные параметры), скопируйте значение **идентификатора сущности** и вставьте его в текстовое поле **Идентификатор** в разделе **Домены и URL-адреса приложения ArcGIS Enterprise** на портале Azure.
    
    ![Настройка ArcGIS Enterprise](./media/arcgisenterprise-tutorial/configure5.png)

    d. Нажмите кнопку **Update Identity Provider** (Обновить поставщика удостоверений).

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

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к ArcGIS Enterprise.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **ArcGIS Enterprise**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. Из списка приложений введите и выберите **ArcGIS Enterprise**.

    ![Ссылка ArcGIS Enterprise в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-arcgis-enterprise-test-user"></a>Создание тестового пользователя ArcGIS Enterprise

В этом разделе вы создадите в ArcGIS Enterprise пользователя Britta Simon. Приложение ArcGIS Enterprise поддерживает JIT-подготовку пользователей, которая включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Если пользователь еще не существует в ArcGIS Enterprise, его создадут после проверки подлинности.

> [!Note]
> Чтобы создать пользователя вручную, обратитесь в  [группу поддержки ArcGIS Enterprise](mailto:support@esri.com).

### <a name="test-single-sign-on"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

При нажатии на плитку "ArcGIS Enterprise" на панели доступа вы автоматически войдете в ArcGIS Enterprise, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что такое условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

