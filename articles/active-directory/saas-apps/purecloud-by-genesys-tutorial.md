---
title: Руководство по Интеграция Azure Active Directory с PureCloud от Genesys | Документация Майкрософт
description: В этой статье объясняется, как настроить единый вход между Azure Active Directory и PureCloud от Genesys.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e16a46db-5de2-4681-b7e0-94c670e3e54e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.openlocfilehash: 764ad8f1ca19238e1986d1d187d19c405963a832
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "65868840"
---
# <a name="tutorial-azure-active-directory-integration-with-purecloud-by-genesys"></a>Руководство по Интеграция Azure Active Directory с PureCloud от Genesys

В этом руководстве описано, как интегрировать PureCloud от Genesys с Azure Active Directory (Azure AD).
Интеграция PureCloud от Genesys с Azure AD дает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к PureCloud от Genesys.
* Вы можете включить автоматический вход для пользователей в PureCloud от Genesys (единый вход) с помощью учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с PureCloud от Genesys, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* подписка PureCloud от Genesys с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* PureCloud от Genesys поддерживает единый вход, инициируемый **поставщиком услуг и поставщиком удостоверений**.

## <a name="adding-purecloud-by-genesys-from-the-gallery"></a>Добавление PureCloud от Genesys из коллекции

Чтобы настроить интеграцию PureCloud от Genesys с Azure AD, необходимо добавить PureCloud от Genesys из коллекции в список управляемых приложений SaaS.

**Чтобы добавить PureCloud от Genesys из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **PureCloud от Genesys**, выберите **PureCloud от Genesys** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![PureCloud от Genesys в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в PureCloud от Genesys с использованием тестового пользователя **Britta Simon**.
Чтобы обеспечить работу единого входа, нужно установить связь между пользователем Azure AD и соответствующим пользователем в PureCloud от Genesys.

Чтобы настроить и проверить единый вход Azure AD в PureCloud от Genesys, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в PureCloud от Genesys](#configure-purecloud-by-genesys-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя PureCloud от Genesys](#create-purecloud-by-genesys-test-user)** требуется для того, чтобы в PureCloud от Genesys существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в PureCloud от Genesys, выполните следующие действия:

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **PureCloud от Genesys** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. Если вы хотите настроить приложение в режиме, инициируемом **поставщиком удостоверений**, в разделе **Базовая конфигурация SAML** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения PureCloud от Genesys](common/idp-intiated.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес для своего региона:

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.au/saml` |

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес для своего региона:

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.com.au/saml` |

5. Чтобы настроить приложение для работы в режиме, инициируемом **поставщиком услуг**, щелкните **Задать дополнительные URL-адреса** и выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения PureCloud от Genesys](common/metadata-upload-additional-signon.png)

    В текстовом поле **URL-адрес входа** введите URL-адрес для своего региона:
    
    | |
    |--|
    | `https://login.mypurecloud.com` |
    | `https://login.mypurecloud.de` |
    | `https://login.mypurecloud.jp` |
    | `https://login.mypurecloud.ie` |
    | `https://login.mypurecloud.com.au` |

6. Приложение PureCloud от Genesys ожидает проверочные утверждения SAML в определенном формате, поэтому вам требуется добавить настраиваемые сопоставления атрибутов в вашу конфигурацию атрибутов токена SAML. На следующем снимке экрана показан список атрибутов по умолчанию. Щелкните значок **Изменить**, чтобы открыть диалоговое окно  **Атрибуты пользователя** .

    ![image](common/edit-attribute.png)

7. В дополнение к описанному выше приложение PureCloud от Genesys ожидает несколько дополнительных атрибутов в ответе SAML. В разделе **Утверждения пользователя** диалогового окна **Атрибуты пользователя** выполните следующие действия, чтобы добавить атрибут токена SAML, как показано в приведенной ниже таблице.

    | ИМЯ | Исходный атрибут|
    | ---------------| --------------- |
    | Email | user.userprinicipalname |
    | OrganizationName | `Your organization name` |

    a. Щелкните **Добавить новое утверждение**, чтобы открыть диалоговое окно **Управление утверждениями пользователя**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.

    c. Оставьте пустым поле **Пространство имен**.

    d. В качестве источника выберите **Атрибут**.

    д. В списке **Атрибут источника** введите значение атрибута, отображаемое для этой строки.

    Е. Нажмите кнопку **ОК**.

    ж. Выберите команду **Сохранить**.

8. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Загрузить**, чтобы загрузить требуемый **сертификат (Base64)** из предложенных вариантов, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

9. Скопируйте требуемый URL-адрес из раздела **Настройка PureCloud от Genesys**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    b. Идентификатор Azure AD

    c. URL-адрес выхода.

### <a name="configure-purecloud-by-genesys-single-sign-on"></a>Настройка единого входа в PureCloud от Genesys

1. В другом окне веб-браузера войдите в приложение PureCloud от Genesys с правами администратора.

2. Щелкните пункт **Admin** (Администратор) вверху и перейдите к пункту **Single Sign-on** (Единый вход) в разделе **Integrations** (Интеграции).

    ![Настройка единого входа](./media/purecloud-by-genesys-tutorial/configure01.png)

3. Перейдите на вкладку **ADFS/Azure AD (Premium)** и выполните следующие действия:

    ![Настройка единого входа](./media/purecloud-by-genesys-tutorial/configure02.png)

    a. Щелкните **Browse** (Обзор), чтобы выбрать сертификат в кодировке Base64, который был скачан с портала Azure, в поле **ADFS Certificate** (Сертификат ADFS).

    b. В текстовое поле **ADFS Issuer URI** (URI издателя ADFS) вставьте **идентификатор Azure AD**, скопированный на портале Azure.

    c. В текстовое поле **Target URI** (URI назначения) вставьте **URL-адрес входа**, скопированный на портале Azure.

    d. Чтобы задать **идентификатор проверяющей стороны**, перейдите на портал Azure, на странице интеграции с приложением **PureCloud от Genesys** выберите вкладку **Свойства** и скопируйте значение **Идентификатор приложения**. Вставьте его в текстовое поле **Relying Party Identifier** (Идентификатор проверяющей стороны). 

    ![Настройка единого входа](./media/purecloud-by-genesys-tutorial/configure06.png)

    д. Нажмите кнопку **Сохранить**   

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

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к PureCloud от Genesys.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения** и **PureCloud от Genesys**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **PureCloud от Genesys**.

    ![Ссылка на PureCloud от Genesys в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-purecloud-by-genesys-test-user"></a>Создание тестового пользователя PureCloud от Genesys

Чтобы пользователи Azure AD могли выполнять вход в PureCloud от Genesys, их нужно подготовить к работе в PureCloud от Genesys. В PureCloud от Genesys такая подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Войдите в PureCloud от Genesys с правами администратора.

2. Щелкните **Admin** (Администратор) вверху и перейдите к пункту **People** (Пользователи) в разделе **People & Permissions** (Пользователи и разрешения).

    ![Настройка единого входа](./media/purecloud-by-genesys-tutorial/configure03.png)

3. На странице "People" (Пользователи) щелкните **Add Person** (Добавить пользователя).

    ![Настройка единого входа](./media/purecloud-by-genesys-tutorial/configure04.png)

4. Во всплывающем окне **Add People to the Organization** (Добавление пользователей к организации) выполните следующие действия:

    ![Настройка единого входа](./media/purecloud-by-genesys-tutorial/configure05.png)

    a. В текстовом поле **Full Name** (Полное имя) введите имя пользователя, например **Brittasimon**.

    b. В текстовом поле **Email** (Электронная почта) введите адрес электронной почты пользователя, например **brittasimon\@contoso.com**.
    
    c. Нажмите кнопку **Создать**.

### <a name="test-single-sign-on"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку PureCloud от Genesys на Панели доступа, вы автоматически войдете в приложение PureCloud от Genesys, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

