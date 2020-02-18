---
title: Руководство. Интеграция единого входа Azure Active Directory с New Relic (Limited Release) | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и New Relic (Limited Release).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2ffbcf4a-b6b4-45ac-922f-358abe4c05e8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad229e7964b6e7ad0e3f0d58cfc09669ee2fb301
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/08/2020
ms.locfileid: "77088268"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic-limited-release"></a>Руководство. Интеграция единого входа Azure Active Directory с New Relic (Limited Release)

В этом руководстве описано, как интегрировать New Relic (Limited Release) с Azure Active Directory (Azure AD). Интеграция New Relic (Limited Release) с Azure AD обеспечивает следующие возможности:

* Контроль доступа к New Relic (Limited Release) с помощью Azure AD.
* Поддержка автоматического входа пользователей в New Relic (Limited Release) с учетными записями Azure AD.
* Централизованное управление учетными записями через портал Azure.

Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее.

* Подписка Azure AD. Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* Подписка New Relic (Limited Release) с поддержкой единого входа (SSO).

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* New Relic (Limited Release) поддерживает единый вход, инициированный **поставщиком удостоверений**.

* После настройки New Relic (Limited Release) можно применять средства управления сеансами, которые защищают от хищения и несанкционированного доступа к конфиденциальным данным вашей организации в реальном времени. Элементы управления сеансом являются расширением функции условного доступа. [Узнайте, как применять управление сеансами с помощью Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-new-relic-limited-release-from-the-gallery"></a>Добавление New Relic (Limited Release) из коллекции

Чтобы настроить интеграцию New Relic (Limited Release) с Azure AD, необходимо добавить New Relic (Limited Release) из коллекции в список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в поле поиска введите **New Relic (Limited Release)** .
1. Выберите **New Relic (Limited Release)** в области результатов и добавьте это приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.


## <a name="configure-and-test-azure-ad-single-sign-on-for-new-relic-limited-release"></a>Настройка и проверка единого входа Azure AD для New Relic (Limited Release)

Настройте и проверьте единый вход Azure AD в New Relic (Limited Release) с использованием тестового пользователя **B.Simon**. Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в New Relic (Limited Release).

Чтобы настроить и проверить единый вход Azure AD в New Relic (Limited Release), выполните действия из следующих стандартных блоков:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
    * **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD с помощью пользователя B.Simon.
    * **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить пользователю B.Simon использовать единый вход Azure AD.
1. **[Настройка единого входа в New Relic (Limited Release)](#configure-new-relic-limited-release-sso)** необходима, чтобы настроить параметры единого входа на стороне приложения.
    * **[Создание тестового пользователя New Relic (Limited Release)](#create-new-relic-limited-release-test-user)** требуется для того, чтобы в New Relic (Limited Release) существовал пользователь B.Simon, связанный с одноименным пользователем в Azure AD.
1. **[Проверка единого входа](#test-sso)** позволяет убедиться в правильности конфигурации.

## <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **New Relic (Limited Release)** найдите раздел **Управление** и выберите **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

   ![Изменение базовой конфигурации SAML](common/edit-urls.png)

1. Если у вас есть **файл метаданных поставщика служб**, выполните следующие действия в разделе **Базовая конфигурация SAML**:

    а. Щелкните **Отправить файл метаданных**.

    ![Изображение](common/upload-metadata.png)

    b. Щелкните **значок папки**, выберите файл метаданных и нажмите кнопку **Отправить**.

    ![Изображение](common/browse-upload-metadata.png)

    c. После успешной передачи файла метаданных значения **Идентификатор** и **URL-адрес ответа** в разделе "Базовая конфигурация SAML" заполнятся автоматически.

    ![Изображение](common/idp-intiated.png)

    > [!Note]
    > Если поля **Идентификатор** и **URL-адрес ответа** автоматически не заполняются значениями, введите эти значения вручную в соответствии со своими требованиями.

1. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** нажмите кнопку "Копировать", чтобы скопировать **URL-адрес метаданных федерации приложений** и сохранить его на компьютере.

    ![Ссылка для скачивания сертификата](common/copy-metadataurl.png)

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

В этом разделе описано, как разрешить пользователю B.Simon использовать единый вход Azure, предоставив этому пользователю доступ к New Relic (Limited Release).

1. На портале Azure выберите **Корпоративные приложения**, а затем — **Все приложения**.
1. В списке приложений выберите **New Relic (Limited Release)** .
1. На странице "Обзор" приложения найдите раздел **Управление** и выберите **Пользователи и группы**.

   ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Ссылка "Добавить пользователя"](common/add-assign-user.png)

1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
1. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.
1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

## <a name="configure-new-relic-limited-release-sso"></a>Настройка единого входа на стороне New Relic (Limited Release)

Для настройки единого входа на стороне **New Relic (Limited Release)** необходимо отправить **URL-адрес метаданных федерации приложений** [службе поддержки New Relic (Limited Release)](https://support.newrelic.com/). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.


### <a name="create-new-relic-limited-release-test-user"></a>Создание тестового пользователя в New Relic (Limited Release)

В этом разделе описано, как создать пользователя B.Simon в приложении New Relic (Limited Release). Обратитесь в  [службу поддержки New Relic (Limited Release)](https://support.newrelic.com/) для добавления пользователей на платформу New Relic (Limited Release). Перед использованием единого входа необходимо создать и активировать пользователей.

## <a name="test-sso"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент New Relic (Limited Release) на панели доступа, вы автоматически войдете в приложение New Relic (Limited Release), для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Попробуйте использовать New Relic (Limited Release) с Azure AD](https://aad.portal.azure.com/)

- [Что такое управление сеансами в Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)