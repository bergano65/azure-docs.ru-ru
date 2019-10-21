---
title: Руководство по интеграции единого входа Azure Active Directory с SAP Cloud for Customer | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в SAP Cloud for Customer.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 90154dab-eba2-4563-bcf0-f2acc797ea97
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 837787d375a7570b7daf0a149960ca0020bcdced
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72264065"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-cloud-for-customer"></a>Руководство по интеграции единого входа Azure Active Directory с SAP Cloud for Customer

В этом учебнике описано, как интегрировать SAP Cloud for Customer с Azure Active Directory (Azure AD). Интеграция SAP Cloud for Customer с Azure AD обеспечивает следующие преимущества:

* C помощью Azure AD вы можете контролировать доступ к приложению SAP Cloud for Customer.
* Вы можете включить автоматический вход пользователей в SAP Cloud for Customer с помощью учетных записей Azure AD.
* Централизованное управление учетными записями через портал Azure.

Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее:

* подписка Azure AD Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* Подписка SAP Cloud for Customer с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* SAP Cloud for Customer поддерживает единый вход, инициированный **поставщиком услуг**.

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>Добавление SAP Cloud for Customer из коллекции

Чтобы настроить интеграцию SAP Cloud for Customer с Azure AD, необходимо добавить SAP Cloud for Customer из коллекции в список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в поле поиска введите **SAP Cloud for Customer**.
1. Выберите **SAP Cloud for Customer** на панели результатов, а затем добавьте приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-cloud-for-customer"></a>Настройка и проверка единого входа Azure AD для SAP Cloud for Customer

Настройте и проверьте единый вход Azure Active Directory в SAP Cloud for Customer с помощью тестового пользователя **B. Simon**. Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в SAP Cloud for Customer.

Чтобы настроить и проверить единый вход Azure AD в SAP Cloud for Customer, выполните действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
    1. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD с помощью пользователя B.Simon.
    1. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить пользователю B.Simon использовать единый вход Azure AD.
1. **[Настройка единого входа в SAP Cloud for Customer](#configure-sap-cloud-for-customer-sso)** необходима, чтобы настроить параметры единого входа на стороне приложения.
    1. **[Создание тестового пользователя SAP Cloud for Customer](#create-sap-cloud-for-customer-test-user)** требуется для того, чтобы в SAP Cloud for Customer существовал пользователь B. Simon, связанный с одноименным пользователем в Azure AD.
1. **[Проверка единого входа](#test-sso)** необходима, чтобы убедиться в корректной работе конфигурации.

## <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **SAP Cloud for Customer** найдите раздел **Управление** и выберите **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

   ![Правка базовой конфигурации SAML](common/edit-urls.png)

1. На странице **Базовая конфигурация SAML** введите значения следующих полей.

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<server name>.crm.ondemand.com`.

    b. В текстовом поле **Идентификатор (сущности)** введите URL-адрес в следующем формате: `https://<server name>.crm.ondemand.com`.

    > [!NOTE]
    > Эти значения приведены для примера. Необходимо обновить эти значения действующим URL-адресом для входа и идентификатором. Чтобы получить эти значения, обратитесь к [группе поддержки клиентов SAP Cloud for Customer](https://www.sap.com/about/agreements.sap-cloud-services-customers.html). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

1. Приложение SAP Cloud for Customer ожидает проверочные утверждения SAML в определенном формате, который требует добавить сопоставления настраиваемых атрибутов в вашу конфигурацию атрибутов токена SAML. На следующем снимке экрана показан список атрибутов по умолчанию. Нажмите кнопку **Изменить**, чтобы открыть диалоговое окно "Атрибуты пользователя".

    ![image](common/edit-attribute.png)

1. В разделе **Атрибуты пользователя** в диалоговом окне **User Attributes & Claims** (Атрибуты пользователя и утверждения) выполните следующие действия.

    a. Щелкните **значок редактирования**, чтобы открыть диалоговое окно **Управление утверждениями пользователя**.

    ![image](./media/sap-customer-cloud-tutorial/tutorial_usermail.png)

    ![image](./media/sap-customer-cloud-tutorial/tutorial_usermailedit.png)

    b. Выберите **Преобразование** в качестве **источника**.

    c. Из списка **Преобразование** выберите **ExtractMailPrefix()**.

    d. В списке **Параметр 1** выберите атрибут пользователя, который вы хотите использовать в своей реализации.
    Например, если в качестве уникального идентификатора пользователя вы хотите использовать EmployeeID и сохранили значение атрибута в ExtensionAttribute2, выберите user.extensionattribute2.

    д. Выберите команду **Сохранить**.

1. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** найдите элемент **XML метаданных федерации** и выберите **Скачать**, чтобы скачать сертификат и сохранить его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

1. Скопируйте требуемый URL-адрес из раздела **Настройка SAP Cloud for Customer**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

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

В этом разделе описано, как включить единый вход Azure для пользователя B. Simon, предоставив этому пользователю доступ к SAP Cloud for Customer.

1. На портале Azure выберите **Корпоративные приложения**, а затем —**Все приложения**.
1. В списке приложений выберите **SAP Cloud for Customer**.
1. На странице "Обзор" приложения найдите раздел **Управление** и выберите **Пользователи и группы**.

   ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Ссылка "Добавить пользователя"](common/add-assign-user.png)

1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
1. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.
1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

## <a name="configure-sap-cloud-for-customer-sso"></a>Настройка единого входа в SAP Cloud for Customer

1. Откройте окно веб-браузера и войдите на сайт своей компании SAP Cloud for Customer как администратор.

2. В левой части меню щелкните  **Identity Providers** (Поставщики удостоверений) > **Corporate Identity Providers** (Поставщики корпоративных удостоверений) > **Add** (Добавить), а во всплывающем окне добавьте имя поставщика удостоверений, например **Azure AD**, нажмите кнопку **Save** (Сохранить), а затем щелкните **SAML 2.0 Configuration** (Настройка SAML 2.0).

    ![Настройка SAP](./media/sap-customer-cloud-tutorial/configure01.png)

3. В разделе **SAML 2.0 Configuration** (Настройка SAML 2.0) выполните следующие действия:

    ![Настройка SAP](./media/sap-customer-cloud-tutorial/configure02.png)

    a. Щелкните **Browse** (Обзор), чтобы передать XML-файл метаданных федерации, скачанный на портале Azure.

    b. После успешной отправки XML-файла указанные ниже значения заполнятся автоматически, после чего следует нажать кнопку **Save** (Сохранить).

### <a name="create-sap-cloud-for-customer-test-user"></a>Создание тестового пользователя в SAP Cloud for Customer

Чтобы пользователи Azure AD могли входить в SAP Cloud for Customer, их следует подготовить в SAP Cloud for Customer. В SAP Cloud for Customer подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Войдите в SAP Cloud for Customer в качестве администратора безопасности.

2. В левой части меню щелкните  **Users & Authorizations** (Пользователи и авторизация) > **User Management** (Управление пользователями) > **Add User** (Добавить пользователя).

    ![Настройка SAP](./media/sap-customer-cloud-tutorial/configure03.png)

3. В разделе **Add New User** (Добавить нового пользователя) выполните следующие действия.

    ![Настройка SAP](./media/sap-customer-cloud-tutorial/configure04.png)

    a. В текстовом поле **First Name** (Имя) введите имя пользователя, например **B**.

    b. В текстовом поле **Last Name** (Фамилия) введите фамилию пользователя, например **Simon**.

    c. В текстовом поле **E-Mail** (Адрес электронной почты) введите адрес электронной почты пользователя, например `B.Simon@contoso.com`.

    d. В текстовом поле **Last Name** (Фамилия) введите фамилию пользователя, например **B. Simon**.

    д. Выберите требуемый **тип пользователя**.

    Е. Выберите параметр **Account Activation** (Активация учетной записи) в соответствии с требованиями.

## <a name="test-sso"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент SAP Cloud for Customer на панели доступа, вы автоматически войдете в приложение SAP Cloud for Customer, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Попробуйте использовать приложение SAP Cloud for Customer с Azure AD](https://aad.portal.azure.com/)

