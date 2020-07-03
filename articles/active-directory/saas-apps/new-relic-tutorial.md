---
title: Руководство по интеграции Azure Active Directory с New Relic by Account | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и New Relic by Account.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3186b9a8-f4d8-45e2-ad82-6275f95e7aa6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/16/2020
ms.author: jeedes
ms.openlocfilehash: 9fdcec5b55f52b7b6b824bf2ba25c14541b2a3c6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82186572"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic-by-account"></a>Руководство по Интеграция единого входа Azure Active Directory с New Relic by Account

В этом учебник описано, как интегрировать New Relic by Account с Azure Active Directory (Azure AD). Интеграция New Relic by Account с Azure AD обеспечивает следующие возможности:

* Контроль доступа к New Relic by Account с помощью Azure AD.
* Поддержка автоматического входа пользователей в New Relic by Account с учетными записями Azure AD.
* Централизованное управление учетными записями через портал Azure.

Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее.

* Подписка Azure AD. Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* Подписка New Relic by Account с поддержкой единого входа (SSO).

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* New Relic by Account поддерживает единый вход, инициируемый **поставщиком услуг**.

* После настройки New Relic by Account можно применять средства управления сеансами, которые защищают от хищения и несанкционированного доступа к конфиденциальным данным вашей организации в реальном времени. Элементы управления сеансом являются расширением функции условного доступа. [Узнайте, как применять управление сеансами с помощью Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-new-relic-by-account-from-the-gallery"></a>Добавление New Relic by Account из коллекции

Чтобы настроить интеграцию New Relic by Account с Azure AD, необходимо добавить New Relic by Account из коллекции в список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в поле поиска введите **New Relic by Account**.
1. Выберите **New Relic by Account** в области результатов и добавьте это приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.

## <a name="configure-and-test-azure-ad-single-sign-on-for-new-relic-by-account"></a>Настройка и проверка единого входа Azure AD для New Relic by Account

Настройте и проверьте единый вход Azure AD в New Relic by Account с использованием тестового пользователя **B. Simon**. Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в New Relic by Account.

Чтобы настроить и проверить единый вход Azure AD в New Relic by Account, выполните действия из следующих стандартных блоков:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
    * **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD с помощью пользователя B.Simon.
    * **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить пользователю B.Simon использовать единый вход Azure AD.
1. **[Настройка единого входа в New Relic by Account](#configure-new-relic-by-account-sso)** необходима, чтобы настроить параметры единого входа на стороне приложения.
    * **[Создание тестового пользователя New Relic by Account](#create-new-relic-by-account-test-user)** требуется для того, чтобы в New Relic by Account существовал пользователь B. Simon, связанный с одноименным пользователем в Azure AD.
1. **[Проверка единого входа](#test-sso)** позволяет убедиться в правильности конфигурации.

## <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **New Relic by Account** найдите раздел **Управление** и выберите **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

   ![Изменение базовой конфигурации SAML](common/edit-urls.png)
1. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    а. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате:

    `https://rpm.newrelic.com/accounts/{acc_id}/sso/saml/login` — обязательно замените `acc_id` собственным идентификатором учетной записи New Relic by Account.

    b. В текстовом поле **Идентификатор (сущности)** введите URL-адрес следующим образом: `rpm.newrelic.com`

1. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Загрузить**, чтобы загрузить требуемый **сертификат (Base64)** из предложенных вариантов, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

1. Требуемые URL-адреса вы можете скопировать из раздела **Настройка New Relic by Account**.

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

В этом разделе описано, как разрешить пользователю B. Simon использовать единый вход Azure, предоставив этому пользователю доступ к New Relic by Account.

1. На портале Azure выберите **Корпоративные приложения**, а затем — **Все приложения**.
1. В списке приложений выберите **New Relic by Account**.
1. На странице "Обзор" приложения найдите раздел **Управление** и выберите **Пользователи и группы**.

   ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Ссылка "Добавить пользователя"](common/add-assign-user.png)

1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
1. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.
1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

## <a name="configure-new-relic-by-account-sso"></a>Настройка единого входа в New Relic by Account

1. В другом окне веб-браузера войдите на веб-сайт **New Relic by Account** компании в качестве администратора.

2. В меню в верхней части страницы щелкните **Параметры учетной записи**.
   
    ![Параметры учетной записи](./media/new-relic-tutorial/ic797036.png "Параметры учетной записи")

3. Щелкните вкладку **Security and authentication** (Безопасность и аутентификация), а затем выберите вкладку **Single sign on** (Единый вход).
   
    ![Единый вход](./media/new-relic-tutorial/ic797037.png "Единый вход")

4. На диалоговой странице «Настройка SAML» выполните следующие действия:
   
    ![SAML](./media/new-relic-tutorial/ic797038.png "SAML")
   
    а. Щелкните **Выбор файла** , чтобы передать скачанный сертификат Azure Active Directory.

    b. В текстовое поле **Remote login URL** (URL-адрес удаленного входа) вставьте **URL-адрес входа**, скопированный на портале Azure.
   
    c. В текстовое поле **Logout landing URL** (URL-адрес целевой страницы выхода) вставьте значение **URL-адрес выхода**, скопированное на портале Azure.

    d. Щелкните **Сохранить изменения**.

### <a name="create-new-relic-by-account-test-user"></a>Создание тестового пользователя New Relic by Account

1. Войдите на сайт компании **New Relic by Account** в качестве администратора.

2. В меню в верхней части страницы щелкните **Параметры учетной записи**.
   
    ![Параметры учетной записи](./media/new-relic-tutorial/ic797040.png "Параметры учетной записи")

3. В области **Account** (Учетная запись) слева щелкните **Summary** (Сводка) и нажмите кнопку **Add user** (Добавить пользователя).
   
    ![Параметры учетной записи](./media/new-relic-tutorial/ic797041.png "Параметры учетной записи")

4. В диалоговом окне **Активные пользователи** сделайте следующее:
   
    ![Активные пользователи](./media/new-relic-tutorial/ic797042.png "Активные пользователи")
   
    а. В текстовом поле **Электронная почта** введите электронный адрес действующего пользователя Azure Active Directory, которого вы хотите подготовить.

    b. Для параметра **Role** (Роль) выберите значение **User** (Пользователь).

    c. Щелкните **Добавить этого пользователя**.

> [!NOTE]
> Вы можете использовать любые другие инструменты создания учетных записей пользователя New Relic by Account или API, предоставляемые New Relic by Account, для подготовки учетных записей пользователя AAD.

## <a name="test-sso"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент New Relic by Account на Панели доступа, вы автоматически войдете в приложение New Relic by Account, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Попробуйте использовать New Relic by Account с Azure AD](https://aad.portal.azure.com/)

- [Что такое управление сеансами в Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)