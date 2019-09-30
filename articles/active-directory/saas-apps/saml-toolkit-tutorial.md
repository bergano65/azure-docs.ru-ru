---
title: Руководство по Интеграция единого входа Azure Active Directory с Azure AD SAML Toolkit | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Azure AD SAML Toolkit.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3f4348e7-c34e-43c7-926e-f1b26ffacf6d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83e5790a036a93c947a79bc2848e7be6febf30b6
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122047"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-azure-ad-saml-toolkit"></a>Руководство по Интеграция единого входа Azure Active Directory с Azure AD SAML Toolkit

В этом руководстве описано, как интегрировать Azure AD SAML Toolkit с Azure Active Directory (Azure AD). Интеграция Azure AD SAML Toolkit с Azure AD обеспечивает следующие возможности.

* С помощью Azure AD вы можете контролировать доступ к Azure AD SAML Toolkit.
* Вы можете включить автоматический вход пользователей в Azure AD SAML Toolkit с помощью учетных записей Azure AD.
* Централизованное управление учетными записями через портал Azure.

Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее:

* подписка Azure AD Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* подписка Azure AD SAML Toolkit с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Azure AD SAML Toolkit поддерживает единый вход, инициируемый **поставщиком службы**.

## <a name="adding-azure-ad-saml-toolkit-from-the-gallery"></a>Добавление Azure AD SAML Toolkit из коллекции

Чтобы настроить интеграцию Azure AD SAML Toolkit с Azure AD, необходимо добавить Azure AD SAML Toolkit из коллекции в список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в поле поиска введите **Azure AD SAML Toolkit**.
1. Выберите **Azure AD SAML Toolkit** в области результатов и добавьте это приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.

## <a name="configure-and-test-azure-ad-single-sign-on-for-azure-ad-saml-toolkit"></a>Настройка и проверка единого входа в Azure AD для Azure AD SAML Toolkit

Настройте и проверьте единый вход Azure AD в Azure AD SAML Toolkit с помощью тестового пользователя **B.Simon**. Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Azure AD SAML Toolkit.

Чтобы настроить и проверить единый вход Azure AD в Azure AD SAML Toolkit, выполните действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
    1. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD с помощью пользователя B.Simon.
    1. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить пользователю B.Simon использовать единый вход Azure AD.
1. **[Настройка единого входа в Azure AD SAML Toolkit](#configure-azure-ad-saml-toolkit-sso)** необходима, чтобы настроить параметры единого входа на стороне приложения.
    1. **[Создание тестового пользователя Azure AD SAML Toolkit](#create-azure-ad-saml-toolkit-test-user)** требуется для того, чтобы в Azure AD SAML Toolkit существовал пользователь B.Simon, связанный с одноименным пользователем в Azure AD.
1. **[Проверка единого входа](#test-sso)** необходима, чтобы убедиться в корректной работе конфигурации.

## <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Azure AD SAML Toolkit** найдите раздел **Управление** и выберите **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

   ![Правка базовой конфигурации SAML](common/edit-urls.png)

1. На странице **Базовая конфигурация SAML** введите значения для следующих полей.

    a. В текстовом поле **URL-адрес входа** введите URL-адрес: `https://samltoolkit.azurewebsites.net/`.

    b. В текстовом поле **Идентификатор (сущности)** введите URL-адрес следующим образом: `https://samltoolkit.azurewebsites.net`

    c. В текстовом поле **URL-адрес ответа** введите URL-адрес `https://samltoolkit.azurewebsites.net/SAML/Consume`

1. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** найдите элемент **Сертификат (необработанный)** и щелкните **Скачать**, чтобы скачать сертификат. Сохраните этот сертификат на компьютере.

    ![Ссылка для скачивания сертификата](common/certificateraw.png)

1. Требуемые URL-адреса можно скопировать из раздела **Настройка Azure AD SAML Toolkit**.

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

В этом разделе описано, как включить единый вход Azure для пользователя B.Simon, предоставив этому пользователю доступ к Azure AD SAML Toolkit.

1. На портале Azure выберите **Корпоративные приложения**, а затем —**Все приложения**.
1. Из списка приложений выберите **Azure AD SAML Toolkit**.
1. На странице "Обзор" приложения найдите раздел **Управление** и выберите **Пользователи и группы**.

   ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Ссылка "Добавить пользователя"](common/add-assign-user.png)

1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
1. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.
1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

## <a name="configure-azure-ad-saml-toolkit-sso"></a>Настройка единого входа в Azure AD SAML Toolkit

1. Откройте новое окно веб-браузера и, если вы еще не зарегистрировались на веб-сайте Azure AD SAML Toolkit, сначала сделайте это, щелкнув **Register** (Зарегистрироваться). Если вы уже зарегистрировались, войдите на свой корпоративный сайт Azure AD SAML Toolkit с помощью зарегистрированных учетных данных для входа.

    ![Регистрация в Azure AD SAML Toolkit](./media/saml-toolkit-tutorial/register.png)

1. Щелкните **SAML Configuration** (Конфигурация SAML).

    ![Конфигурация SAML в Azure AD SAML Toolkit](./media/saml-toolkit-tutorial/saml-configure.png)

1. Нажмите кнопку **Создать**.

    ![Настройка единого входа в Azure AD SAML Toolkit](./media/saml-toolkit-tutorial/createsso.png)

1. На странице **SAML SSO Configuration** (Конфигурация единого входа SAML) сделайте следующее.

    ![Настройка единого входа в Azure AD SAML Toolkit](./media/saml-toolkit-tutorial/fill-details.png)

    1. В текстовое поле **Login URL** (URL-адрес для входа) вставьте значение **URL-адрес входа**, скопированное на портале Azure.

    1. В текстовое поле **Azure AD Identifier** (Идентификатор Azure AD) вставьте значение **Идентификатор Azure AD**, скопированное на портале Azure.

    1. В текстовое поле **URL-адрес выхода** вставьте значение **URL-адреса выхода**, скопированное на портале Azure.

    1. Щелкните **Choose File** (Выбрать файл) и передайте файл **сертификата (необработанный)** , скачанный с портала Azure.

    1. Нажмите кнопку **Создать**.

### <a name="create-azure-ad-saml-toolkit-test-user"></a>Создание тестового пользователя Azure AD SAML Toolkit

В этом разделе описано, как в приложении Azure AD SAML Toolkit создать пользователя B.Simon. Azure AD SAML Toolkit поддерживает JIT-подготовку пользователей, которая включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Если пользователь еще не существует в Azure AD SAML Toolkit, он создается после выполнения аутентификации.

## <a name="test-sso"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Azure AD SAML Toolkit на Панели доступа, вы автоматически войдете в приложение Azure AD SAML Toolkit, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Попробуйте Azure AD SAML Toolkit с Azure AD](https://aad.portal.azure.com/)