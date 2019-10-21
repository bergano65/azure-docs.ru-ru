---
title: Руководство по интеграции единого входа Azure Active Directory с PureCloud от Genesys | Документация Майкрософт
description: В этой статье объясняется, как настроить единый вход между Azure Active Directory и PureCloud от Genesys.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e16a46db-5de2-4681-b7e0-94c670e3e54e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfaa5a4ebb8bc633dc49db08698aec31de9436d4
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72373118"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-purecloud-by-genesys"></a>Руководство по интеграции единого входа Azure Active Directory с PureCloud от Genesys

В этом учебнике описано, как интегрировать PureCloud от Genesys с Azure Active Directory (Azure AD). Интеграция PureCloud от Genesys с Azure AD предоставляет следующие возможности:

* С помощью Azure AD вы можете контролировать доступ к PureCloud от Genesys.
* Вы можете включить автоматический вход (единый вход) для пользователей в PureCloud от Genesys с помощью учетных записей Azure AD.
* Централизованное управление учетными записями через портал Azure.

Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее:

* подписка Azure AD Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* Подписка PureCloud от Genesys с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* PureCloud от Genesys поддерживает единый вход, инициируемый **поставщиком услуг и поставщиком удостоверений**.

> [!NOTE]
> Идентификатор этого приложения — фиксированное строковое значение, поэтому в одном клиенте можно настроить только один экземпляр.

## <a name="adding-purecloud-by-genesys-from-the-gallery"></a>Добавление PureCloud от Genesys из коллекции

Чтобы настроить интеграцию PureCloud от Genesys с Azure AD, необходимо добавить PureCloud от Genesys из коллекции в список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в поле поиска введите **PureCloud от Genesys**.
1. Выберите **PureCloud от Genesys** в области результатов и добавьте это приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.

## <a name="configure-and-test-azure-ad-single-sign-on-for-purecloud-by-genesys"></a>Настройка и проверка единого входа Azure AD для PureCloud от Genesys

Настройте и проверьте единый вход Azure AD в PureCloud от Genesys с помощью тестового пользователя **B. Simon**. Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в PureCloud от Genesys.

Чтобы настроить и проверить единый вход Azure AD в PureCloud от Genesys, выполните действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
    1. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD с помощью пользователя B.Simon.
    1. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить пользователю B.Simon использовать единый вход Azure AD.
1. **[Настройка единого входа в PureCloud от Genesys](#configure-purecloud-by-genesys-sso)** необходима, чтобы настроить параметры единого входа на стороне приложения.
    1. **[Создание тестового пользователя PureCloud от Genesys](#create-purecloud-by-genesys-test-user)** требуется для того, чтобы в PureCloud от Genesys существовал пользователь B. Simon, связанный с одноименным пользователем в Azure AD.
1. **[Проверка единого входа](#test-sso)** необходима, чтобы убедиться в корректной работе конфигурации.

## <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **PureCloud от Genesys** найдите раздел **Управление** и выберите **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

   ![Правка базовой конфигурации SAML](common/edit-urls.png)

1. Если вы хотите настроить приложение в режиме, инициируемом **поставщиком удостоверений**, в разделе **Базовая конфигурация SAML** введите значения следующих полей.

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
    | `https://login.mypurecloud.com.au/saml`|

1. Чтобы настроить приложение для работы в режиме, инициируемом **поставщиком услуг**, щелкните **Задать дополнительные URL-адреса** и выполните следующие действия.

    В текстовом поле **URL-адрес входа** введите URL-адрес для своего региона:
    
    | |
    |--|
    | `https://login.mypurecloud.com` |
    | `https://login.mypurecloud.de` |
    | `https://login.mypurecloud.jp` |
    | `https://login.mypurecloud.ie` |
    | `https://login.mypurecloud.com.au` |

1. Приложение PureCloud от Genesys ожидает проверочные утверждения SAML в определенном формате, поэтому вам требуется добавить настраиваемые сопоставления атрибутов в вашу конфигурацию атрибутов токена SAML. На следующем снимке экрана показан список атрибутов по умолчанию.

    ![image](common/default-attributes.png)

1. В дополнение к описанному выше приложение PureCloud от Genesys ожидает несколько дополнительных атрибутов в ответе SAML, которые показаны ниже. Эти атрибуты также заранее заполнены, но вы можете изменить их в соответствии со своими требованиями.

    | ИМЯ | Исходный атрибут|
    | ---------------| --------------- |
    | Email | user.userprinicipalname |
    | OrganizationName | `Your organization name` |

1. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** найдите пункт **Сертификат (Base64)** и щелкните **Скачать**, чтобы скачать сертификат. Сохраните этот сертификат на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

1. Скопируйте требуемый URL-адрес из раздела **Настройка PureCloud от Genesys**.

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

В этом разделе описано, как включить единый вход Azure для пользователя B. Simon, предоставив этому пользователю доступ к PureCloud от Genesys.

1. На портале Azure выберите **Корпоративные приложения**, а затем —**Все приложения**.
1. В списке приложений выберите **PureCloud от Genesys**.
1. На странице "Обзор" приложения найдите раздел **Управление** и выберите **Пользователи и группы**.

   ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Ссылка "Добавить пользователя"](common/add-assign-user.png)

1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
1. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.
1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

## <a name="configure-purecloud-by-genesys-sso"></a>Настройка единого входа в PureCloud от Genesys

1. В другом окне веб-браузера войдите в приложение PureCloud от Genesys с правами администратора.

1. Щелкните пункт **Admin** (Администратор) вверху и перейдите к пункту **Single Sign-on** (Единый вход) в разделе **Integrations** (Интеграции).

    ![Настройка единого входа](./media/purecloud-by-genesys-tutorial/configure01.png)

1. Перейдите на вкладку **ADFS/Azure AD (Premium)** и выполните следующие действия:

    ![Настройка единого входа](./media/purecloud-by-genesys-tutorial/configure02.png)

    a. Щелкните **Browse** (Обзор), чтобы выбрать сертификат в кодировке Base64, который был скачан с портала Azure, в поле **ADFS Certificate** (Сертификат ADFS).

    b. В текстовое поле **ADFS Issuer URI** (URI издателя ADFS) вставьте **идентификатор Azure AD**, скопированный на портале Azure.

    c. В текстовое поле **Target URI** (URI назначения) вставьте **URL-адрес входа**, скопированный на портале Azure.

    d. Чтобы задать **идентификатор проверяющей стороны**, перейдите на портал Azure, на странице интеграции с приложением **PureCloud от Genesys** выберите вкладку **Свойства** и скопируйте значение **Идентификатор приложения**. Вставьте его в текстовое поле **Relying Party Identifier** (Идентификатор проверяющей стороны). 

    ![Настройка единого входа](./media/purecloud-by-genesys-tutorial/configure06.png)

    д. Нажмите кнопку **Сохранить**

### <a name="create-purecloud-by-genesys-test-user"></a>Создание тестового пользователя PureCloud от Genesys

Чтобы пользователи Azure AD могли выполнять вход в PureCloud от Genesys, их нужно подготовить к работе в PureCloud от Genesys. В PureCloud от Genesys такая подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Войдите в PureCloud от Genesys с правами администратора.

1. Щелкните **Admin** (Администратор) вверху и перейдите к пункту **People** (Пользователи) в разделе **People & Permissions** (Пользователи и разрешения).

    ![Настройка единого входа](./media/purecloud-by-genesys-tutorial/configure03.png)

1. На странице "People" (Пользователи) щелкните **Add Person** (Добавить пользователя).

    ![Настройка единого входа](./media/purecloud-by-genesys-tutorial/configure04.png)

1. Во всплывающем окне **Add People to the Organization** (Добавление пользователей к организации) выполните следующие действия:

    ![Настройка единого входа](./media/purecloud-by-genesys-tutorial/configure05.png)

    a. В текстовом поле **Full name** (Полное имя) введите имя пользователя, например **B. Simon**.

    b. В текстовом поле **Email** (Адрес электронной почты) введите адрес электронной почты пользователя, например **b.simon\@contoso.com**.

    c. Нажмите кнопку **Создать**.

## <a name="test-sso"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку PureCloud от Genesys на Панели доступа, вы автоматически войдете в приложение PureCloud от Genesys, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Попробуйте использовать приложение PureCloud от Genesys с Azure AD](https://aad.portal.azure.com/)