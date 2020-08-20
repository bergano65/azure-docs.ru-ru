---
title: Руководство по интеграции единого входа Azure Active Directory с GitHub Enterprise Cloud — учетная запись Enterprise | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в GitHub Enterprise Cloud — учетная запись Enterprise.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/29/2020
ms.author: jeedes
ms.openlocfilehash: 4e23c8a8497459ce7cb3cab3d1469359f80ad846
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88550781"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-github-enterprise-cloud---enterprise-account"></a>Руководство по интеграции единого входа Azure Active Directory с GitHub Enterprise Cloud — учетная запись Enterprise

В этом руководстве описано, как интегрировать GitHub Enterprise Cloud — учетная запись Enterprise с Azure Active Directory (Azure AD). При интеграции GitHub Enterprise Cloud — учетная запись Enterprise с Azure AD вы получаете следующие возможности:

* С помощью Azure AD можно контролировать доступ к GitHub Enterprise Cloud — учетная запись Enterprise.
* Автоматический вход пользователей в GitHub Enterprise Cloud — учетная запись Enterprise с учетными записями Azure AD.
* Централизованное управление учетными записями через портал Azure.

Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее.

* Подписка Azure AD. Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* Подписка GitHub Enterprise Cloud — учетная запись Enterprise с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* GitHub Enterprise Cloud — учетная запись Enterprise поддерживает единый вход, инициированный **поставщиком служб** или **поставщиком удостоверений**.
* GitHub Enterprise Cloud — учетная запись Enterprise поддерживает **JIT**-подготовку пользователей.
* После настройки GitHub Enterprise Cloud — учетная запись Enterprise вы можете применить функцию управления сеансом, которая в реальном времени защищает конфиденциальные данные вашей организации от хищения и несанкционированного доступа. Управление сеансом является расширением функции условного доступа. [Узнайте, как применять управление сеансами с помощью Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-github-enterprise-cloud---enterprise-account-from-the-gallery"></a>Добавление учетной записи GitHub Enterprise Cloud — учетная запись Enterprise из коллекции

Чтобы настроить интеграцию GitHub Enterprise Cloud — учетная запись Enterprise с Azure AD, необходимо добавить GitHub Enterprise Cloud — учетная запись Enterprise из коллекции в список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в поле поиска введите **GitHub Enterprise Cloud (учетная запись Enterprise)** .
1. Выберите **GitHub Enterprise Cloud — учетная запись Enterprise** на панели результатов и добавьте это приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.


## <a name="configure-and-test-azure-ad-sso-for-github-enterprise-cloud---enterprise-account"></a>Настройка и проверка единого входа Azure AD для GitHub Enterprise Cloud — учетная запись Enterprise

Настройте и проверьте единый вход Azure AD в GitHub Enterprise Cloud — учетная запись Enterprise, используя тестового пользователя с именем **B. Simon**. Чтобы обеспечить работу единого входа, необходимо создать связь между пользователем в Azure AD и соответствующим пользователем в GitHub Enterprise Cloud — учетная запись Enterprise.

Чтобы настроить и проверить единый вход Azure AD в GitHub Enterprise Cloud — учетная запись Enterprise, выполните действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
    1. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD с помощью пользователя B.Simon.
    1. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить пользователю B.Simon использовать единый вход Azure AD.
1. **[Настройка единого входа в GitHub Enterprise Cloud — учетная запись Enterprise](#configure-github-enterprise-cloud-enterprise-account-sso)** необходима, чтобы настроить параметры единого входа на стороне приложения.
    1. **[Создание тестового пользователя GitHub Enterprise Cloud — учетная запись Enterprise](#create-github-enterprise-cloud-enterprise-account-test-user)** нужно, чтобы создать в GitHub Enterprise Cloud — учетная запись Enterprise пользователя B. Simon, связанного с представлением этого пользователя в Azure AD.
1. **[Проверка единого входа](#test-sso)** позволяет убедиться в правильности конфигурации.

## <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **GitHub Enterprise Cloud — учетная запись Enterprise** найдите раздел **Управление** и выберите **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

   ![Изменение базовой конфигурации SAML](common/edit-urls.png)

1. Если вы хотите настроить приложение в режиме, инициируемом **поставщиком удостоверений**, в разделе **Базовая конфигурация SAML** введите значения следующих полей.

    а. В текстовом поле **Идентификатор (сущности)** введите URL-адрес в следующем формате: `https://github.com/enterprises/<ENTERPRISE-SLUG>`.

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в формате `https://github.com/enterprises/<ENTERPRISE-SLUG>/saml/consume`.

1. Чтобы настроить приложение для работы в режиме, инициируемом **поставщиком услуг**, щелкните **Задать дополнительные URL-адреса** и выполните следующие действия.

     В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://github.com/enterprises/<ENTERPRISE-SLUG>/sso`

    > [!NOTE]
    > Эти значения приведены для примера. Вместо них необходимо указать фактические значения URL-адреса входа, URL-адреса ответа и идентификатора. Чтобы получить эти значения, обратитесь к [группе поддержки GitHub Enterprise Cloud — учетная запись Enterprise](mailto:support@github.com). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

1. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** найдите пункт **Сертификат (Base64)** и щелкните **Скачать**, чтобы скачать сертификат. Сохраните этот сертификат на компьютере.

    ![Ссылка для скачивания сертификата](common/certificateBase64.png)

1. Скопируйте требуемый URL-адрес в разделе **Настройка GitHub Enterprise Cloud — учетная запись Enterprise**.

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

В этом разделе описано, как предоставить пользователю B. Simon доступ к GitHub Enterprise Cloud — учетная запись Enterprise, чтобы он мог использовать единый вход Azure.

1. На портале Azure выберите **Корпоративные приложения**, а затем — **Все приложения**.
1. В списке приложений выберите **GitHub Enterprise Cloud — учетная запись Enterprise**.
1. На странице "Обзор" приложения найдите раздел **Управление** и выберите **Пользователи и группы**.

   ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Ссылка "Добавить пользователя"](common/add-assign-user.png)

1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
1. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.
1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

## <a name="configure-github-enterprise-cloud-enterprise-account-sso"></a>Настройка единого входа в GitHub Enterprise Cloud — учетная запись Enterprise

Чтобы настроить единый вход на стороне **GitHub Enterprise Cloud — учетная запись Enterprise**, нужно передать скачанный **сертификат в кодировке Base64** и соответствующие URL-адреса, скопированные на портале Azure, [группе поддержки GitHub Enterprise Cloud — учетная запись Enterprise](mailto:support@github.com). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-github-enterprise-cloud-enterprise-account-test-user"></a>Создание тестового пользователя в GitHub Enterprise Cloud — учетная запись Enterprise

В этом разделе вы создадите пользователя с именем B.Simon в GitHub Enterprise Cloud — учетная запись Enterprise. Приложение GitHub Enterprise Cloud — учетная запись Enterprise поддерживает JIT-подготовку пользователей. Эта функция включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Если пользователь еще не существует в GitHub Enterprise Cloud — учетная запись Enterprise, он создается после проверки подлинности.

## <a name="test-sso"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку GitHub Enterprise Cloud — учетная запись Enterprise на панели доступа, вы автоматически войдете в учетную запись GitHub Enterprise Cloud — учетная запись Enterprise, для которой настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Попробуйте работу GitHub Enterprise Cloud — учетная запись Enterprise с Azure Active Directory](https://aad.portal.azure.com/)

- [Что такое управление сеансами в Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Защита приложений с помощью функции управления настройками условного доступа для приложений в Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)