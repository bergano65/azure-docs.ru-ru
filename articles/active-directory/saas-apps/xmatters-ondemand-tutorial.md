---
title: Руководство по Интеграция Azure Active Directory с xMatters OnDemand | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в xMatters OnDemand.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ca0633db-4f95-432e-b3db-0168193b5ce9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: e8ae31122d59238ac104d7d873cf56f32977c9af
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67086502"
---
# <a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>Руководство по Интеграция Azure Active Directory с xMatters OnDemand

В этом руководстве описано, как интегрировать xMatters OnDemand с Azure Active Directory (Azure AD).
Интеграция xMatters OnDemand с Azure AD обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к xMatters OnDemand.
* Вы можете включить автоматический вход пользователей в xMatters OnDemand (единый вход) с помощью учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с xMatters OnDemand, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/));
* подписка xMatters OnDemand с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* xMatters OnDemand поддерживает единый вход, инициированный **IDP**.

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>Добавление xMatters OnDemand из коллекции

Чтобы настроить интеграцию xMatters OnDemand с Azure AD, необходимо добавить xMatters OnDemand из коллекции в список управляемых приложений SaaS.

**Чтобы добавить xMatters OnDemand из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **xMatters OnDemand**, выберите **xMatters OnDemand** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![xMatters OnDemand в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в xMatters OnDemand с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем xMatters OnDemand.

Чтобы настроить и проверить единый вход Azure AD в xMatters OnDemand, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в xMatters OnDemand](#configure-xmatters-ondemand-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя xMatters OnDemand](#create-xmatters-ondemand-test-user)** требуется для того, чтобы в xMatters OnDemand существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в xMatters OnDemand, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **xMatters OnDemand** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. На странице **Настройка единого входа с помощью SAML** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения xMatters OnDemand](common/idp-intiated.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в таком формате:

    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au/`|
    | `https://<companyname>.cs1.xmatters.com/`|
    | `https://<companyname>.xmatters.com/`|
    | `https://www.xmatters.com`|
    | `https://<companyname>.xmatters.com.au/`|
    | |

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в таком формате:

    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au`|
    | `https://<companyname>.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.cs1.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.au1.xmatters.com.au/<instancename>`|
    | |

    > [!NOTE]
    > Эти значения приведены для примера. Измените их на фактические значения идентификатора и URL-адреса ответа. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов xMatters OnDemand](https://www.xmatters.com/company/contact-us/). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

5. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Загрузить**, чтобы загрузить требуемый **сертификат (Base64)** из предложенных вариантов, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

    > [!IMPORTANT]
    > Вам необходимо отправить сертификат [группе поддержки xMatters OnDemand](https://www.xmatters.com/company/contact-us/). Служба поддержки xMatters должна загрузить сертификат, чтобы вы могли завершить настройку единого входа.

6. Скопируйте требуемые URL-адреса из раздела **Настройка xMatters OnDemand**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    b. Идентификатор Azure AD

    c. URL-адрес выхода.

### <a name="configure-xmatters-ondemand-single-sign-on"></a>Настройка единого входа в xMatters OnDemand

1. В другом окне веб-браузера войдите на корпоративный сайт xMatters OnDemand в качестве администратора.

2. В верхней части панели инструментов щелкните **Admin** (Администратор) и выберите **Company Details** (Сведения о компании) на панели навигации слева.

    ![Администратор](./media/xmatters-ondemand-tutorial/IC776795.png "Администратор")

3. На странице **Настройка SAML** сделайте следующее:

    ![Настройка SAML](./media/xmatters-ondemand-tutorial/IC776796.png "Настройка SAML")

    a. Выберите **Включить SAML**.

    b. В текстовое поле **Идентификатор поставщика удостоверений** вставьте значение **идентификатора Azure AD**, скопированное на портале Azure.

    c. В текстовое поле **URL-адрес для единого входа** вставьте значение **URL-адреса входа**, скопированное на портале Azure.

    d. В текстовое поле **Single Logout URL** (URL-адрес единого выхода) вставьте значение **URL-адреса выхода**, скопированное на портале Azure.

    д. В верхней части страницы «Информация о компании» нажмите кнопку **Сохранить изменения**.

    ![Сведения о компании](./media/xmatters-ondemand-tutorial/IC776797.png "Сведения о компании")

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD 

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    а. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите brittasimon@yourcompanydomain.extension. Например BrittaSimon@contoso.com.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к xMatters OnDemand.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем **xMatters OnDemand**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. Из списка приложений выберите **xMatters OnDemand**.

    ![Ссылка на xMatters OnDemand в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-xmatters-ondemand-test-user"></a>Создание тестового пользователя xMatters OnDemand

В рамках этого раздела создается пользователь с именем Britta Simon в xMatters OnDemand.

**Если необходимо создать пользователя вручную, выполните следующие действия:**

1. Войдите в клиент **xMatters OnDemand**.

2. Откройте вкладку **Пользователи**, а затем выберите **Добавить пользователя**.

    ![Пользователи](./media/xmatters-ondemand-tutorial/IC781048.png "Пользователи")

3. В разделе **Добавление пользователя** сделайте следующее:

    ![Добавление пользователя](./media/xmatters-ondemand-tutorial/IC781049.png "Добавление пользователя")

    a. Установите флажок **Активно**.

    b. В текстовое поле **User ID** (Идентификатор пользователя) введите идентификатор пользователя, например Brittasimon@contoso.com.

    c. В текстовое поле **First Name** (Имя) введите имя пользователя, например Britta.

    d. В текстовое поле **Last Name** (Фамилия) введите фамилию пользователя, например Simon.

    д. В текстовое поле **Site** (Сайт) введите действительный сайт учетной записи Azure AD, которую необходимо подготовить.

    Е. Выберите команду **Сохранить**.

### <a name="test-single-sign-on"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку xMatters OnDemand на Панели доступа, вы автоматически войдете в приложение xMatters OnDemand, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что такое условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

