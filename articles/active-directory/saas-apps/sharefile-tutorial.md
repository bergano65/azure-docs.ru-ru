---
title: Руководство. Интеграция Azure Active Directory с Citrix ShareFile | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Citrix ShareFile.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/18/2020
ms.author: jeedes
ms.openlocfilehash: cfbb704799a1884c689bd0de547526a33f1ba7ce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88651927"
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Руководство. Интеграция Azure Active Directory с Citrix ShareFile

В этом руководстве описано, как интегрировать Citrix ShareFile с Azure Active Directory (Azure AD).
Интеграция Azure AD с Citrix ShareFile обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к Citrix ShareFile.
* Вы можете включить автоматический вход пользователей в Citrix ShareFile (единый вход) под учетной записью Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>предварительные требования

Чтобы настроить интеграцию Azure AD с Citrix ShareFile, вам потребуется:

* подписка Azure AD; (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* Подписка Citrix ShareFile с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Citrix ShareFile поддерживает единый вход, инициированный **поставщиком услуг**.
* После настройки Citrix ShareFile вы можете применить функцию управления сеансом, которая в реальном времени защищает конфиденциальные данные вашей организации от хищения и несанкционированного доступа. Управление сеансом является расширением функции условного доступа. [Узнайте, как применять управление сеансами с помощью Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-citrix-sharefile-from-the-gallery"></a>Добавление Citrix ShareFile из коллекции

Чтобы настроить интеграцию Citrix ShareFile с Azure AD, вам нужно добавить Citrix ShareFile из коллекции в список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в поле поиска введите **Citrix ShareFile**.
1. Выберите **Citrix ShareFile** в области результатов и добавьте это приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.

## <a name="configure-and-test-azure-ad-sso"></a>Настройка и проверка единого входа Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Citrix ShareFile с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Citrix ShareFile.

Чтобы настроить и проверить единый вход Azure AD в Citrix ShareFile, вам нужно выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
    
    * **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
    * **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
2. **[Настройка единого входа в Citrix ShareFile](#configure-citrix-sharefile-sso)** необходима, чтобы настроить параметры единого входа на стороне приложения.
    * **[Создание тестового пользователя Citrix ShareFile](#create-citrix-sharefile-test-user)** требуется для того, чтобы в Citrix ShareFile существовал пользователь Britta Simon, связанный с представлением этого же пользователя в Azure AD.
3. **[Проверка единого входа](#test-sso)** позволяет убедиться в правильности конфигурации.

### <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Citrix ShareFile** найдите раздел **Управление** и выберите **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

   ![Изменение базовой конфигурации SAML](common/edit-urls.png)

1. На странице **Базовая конфигурация SAML** введите значения следующих полей.

    а. В текстовое поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<tenant-name>.sharefile.com/saml/login`.

    b. В текстовом поле **Identifier (Entity ID)** (Идентификатор (ИД сущности)) введите URL-адрес в следующем формате.

    - `https://<tenant-name>.sharefile.com`
    - `https://<tenant-name>.sharefile.com/saml/info`
    - `https://<tenant-name>.sharefile1.com/saml/info`
    - `https://<tenant-name>.sharefile1.eu/saml/info`
    - `https://<tenant-name>.sharefile.eu/saml/info`

    c. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: .
    
    - `https://<tenant-name>.sharefile.com/saml/acs`
    - `https://<tenant-name>.sharefile.eu/saml/<URL path>`
    - `https://<tenant-name>.sharefile.com/saml/<URL path>`

    > [!NOTE]
    > Эти значения приведены для примера. Укажите вместо них фактические значения URL-адреса для входа, идентификатора и URL-адреса ответа. Чтобы получить эти значения, обратитесь к [группе поддержки клиентов Citrix ShareFile](https://www.citrix.co.in/products/citrix-content-collaboration/support.html). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

4. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Загрузить**, чтобы загрузить требуемый **сертификат (Base64)** из предложенных вариантов, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

6. Скопируйте требуемый URL-адрес из раздела **Настройка Citrix ShareFile**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а. URL-адрес входа.

    b. Идентификатор Azure AD.

    c. URL-адрес выхода.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD 

В этом разделе описано, как на портале Azure создать тестового пользователя с именем B.Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.
1. В верхней части экрана выберите **Новый пользователь**.
1. В разделе **Свойства пользователя** выполните следующие действия.
   1. В поле **Имя** введите `B.Simon`.  
   1. В поле **Имя пользователя** введите username@companydomain.extension. Например, `B.Simon@contoso.com`.
   1. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.
   1. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю B. Simon использовать единый вход Azure, предоставив этому пользователю доступ к Citrix ShareFile.

1. На портале Azure выберите **Корпоративные приложения**, а затем — **Все приложения**.
1. В списке приложений выберите **Citrix ShareFile**.
1. На странице "Обзор" приложения найдите раздел **Управление** и выберите **Пользователи и группы**.

   ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Ссылка "Добавить пользователя"](common/add-assign-user.png)

1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
1. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.
1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

## <a name="configure-citrix-sharefile-sso"></a>Настройка единого входа в Citrix ShareFile

1. В другом окне браузера под учетной записью администратора войдите на сайт **Citrix ShareFile** своей организации.

1. В меню слева щелкните **Settings Icon** -> **Security** -> **Login & Security Policy** (значок параметров > Безопасность > Вход и политика безопасности).
   
    ![Администрирование учетной записи](./media/sharefile-tutorial/settings-security.png "Администрирование учетной записи")

1. На странице **Настройка единого входа и SAML 2.0** в разделе **Основные параметры** выполните следующие действия:
   
    ![Единый вход](./media/sharefile-tutorial/saml-configuration.png "Единый вход")
   
    а. Выберите значение **YES** (Да) для параметра **Enable SAML** (Включить SAML).

    b. Скопируйте значение **издателя ShareFile и идентификатора сущности** и вставьте его в текстовое поле **URL-адрес идентификатора** в диалоговом окне **Базовая конфигурация SAML** на портале Azure.
    
    c. В текстовое поле **Your IDP Issuer/ Entity ID** (Издатель поставщика удостоверений/идентификатор сущности) вставьте значение **Идентификатор Azure AD**, скопированное на портале Azure.

    d. Нажмите **Изменить** рядом с полем **X.509 Certificate** (Сертификат X.509) и передайте сертификат, скачанный с портала Azure AD.
    
    д) В текстовое поле **Login URL** (URL-адрес входа) вставьте значение **URL-адреса входа**, скопированное с портала Azure.
    
    е) В текстовое поле **Logout URL** (URL-адрес выхода) вставьте значение **URL-адреса выхода**, скопированное с портала Azure.

5. Щелкните **Сохранить** на портале управления Citrix ShareFile.

## <a name="create-citrix-sharefile-test-user"></a>Создание тестового пользователя Citrix ShareFile

1. Войдите в клиент **Citrix ShareFile**.

2. Щелкните **People** -> **Manage Users Home** -> **Create New Users** -> **Create Employee** (Люди > Управление домашним ресурсом пользователей > Создать новых пользователей > Создать сотрудника).
   
    ![Создание сотрудника](./media/sharefile-tutorial/create-user.png "Создать сотрудника")

3. В разделе **Basic Information** (Базовые сведения) выполните следующие действия.
   
    ![Основные сведения](./media/sharefile-tutorial/user-form.png "Основные сведения")
   
    а. В текстовое поле **First Name** (Имя) введите **имя пользователя**, например **Britta**.
   
    b.  В текстовое поле **Last Name** (Фамилия) введите **фамилию**, например **Simon**.
   
    c. В текстовое поле **Email Address** (Электронная почта) введите адрес электронной почты пользователя Britta Simon: **brittasimon\@contoso.com**.

4. Нажмите кнопку **Add User**(Добавить пользователя).
  
    >[!NOTE]
    >Владелец учетной записи Azure AD получит по электронной почте сообщение со ссылкой для активации учетной записи. Вы можете использовать любые другие инструменты создания учетной записи пользователя Citrix ShareFile или API, предоставляемые Citrix ShareFile для подготовки учетных записей пользователей AAD.

## <a name="test-sso"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Citrix ShareFile на панели доступа, вы автоматически войдете в приложение Citrix ShareFile, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

