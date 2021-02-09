---
title: Руководство по интеграции единого входа Azure Active Directory с Samsung Knox and Business Services | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Samsung Knox and Business Services.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: e1cf12d676de84bc18a123fbdf05b1170725eda8
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99072463"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-samsung-knox-and-business-services"></a>Руководство по интеграции единого входа Azure Active Directory с Samsung Knox and Business Services

В этом руководстве объясняется, как интегрировать Samsung Knox and Business Services с Azure Active Directory (Azure AD). Интеграция Samsung Knox and Business Services с Azure AD обеспечивает следующие возможности:

* Контроль доступа к приложению Samsung Knox and Business Services с помощью Azure AD.
* Автоматический вход пользователей в Samsung Knox and Business Services с учетными записями Azure AD.
* Централизованное управление учетными записями через портал Azure.

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее.

* Подписка Azure AD. Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* Подписка Samsung Knox and Business Services с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Samsung Knox and Business Services поддерживает единый вход, инициированный **поставщиком службы**.

> [!NOTE]
> Идентификатор этого приложения — фиксированное строковое значение, поэтому в одном клиенте можно настроить только один экземпляр.

## <a name="adding-samsung-knox-and-business-services-from-the-gallery"></a>Добавление Samsung Knox and Business Services из коллекции

Чтобы настроить интеграцию Samsung Knox and Business Services с Azure AD, вам потребуется добавить Samsung Knox and Business Services из коллекции в список управляемых приложений SaaS.

1. Войдите на портал Azure с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в поле поиска введите **Samsung Knox and Business Services**.
1. Выберите **Samsung Knox and Business Services** в области результатов, а затем добавьте это приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.

## <a name="configure-and-test-azure-ad-sso-for-samsung-knox-and-business-services"></a>Настройка и проверка единого входа Azure AD для Samsung Knox and Business Services

Настройте и проверьте единый вход Azure AD в Samsung Knox and Business Services с помощью тестового пользователя **B.Simon**. Для обеспечения единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Samsung Knox and Business Services.

Чтобы настроить и проверить единый вход Azure AD в Samsung Knox and Business Services, выполните следующие действия:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
    1. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD с помощью пользователя B.Simon.
    1. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить пользователю B.Simon использовать единый вход Azure AD.
1. **[Настройка единого входа в Samsung Knox and Business Services](#configure-samsung-knox-and-business-services-sso)** необходима, чтобы настроить параметры единого входа на стороне приложения.
    1. **[Создание тестового пользователя Samsung Knox and Business Services](#create-samsung-knox-and-business-services-test-user)** требуется для того, чтобы в Samsung Knox and Business Services существовал пользователь B.Simon, связанный с представлением этого пользователя в Azure AD.
1. **[Проверка единого входа](#test-sso)** позволяет убедиться в правильности конфигурации.

## <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На портале Azure на странице интеграции с приложением **Samsung Knox and Business Services** найдите раздел **Управление** и выберите элемент **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок карандаша, чтобы открыть диалоговое окно **Базовая конфигурация SAML** для изменения параметров.

   ![Изменение базовой конфигурации SAML](common/edit-urls.png)

1. На странице **Базовая конфигурация SAML** введите значения следующих полей.

    В текстовом поле **URL-адрес для входа** введите URL-адрес: `https://www.samsungknox.com`.

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

В этом разделе объясняется, как включить единый вход Azure для пользователя B.Simon, предоставив этому пользователю доступ к приложению Samsung Knox and Business Services.

1. На портале Azure выберите **Корпоративные приложения**, а затем — **Все приложения**.
1. В списке приложений выберите **Samsung Knox and Business Services**.
1. На странице "Обзор" приложения найдите раздел **Управление** и выберите **Пользователи и группы**.
1. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.
1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
1. Если пользователям необходимо назначить роль, вы можете выбрать ее из раскрывающегося списка **Выберите роль**. Если для этого приложения не настроена ни одна роль, будет выбрана роль "Доступ по умолчанию".
1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

## <a name="configure-samsung-knox-and-business-services-sso"></a>Настройка единого входа для Samsung Knox and Business Services

1. В другом окне браузера войдите на сайт Samsung Knox and Business Services своей компании в качестве администратора.

1. Щелкните **аватар** в верхнем углу справа.

    ![Аватар Samsung Knox](./media/samsung-knox-and-business-services-tutorial/avatar.png)

1. На боковой панели слева щелкните элемент **ACTIVE DIRECTORY SETTINGS** (Параметры Active Directory) и выполните следующие действия:

    ![Окно параметров Active Directory](./media/samsung-knox-and-business-services-tutorial/sso-settings.png)

    а. В поле **Identifier(entity ID)** (Идентификатор (ИД сущности)) введите **идентификатор**, указанный на портале Azure.

    b. В текстовое поле **App federation Metadata URL** (URL-адрес метаданных федерации приложения) вставьте **URL-адрес метаданных федерации приложения**, скопированный на портале Azure.

    c. Щелкните элемент **CONNECT TO AD SSO** (Подключиться к единому входу AD).

### <a name="create-samsung-knox-and-business-services-test-user"></a>Создание тестового пользователя Samsung Knox and Business Services

В этом разделе объясняется, как создать пользователя Britta Simon в приложении Samsung Knox and Business Services. Для добавления пользователей на платформу Samsung Knox and Business Services обратитесь в [службу поддержки Samsung Knox and Business Services](mailto:noreplyk.sec@samsung.com). Перед использованием единого входа необходимо создать и активировать пользователей.

## <a name="test-sso"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью указанных ниже способов. 

* Выберите **Тестировать приложение** на портале Azure. Вы будете перенаправлены по URL-адресу для входа в Samsung Knox and Business Services, где можно инициировать поток входа. 

* Перейдите по URL-адресу для входа в Samsung Knox and Business Services и инициируйте поток входа.

* Вы можете использовать портал "Мои приложения" корпорации Майкрософт. Щелкните плитку Samsung Knox and Business Services на портале "Мои приложения". Вы перейдете по URL-адресу для входа в Samsung Knox and Business Services. Дополнительные сведения о портале "Мои приложения" см. в [этой статье](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Дальнейшие действия

После настройки Samsung Knox and Business Services вы можете применить функцию управления сеансом, которая в реальном времени защищает конфиденциальные данные вашей организации от кражи и несанкционированного доступа. Управление сеансом является расширением функции условного доступа. [Узнайте, как применять управление сеансами с помощью Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


