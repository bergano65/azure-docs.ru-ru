---
title: Руководство. Интеграция Azure Active Directory с AirWatch | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и AirWatch.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 96a3bb1c-96c6-40dc-8ea0-060b0c2a62e5
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.openlocfilehash: 83a3a6fee7446766973cc8fdca1129cdc2ff80d0
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/02/2019
ms.locfileid: "53974460"
---
# <a name="tutorial-azure-active-directory-integration-with-airwatch"></a>Руководство. Интеграция Azure Active Directory с AirWatch

В этом руководстве описано, как интегрировать AirWatch с Azure Active Directory (Azure AD).
Интеграция приложения AirWatch c Azure AD обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать, у кого есть доступ к приложению AirWatch.
* Вы можете включить автоматический вход пользователей в AirWatch (единый вход) с помощью учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с AirWatch, вам потребуется:

* подписка Azure AD; Если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).
* Подписка AirWatch с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства проводится настройка и проверка единого входа Azure AD в тестовой среде.

* AirWatch поддерживает единый вход, инициированный **поставщиком услуг**.

## <a name="adding-airwatch-from-the-gallery"></a>Добавление AirWatch из коллекции.

Чтобы настроить интеграцию приложения AirWatch с Azure AD, вам нужно добавить это приложение из коллекции в свой список управляемых приложений SaaS.

**Добавление приложения AirWatch из коллекции**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"](common/add-new-app.png)

4. В поле поиска введите **AirWatch**, выберите **AirWatch** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![AirWatch в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в приложение AirWatch с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в AirWatch.

Чтобы настроить и проверить единый вход Azure AD в AirWatch, выполните следующие действия:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в AirWatch](#configure-airwatch-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Создание тестового пользователя AirWatch](#create-airwatch-test-user)** требуется, чтобы создать в AirWatch пользователя Britta Simon, связанного с представлением этого пользователя в Azure AD.
5. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в AirWatch, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **AirWatch** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    ![Сведения о домене и URL-адресах единого входа приложения AirWatch](common/sp-identifier.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`.

    b. В текстовом поле **Идентификатор (сущности)** введите значение следующим образом: `AirWatch`

    > [!NOTE]
    > Это значение приведено для примера. Вместо него необходимо указать фактический URL-адрес для входа. Для получения этого значения обратитесь в [службу поддержки клиентов AirWatch](https://www.air-watch.com/company/contact-us/). Можно также обратиться к шаблонам, указанным в разделе **Базовая конфигурация SAML** на портале Azure.

5. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать нужный вам **XML метаданных федерации**, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

6. Скопируйте требуемый URL-адрес из раздела **Настройка AirWatch**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    a. URL-адрес входа.

    b. Идентификатор Azure AD.

    c. URL-адрес выхода

### <a name="configure-airwatch-single-sign-on"></a>Настройка единого входа в AirWatch

1. В другом окне веб-браузера войдите на корпоративный веб-сайт AirWatch с правами администратора.

2. На панели навигации слева щелкните **Accounts** (Учетные записи), а затем — **Administrators** (Администраторы).

   ![Администраторы](./media/airwatch-tutorial/ic791920.png "Администраторы")

3. Разверните меню **Settings** (Параметры) и выберите пункт **Directory Services** (Службы каталогов).

   ![Параметры](./media/airwatch-tutorial/ic791921.png "Параметры")

4. Откройте вкладку **User** (Пользователь), введите в текстовое поле **Base DN** (Базовое доменное имя) используемое доменное имя и щелкните **Save** (Сохранить).

   ![Пользователь](./media/airwatch-tutorial/ic791922.png "Пользователь")

5. Откройте вкладку **Server** (Сервер).

   ![Сервер](./media/airwatch-tutorial/ic791923.png "Сервер")

6. Выполните следующие действия:

    ![Передача](./media/airwatch-tutorial/ic791924.png "Передача")   

    a. Для параметра **Directory Type** (Тип каталога) выберите значение **None** (Нет).

    b. Установите флажок **Use SAML For Authentication**(Использовать SAML для проверки подлинности).

    c. Чтобы отправить скачанный сертификат, нажмите кнопку **Upload**(Отправить).

7. В разделе **Request** (Запрос) выполните следующие действия.

    ![Запрос](./media/airwatch-tutorial/ic791925.png "Запрос")  

    a. Для параметра **Request Binding Type** (Тип привязки запроса) выберите значение **POST**.

    b. На портале Azure на диалоговой странице **Configure single sign-on at Airwatch** (Настройка единого входа в AirWatch) скопируйте значение в поле **URL-адрес входа** и вставьте его в текстовое поле **Identity Provider Single Sign On URL** (URL-адрес единого входа для поставщика удостоверений).

    c. Для параметра **NameID Format** (Формат идентификатора имени) выберите значение **Email Address** (Адрес электронной почты).

    d. Выберите команду **Сохранить**.

8. Снова откройте вкладку **Пользователь** .

    ![Пользователь](./media/airwatch-tutorial/ic791926.png "Пользователь")

9. В разделе **Атрибут** выполните следующие действия.

    ![Атрибут](./media/airwatch-tutorial/ic791927.png "Атрибут")

    a. В текстовом поле **Object Identifier** (Идентификатор объекта) введите `http://schemas.microsoft.com/identity/claims/objectidentifier`.

    b. В текстовом поле **Username** (Имя пользователя) введите `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    c. В текстовом поле **Display Name** (Отображаемое имя) введите `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    d. В текстовом поле **First Name** (Имя) введите `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    д. В текстовое поле **Last Name** (Фамилия) введите `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.

    Е. В текстовое поле **Email** (Электронная почта) введите `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    ж. Выберите команду **Сохранить**.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD 

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее.

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    a. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon@yourcompanydomain.extension**.  
    Например, BrittaSimon@contoso.com

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе мы разрешим пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к AirWatch.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем **AirWatch**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **AirWatch**.

    ![Ссылка на AirWatch в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-airwatch-test-user"></a>Создание тестового пользователя AirWatch

Чтобы пользователи Azure AD могли выполнять вход в AirWatch, они должны быть подготовлены в AirWatch. В случае с AirWatch подготовка выполняется вручную.

**Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.**

1. Выполните вход на корпоративном веб-сайте **AirWatch** в качестве администратора.

2. На панели навигации слева щелкните **Accounts** (Учетные записи), а затем —**Users** (Пользователи).
  
   ![Пользователи](./media/airwatch-tutorial/ic791929.png "Пользователи")

3. В меню **Users** (Пользователи) выберите пункт **List View** (Представление списка), а затем щелкните **Add \> Add User** (Добавить > Добавить пользователя).
  
   ![Добавление пользователя](./media/airwatch-tutorial/ic791930.png "Добавление пользователя")

4. В диалоговом окне **Add / Edit User** (Добавление или изменение пользователя) выполните следующие действия.

   ![Добавление пользователя](./media/airwatch-tutorial/ic791931.png "Добавление пользователя")

   a. В текстовых полях **Username** (Имя пользователя), **Password** (Пароль), **Confirm Password** (Подтверждение пароля), **First Name** (Имя), **Last Name** (Фамилия) и **Email Address** (Адрес электронной почты) введите соответствующие данные действующей учетной записи Azure Active Directory, которую вы хотите подготовить.

   b. Выберите команду **Сохранить**.

> [!NOTE]
> Вы можете использовать любые другие средства создания учетной записи пользователя AirWatch или API, предоставляемые AirWatch для подготовки учетных записей пользователя AAD.

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку AirWatch на панели доступа, вы автоматически войдете в приложение AirWatch, для которого настроили единый вход. Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)