---
title: Руководство по интеграции единого входа Azure Active Directory с Perforce Helix Core — Helix Authentication Service | Документация Майкрософт
description: Сведения о настройке единого входа в Azure Active Directory и Perforce Helix Core — Helix Authentication Service.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 70625d03-2156-4dea-828b-0af251e12db9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 05/28/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6aba0b5daf5a1e2d95a95feea321066e090dbed1
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/31/2020
ms.locfileid: "84236362"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-perforce-helix-core--helix-authentication-service"></a>Руководство по Интеграция единого входа Azure Active Directory с Perforce Helix Core — Helix Authentication Service

В этом учебнике описано, как интегрировать Perforce Helix Core — Helix Authentication Service с Azure Active Directory (Azure AD). При интеграции Perforce Helix Core — Helix Authentication Service с Azure AD можно:

* осуществлять контроль в Azure AD с доступом к Perforce Helix Core — Helix Authentication Service;
* включить автоматический вход пользователей Perforce Helix Core — Helix Authentication Service с помощью их учетных записей Azure AD;
* Централизованное управление учетными записями через портал Azure.

Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее.

* Подписка Azure AD. Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* Подписка с поддержкой единого входа в Perforce Helix Core — Helix Authentication Service.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Perforce Helix Core — Helix Authentication Service поддерживает единый вход, инициированный **поставщиком услуг**
* После настройки Perforce Helix Core — Helix Authentication Service можно применять элементы управления сеансами, которые защищают от хищения конфиденциальных данных вашей организации и несанкционированного доступа к ним в режиме реального времени. Управление сеансом является расширением функции условного доступа. [Узнайте, как применять управление сеансами с помощью Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-perforce-helix-core--helix-authentication-service-from-the-gallery"></a>Добавление Perforce Helix Core — Helix Authentication Service из коллекции

Чтобы настроить интеграцию Perforce Helix Core — Helix Authentication Service с Azure AD, необходимо добавить Perforce Helix Core — Helix Authentication Service из коллекции в ваш список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в поле поиска введите **Perforce Helix Core — Helix Authentication Service**.
1. На панели результатов выберите **Perforce Helix Core — Helix Authentication Service**, а затем добавьте приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.

## <a name="configure-and-test-azure-ad-single-sign-on-for-perforce-helix-core--helix-authentication-service"></a>Настройка и проверка единого входа Azure AD для Perforce Helix Core — Helix Authentication Service

Настройте и проверьте единый вход Azure AD в Perforce Helix Core — Helix Authentication Service с помощью тестового пользователя **B. Simon**. Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Perforce Helix Core — Helix Authentication Service.

Чтобы настроить и проверить единый вход Azure AD в Perforce Helix Core — Helix Authentication Service, выполните действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
    1. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD с помощью пользователя B.Simon.
    1. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить пользователю B.Simon использовать единый вход Azure AD.
1. **[Настройка единого входа в Perforce Helix Core — Helix Authentication Service](#configure-perforce-helix-core--helix-authentication-service-sso)** . Настройка единого входа на стороне приложения.
    1. **[Создание тестового пользователя Perforce Helix Core — Helix Authentication Service](#create-perforce-helix-core--helix-authentication-service-test-user)** требуется для того, чтобы в этом приложении имелся эквивалент B. Simon, связанный с представлением этого пользователя в Azure AD.
1. **[Проверка единого входа](#test-sso)** позволяет убедиться в правильности конфигурации.

## <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Perforce Helix Core — Helix Authentication Service** найдите раздел **Управление** и выберите **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

   ![Изменение базовой конфигурации SAML](common/edit-urls.png)

1. На странице **Базовая конфигурация SAML** введите значения следующих полей.

    а. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<helix-auth-service>.<customer-hostname>.com/`.

    b. В текстовом поле **Идентификатор (сущности)** введите URL-адрес в следующем формате: `https://<helix-auth-service>.<customer-hostname>.com/saml`.

    > [!NOTE]
    > Эти значения приведены для примера. Необходимо обновить эти значения действующим URL-адресом для входа и идентификатором. Чтобы получить эти значения, обратитесь в [группу поддержки Perforce Helix Core — Helix Authentication Service](mailto:support@perforce.com). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

1. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** нажмите кнопку "Копировать", чтобы скопировать **URL-адрес метаданных федерации приложений** и сохранить его на компьютере.

    ![Ссылка для скачивания сертификата](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

В этом разделе описано, как на портале Azure создать тестового пользователя с именем B.Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.
1. В верхней части экрана выберите **Новый пользователь**.
1. В разделе **Свойства пользователя** выполните следующие действия.
   1. В поле **Имя** введите `B.Simon`.  
   1. В поле **Имя пользователя** введите username@companydomain.extension. Например, `B.Simon@contoso.com`.
   1. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.
   1. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как включить единый вход Azure для пользователя B. Simon, предоставив этому пользователю доступ к Perforce Helix Core — Helix Authentication Service.

1. На портале Azure выберите **Корпоративные приложения**, а затем — **Все приложения**.
1. В списке приложений выберите **Perforce Helix Core — Helix Authentication Service**.
1. На странице "Обзор" приложения найдите раздел **Управление** и выберите **Пользователи и группы**.

   ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Ссылка "Добавить пользователя"](common/add-assign-user.png)

1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
1. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.
1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

## <a name="configure-perforce-helix-core--helix-authentication-service-sso"></a>Настройка единого входа в Perforce Helix Core — Helix Authentication Service

Чтобы настроить единый вход на стороне **Perforce Helix Core — Helix Authentication Service**, необходимо отправить **URL-адрес метаданных федерации** в [группу поддержки Perforce Helix Core — Helix Authentication Service](mailto:support@perforce.com). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-perforce-helix-core--helix-authentication-service-test-user"></a>Создание тестового пользователя Perforce Helix Core — Helix Authentication Service

Из этого раздела вы узнаете, как создать пользователя Britta Simon в Perforce Helix Core — Helix Authentication Service. Для добавления пользователей на платформу Perforce Helix Core — Helix Authentication Service обратитесь в  [группу поддержки Perforce Helix Core — Helix Authentication Service](mailto:support@perforce.com). Перед использованием единого входа необходимо создать и активировать пользователей.

## <a name="test-sso"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Perforce Helix Core — Helix Authentication Service на Панели доступа, вы автоматически войдете в приложение Perforce Helix Core — Helix Authentication Service, для которого вы настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Попробуйте Perforce Helix Core — Helix Authentication Service с Azure AD](https://aad.portal.azure.com/)

- [Что такое управление сеансами в Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Защита Perforce Helix Core — Helix Authentication Service с помощью средств улучшения видимости и управления](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

