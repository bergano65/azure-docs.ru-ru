---
title: Руководство. Интеграция Azure Active Directory с Adobe Creative Cloud | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Adobe Creative Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: c199073f-02ce-45c2-b515-8285d4bbbca2
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.openlocfilehash: 4907179b9cc333103a898056ff951decf81c0f6c
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54826810"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-creative-cloud"></a>Руководство. Интеграция Azure Active Directory с Adobe Creative Cloud

В этом учебнике описано, как интегрировать приложение Adobe Creative Cloud с Azure Active Directory (Azure AD).
Интеграция Azure AD с приложением Adobe Creative Cloud обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к Adobe Creative Cloud.
* Вы можете включить автоматический вход пользователей в Adobe Creative Cloud (единый вход) с использованием учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с приложением Adobe Creative Cloud, вам потребуется:

* подписка Azure AD; Если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).
* Подписка Adobe Creative Cloud с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства проводится настройка и проверка единого входа Azure AD в тестовой среде.

* Adobe Creative Cloud поддерживает единый вход, инициированный **поставщиком услуг и поставщиком удостоверений**.

## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>Добавление Adobe Creative Cloud из коллекции

Чтобы настроить интеграцию Adobe Creative Cloud с Azure AD, необходимо добавить Adobe Creative Cloud из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Adobe Creative Cloud из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"](common/add-new-app.png)

4. В поле поиска введите **Adobe Creative Cloud**, выберите **Adobe Creative Cloud** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![Adobe Creative Cloud в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Adobe Creative Cloud с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Adobe Creative Cloud.

Чтобы настроить и проверить единый вход Azure AD в Adobe Creative Cloud, выполните действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в Adobe Creative Cloud](#configure-adobe-creative-cloud-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя Adobe Creative Cloud](#create-adobe-creative-cloud-test-user)** требуется для того, чтобы в Adobe Creative Cloud существовал пользователь Britta Simon, связанный с представлением этого же пользователя в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в Adobe Creative Cloud, выполните следующие действия:

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Adobe Creative Cloud** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. Если вы хотите настроить в режиме, инициируемом **IDP**, в разделе **Базовая конфигурация SAML** выполните следующие действия:

    ![Сведения о домене и URL-адресах единого входа приложения Adobe Creative Cloud](common/idp-intiated.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в формате `https://www.okta.com/saml2/service-provider/<token>`.

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<company name>.okta.com/auth/saml20/accauthlinktest`.

    > [!NOTE]
    > Эти значения приведены в качестве примера. Замените их фактическими значениями идентификатора и URL-адреса ответа. Чтобы получить эти значения, обратитесь к [группе поддержки клиентов Adobe Creative Cloud](https://www.adobe.com/au/creativecloud/business/teams/plans.html). Можно также обратиться к шаблонам, указанным в разделе **Базовая конфигурация SAML** на портале Azure.

5. Чтобы настроить приложение для работы в режиме, инициируемом **поставщиком услуг**, щелкните **Задать дополнительные URL-адреса** и выполните следующее действие:

    В текстовом поле **URL-адрес входа** введите значение в формате `https://adobe.com`.

    ![Сведения о домене и URL-адресах единого входа приложения Adobe Creative Cloud](common/metadata-upload-additional-signon.png)

6. Adobe Creative Cloud ожидает утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно в разделе **Атрибуты пользователя** на странице интеграции приложения. На странице **Настройка единого входа с помощью SAML** нажмите кнопку **Изменить**, чтобы открыть диалоговое окно **Атрибуты пользователя**.

    ![изображение](common/edit-attribute.png)

7. В разделе **Утверждения пользователя** диалогового окна **Атрибуты пользователя** настройте атрибут токена SAML, как показано на рисунке выше, и выполните следующие действия.
 
    | ИМЯ | Исходный атрибут|
    |----- | --------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | Email | user.mail

    a. Щелкните **Добавить новое утверждение**, чтобы открыть диалоговое окно **Управление утверждениями пользователя**.

    ![изображение](common/new-save-attribute.png)

    ![изображение](common/new-attribute-details.png)

    b. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.

    c. Оставьте пустым поле **Пространство имен**.

    d. В качестве источника выберите **Атрибут**.

    д. В списке **Атрибут источника** введите значение атрибута, отображаемое для этой строки.

    Е. Нажмите кнопку **ОК**.

    ж. Выберите команду **Сохранить**.

    > [!NOTE]
    > У пользователей должна быть действительная лицензия Office 365 ExO для заполнения значения утверждения из электронного письма в ответе SAML.

8. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Загрузить**, чтобы загрузить требуемый **сертификат (Base64)** из предложенных вариантов, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

9. Скопируйте требуемый URL-адрес из раздела **Настройка Adobe Creative Cloud**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    a. URL-адрес входа.

    b. Идентификатор Azure AD.

    c. URL-адрес выхода

### <a name="configure-adobe-creative-cloud-single-sign-on"></a>Подписка Adobe Creative Cloud с поддержкой единого входа

1. В другом окне веб-браузера войдите в [консоль администрирования Adobe](https://adminconsole.adobe.com) от имени администратора.

2. На панели навигации вверху выберите **Параметры**, а затем щелкните **Удостоверение**. Откроется список доменов. Щелкните ссылку **Настройка** для вашего домена. В разделе **Single Sign On Configuration Required** (Обязательная конфигурация единого входа) выполните следующие действия. Дополнительные сведения см. в статье о [настройке домена](https://helpx.adobe.com/enterprise/using/set-up-domain.html).

    ![Параметры](https://helpx.adobe.com/content/dam/help/en/enterprise/using/configure-microsoft-azure-with-adobe-sso/_jcr_content/main-pars/procedure_719391630/proc_par/step_3/step_par/image/edit-sso-configuration.png "Параметры")

    a. Щелкните **Browse** (Просмотр), чтобы найти и передать сертификат, загруженный из Azure AD, в качестве **сертификата поставщика удостоверений**.

    b. В текстовое поле **IDP issuer** (Издатель удостоверений) поместите значение **Идентификатор сущности SAML**, который вы скопировали из раздела **Настройка входа** на портале Azure.

    c. В текстовое поле **IDP Login URL** (URL-адрес входа поставщика удостоверений) поместите значение **URL-адрес службы единого входа SAML**, который вы скопировали из раздела **Настройка входа** на портале Azure.

    d. Для параметра **HTTP — Redirect** (Перенаправление HTTP) выберите вариант **IDP Binding** (Привязка к поставщику удостоверений).

    д. Для параметра **Email Address** (Адрес электронной почты) выберите вариант **User Login Setting** (Настройки входа пользователя).

    Е. Нажмите кнопку **Сохранить** .

3. Теперь на панели мониторинга отобразится XML-файл **Download Metadata** (Загрузить метаданные). Он содержит URL-адреса компании Adobe для описания сущности (EntityDescriptor) и URL-адреса службы утверждений (AssertionConsumerService). Откройте этот файл и перенесите настройки в приложение Azure AD.

    ![Настройка единого входа на стороне приложения](./media/adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Используйте значение EntityDescriptor, предоставленное компанией Adobe, в качестве значения для параметра **Identifier** (Идентификатор) в диалоговом окне **Configure App Settings** (Настройка параметров приложения).

    b. Используйте значение AssertionConsumerService, предоставленное компанией Adobe, в качестве значения для параметра **Reply URL** (URL-адрес ответа) в диалоговом окне **Configure App Settings** (Настройка параметров приложения).

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

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Adobe Creative Cloud.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **Adobe Creative Cloud**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Adobe Creative Cloud**.

    ![Ссылка на Adobe Creative Cloud в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-adobe-creative-cloud-test-user"></a>Создание тестового пользователя Adobe Creative Cloud

Чтобы пользователи Azure AD могли входить в Adobe Creative Cloud, они должны быть подготовлены для Adobe Creative Cloud. В случае Adobe Creative Cloud подготовка выполняется вручную.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Чтобы подготовить учетные записи пользователей, выполните следующие действия.

1. Войдите на сайт [консоли администрирования Adobe](https://adminconsole.adobe.com) от имени администратора.

2. Добавьте пользователя в консоль Adobe с помощью федеративного идентификатора и назначьте его профилю продукта. Подробные сведения о добавлении пользователей см. в статье, посвященной [добавлению пользователей в консоль администрирования Adobe](https://helpx.adobe.com/enterprise/using/users.html#Addusers) 

3. На этом этапе введите свой адрес электронной почты или имя участника-пользователя в форму регистрации Adobe, нажмите клавишу TAB, и вы должны попасть обратно в Azure AD через федеративные отношения:
    * Веб-доступ: www.adobe.com > "Вход"
    * В программе классического приложения > "Вход"
    * В приложении > "Справка" > "Вход"

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Adobe Creative Cloud на панели доступа, вы автоматически войдете в приложение Adobe Creative Cloud, для которого настроили единый вход. Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
  
- [Настройка домена (adobe.com)](https://helpx.adobe.com/enterprise/using/set-up-domain.html)
  
- [Настройка Azure для использования с единым входом Adobe (adobe.com)](https://helpx.adobe.com/enterprise/kb/configure-microsoft-azure-with-adobe-sso.html)