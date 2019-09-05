---
title: Руководство по Интеграция Azure Active Directory с Adaptive Insights | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Adaptive Insights.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad059e6047dd883c0a5aab0d714d999840050584
ms.sourcegitcommit: ac29357a47cc05afdf0f84834de5277598f4d87c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/03/2019
ms.locfileid: "70213618"
---
# <a name="tutorial-integrate-adaptive-insights-with-azure-active-directory"></a>Руководство по Интеграции Adaptive Insights с Azure Active Directory

В этом руководстве описано, как интегрировать Adaptive Insights с Azure Active Directory (Azure AD). Во время интеграции Adaptive Insights с Azure AD вы можете:

* контролировать с помощью Azure AD доступ к Adaptive Insights;
* включать автоматический вход пользователей в Adaptive Insights с использованием учетных записей Azure AD.
* Централизованное управление учетными записями через портал Azure.

Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее:

* подписка Azure AD Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* подписка с поддержкой единого входа на Adaptive Insights.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Adaptive Insights поддерживает единый вход, инициированный **IDP**

## <a name="adding-adaptive-insights-from-the-gallery"></a>Добавление Adaptive Insights из коллекции

Чтобы настроить интеграцию приложения Adaptive Insights с Azure AD, вам нужно добавить это приложение из коллекции в список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** введите **Adaptive Insights** в поле поиска.
1. Выберите **Adaptive Insights** на панели результатов, а затем добавьте приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

Настройте и проверьте единый вход Azure AD и Adaptive Insights с помощью тестового пользователя **B.Simon**. Чтобы обеспечить работу единого входа, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Adaptive Insights.

Чтобы настроить и проверить единый вход Azure AD в Adaptive Insights, выполните действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в Adaptive Insights](#configure-adaptive-insights-sso)** необходима, чтобы конфигурировать параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD с помощью пользователя B.Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить пользователю B.Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя Adaptive Insights](#create-adaptive-insights-test-user)** нужно для того, чтобы в Adaptive Insights также существовал пользователь B.Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-sso)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Adaptive Insights** найдите раздел **Управление** и выберите **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

   ![Правка базовой конфигурации SAML](common/edit-urls.png)

1. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    a. В текстовом поле **Идентификатор** введите URL-адрес в формате `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`.

    б) В текстовом поле **URL-адрес ответа** введите URL-адрес в формате `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`.

    > [!NOTE]
    > Идентификатор сущности и URL-адрес ответа можно получить на странице **Параметры единого входа SAML** Adaptive Insights.

4. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** найдите пункт **Сертификат (Base64)** и щелкните **Скачать**, чтобы скачать сертификат. Сохраните этот сертификат на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

6. Скопируйте нужные URL-адреса в разделе **Настройка Adaptive Insights**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

### <a name="configure-adaptive-insights-sso"></a>Настройка единого входа для Adaptive Insights

1. В другом окне веб-браузера войдите на корпоративный сайт Adaptive Insights на правах администратора.

2. Перейдите в раздел **Administration**(Администрирование).

    ![Администратор](./media/adaptivesuite-tutorial/ic805644.png "Администратор")

3. В разделе **Users and Roles** (Пользователи и роли) щелкните **SAML SSO Settings** (Параметры единого входа SAML).

    ![Управление параметрами единого входа SAML](./media/adaptivesuite-tutorial/ic805645.png "Управление параметрами единого входа SAML")

4. На странице **SAML SSO Settings** (Параметры единого входа SAML) выполните следующие действия.

    ![Параметры единого входа SAML](./media/adaptivesuite-tutorial/ic805646.png "Параметры единого входа SAML")

    a. Введите имя конфигурации в текстовое поле **Identity provider name** (Имя поставщика удостоверений).

    b. Вставьте значение поля **Идентификатор Azure AD**, скопированное на портале Azure, в текстовое поле **Идентификатор сущности поставщика удостоверений**.

    c. Вставьте значение поля **URL-адрес входа**, скопированное на портале Azure, в текстовое поле **URL-адрес единого входа поставщика удостоверений**.

    d. Вставьте значение поля **URL-адрес выхода**, скопированное на портале Azure, в текстовое поле **Пользовательский URL-адрес для выхода**.

    д. Чтобы отправить загруженный сертификат, нажмите кнопку **Выбрать файл**.

    f. Выберите следующие параметры:

     * Для параметра **SAML user id** (Идентификатор пользователя SAML) выберите значение **User’s Adaptive Insights user name** (Имя пользователя Adaptive Insights).

     * Для параметра **SAML user id location** (Расположение идентификатора пользователя SAML) выберите значение **User id in NameID of Subject** (Идентификатор пользователя из NameID в Subject).

     * Для параметра **SAML NameID format** (Формат NameID SAML) выберите значение **Адрес электронной почты**.

     * Для параметра **Включить SAML** выберите значение **Allow SAML SSO and direct Adaptive Insights login** (Разрешить единый вход SAML и прямой вход Adaptive Insights).

    ж. Скопируйте значение **Adaptive Insights SSO URL** (URL-адрес единого входа Adaptive Insights) и вставьте его в текстовые поля **Идентификатор сущности** и **URL-адрес ответа** в разделе **Базовая конфигурация SAML** на портале Azure.

    h. Выберите команду **Сохранить**.

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

В этом разделе описано, как включить единый вход в Azure для пользователя B.Simon, предоставив этому пользователю доступ к Adaptive Insights.

1. На портале Azure выберите **Корпоративные приложения**, а затем —**Все приложения**.
1. Из списка приложений выберите **Adaptive Insights**.
1. На странице "Обзор" приложения найдите раздел **Управление** и выберите **Пользователи и группы**.

   ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Ссылка "Добавить пользователя"](common/add-assign-user.png)

1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
1. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.
1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-adaptive-insights-test-user"></a>Создание тестового пользователя Adaptive Insights

Чтобы пользователи Azure AD могли выполнять вход в Adaptive Insights, их следует подготовить для работы в Adaptive Insights. В случае Adaptive Insights подготовка выполняется вручную.

**Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.**

1. Выполните вход на корпоративный сайт **Adaptive Insights** на правах администратора.

2. Перейдите в раздел **Administration**(Администрирование).

   ![Администратор](./media/adaptivesuite-tutorial/IC805644.png "Администратор")

3. В разделе **Users and Roles** (Пользователи и роли) щелкните **Пользователи**.

   ![Добавление пользователя](./media/adaptivesuite-tutorial/IC805648.png "Добавление пользователя")

4. В разделе **New User** (Новый пользователь) выполните следующие действия.

   ![Отправка](./media/adaptivesuite-tutorial/IC805649.png "Отправка")

   a. Введите в текстовые поля **Name** (Имя), **Username** (Имя пользователя), **Email** (Адрес электронной почты) и **Password** (Пароль) соответствующие данные действующего пользователя Azure Active Directory, для которого выполняется подготовка.

   b. Выберите **Роль**.

   c. Нажмите кнопку **Submit**(Отправить).

> [!NOTE]
> Вы можете использовать любые другие средства создания учетной записи пользователя Adaptive Insights или API, предоставляемые Adaptive Insights для подготовки учетных записей пользователя AAD.

### <a name="test-sso"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Adaptive Insights на панели доступа, вы автоматически войдете в приложение Adaptive Insights, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

