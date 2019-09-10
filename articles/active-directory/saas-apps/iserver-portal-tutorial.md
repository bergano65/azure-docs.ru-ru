---
title: Руководство по интеграции единого входа Azure Active Directory с iServer Portal | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в приложении iServer Portal.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2923fe11-954d-4f9a-9af9-5cde020432e0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c2af8f81636f7653049029b3c0d3a81cbfb8e6fd
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70143085"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-iserver-portal"></a>Руководство по интеграции единого входа Azure Active Directory с iServer Portal

В этом учебнике описано, как интегрировать iServer Portal с Azure Active Directory (Azure AD). Интеграция iServer Portal с Azure AD обеспечивает следующие возможности:

* С помощью Azure AD вы можете контролировать доступ к iServer Portal.
* Вы можете включить автоматический вход пользователей в iServer Portal с помощью учетных записей Azure AD.
* Централизованное управление учетными записями через портал Azure.

Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее:

* подписка Azure AD Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* Подписка iServer Portal с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* iServer Portal поддерживает единый вход, инициированный **поставщиком услуг и поставщиком удостоверений**.

## <a name="adding-iserver-portal-from-the-gallery"></a>Добавление iServer Portal из коллекции

Чтобы настроить интеграцию iServer Portal с Azure AD, добавьте iServer Portal из коллекции в список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в поле поиска введите **iServer Portal**.
1. Выберите **iServer Portal** в области результатов и добавьте это приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.

## <a name="configure-and-test-azure-ad-single-sign-on-for-iserver-portal"></a>Настройка и проверка единого входа Azure AD в iServer Portal

Настройте и проверьте единый вход Azure AD в iServer Portal с помощью тестового пользователя **B. Simon**. Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в iServer Portal.

Чтобы настроить и проверить единый вход Azure AD в iServer Portal, выполните действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
    1. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD с помощью пользователя B.Simon.
    1. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить пользователю B.Simon использовать единый вход Azure AD.
1. **[Настройка единого входа в iServer Portal](#configure-iserver-portal-sso)** требуется, чтобы задать параметры единого входа на стороне приложения.
    1. **[Создание тестового пользователя iServer Portal](#create-iserver-portal-test-user)** нужно для того, чтобы в iServer Portal существовал пользователь B. Simon, связанный с одноименным пользователем в Azure AD.
1. **[Проверка единого входа](#test-sso)** необходима, чтобы убедиться в корректной работе конфигурации.

## <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **iServer Portal** найдите раздел **Управление** и выберите **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

   ![Правка базовой конфигурации SAML](common/edit-urls.png)

1. Если вы хотите настроить приложение в режиме, инициируемом **поставщиком удостоверений**, в разделе **Базовая конфигурация SAML** введите значения следующих полей.

    a. В текстовом поле **Идентификатор** введите URL-адрес в формате `iserver-portal-<myiserverportal>`.

    б) В текстовом поле **URL-адрес ответа** введите URL-адрес в формате `https://<myiserverportal.com>/SAML/login`.

1. Чтобы настроить приложение для работы в режиме, инициируемом **поставщиком услуг**, щелкните **Задать дополнительные URL-адреса** и выполните следующие действия.

    В текстовом поле **URL-адрес входа** введите URL-адрес в формате `https://<myiserverportal.com>/SAML/login`.

    > [!NOTE]
    > Эти значения приведены для примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Чтобы получить эти значения, обратитесь в [службу поддержки iServer Portal](mailto:support@orbussoftware.com). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

1. В разделе **Сертификат подписи SAML** щелкните кнопку **Правка**, чтобы открыть диалоговое окно **Сертификат подписи SAML**.

    ![Изменение сертификата подписи SAML](common/edit-certificate.png)

1. В разделе **Сертификат подписи SAML** скопируйте **значение отпечатка** и сохраните его на компьютере.

    ![Копирование значения "Отпечаток"](common/copy-thumbprint.png)

1. Требуемый URL-адрес можно скопировать из раздела **Настройка iServer Portal**.

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

В этом разделе описано, как включить единый вход Azure для пользователя B. Simon, предоставив этому пользователю доступ к приложению iServer Portal.

1. На портале Azure выберите **Корпоративные приложения**, а затем —**Все приложения**.
1. Из списка приложений выберите **iServer Portal**.
1. На странице "Обзор" приложения найдите раздел **Управление** и выберите **Пользователи и группы**.

   ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Ссылка "Добавить пользователя"](common/add-assign-user.png)

1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
1. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.
1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

## <a name="configure-iserver-portal-sso"></a>Настройка единого входа в iServer Portal

Чтобы настроить единый вход на стороне **iServer Portal**, нужно отправить **значение отпечатка** и соответствующие URL-адреса, скопированные на портале Azure, [группе поддержки iServer Portal](mailto:support@orbussoftware.com). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-iserver-portal-test-user"></a>Создание тестового пользователя в iServer Portal

Из этого раздела вы узнаете, как создать пользователя B. Simon в приложении iServer Portal. Чтобы добавить пользователей на платформу iServer Portal, обратитесь к [группе поддержки iServer Portal](mailto:support@orbussoftware.com). Перед использованием единого входа необходимо создать и активировать пользователей.

## <a name="test-sso"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку iServer Portal на Панели доступа, вы автоматически войдете в приложение iServer Portal, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Попробуйте использовать iServer Portal с Azure AD](https://aad.portal.azure.com/)

