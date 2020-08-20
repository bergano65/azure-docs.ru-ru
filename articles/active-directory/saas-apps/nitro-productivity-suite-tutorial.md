---
title: Руководство по интеграции единого входа Azure Active Directory с Nitro Productivity Suite | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Nitro Productivity Suite.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/02/2020
ms.author: jeedes
ms.openlocfilehash: b67e280824c5e2336aa79e14d3e09d3a670118a5
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554345"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nitro-productivity-suite"></a>Руководство по интеграции единого входа Azure Active Directory с Nitro Productivity Suite

В этом учебнике описано, как интегрировать Nitro Productivity Suite с Azure Active Directory (Azure AD). Интеграция Nitro Productivity Suit с Azure AD обеспечивает следующие возможности:

* Контроль доступа к Nitro Productivity Suite с помощью Azure AD.
* Автоматический вход пользователей в Nitro Productivity Suite с учетными записями Azure AD.
* Централизованное управление учетными записями через портал Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье о [едином входе в приложения с помощью Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Предварительные требования

Для начала работы необходимы перечисленные ниже компоненты и данные.

* Подписка Azure AD. Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* [Подписка Enterprise](https://www.gonitro.com/pricing) для Nitro Productivity Suite.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Nitro Productivity Suite поддерживает единый вход, инициированный **поставщиком служб** и **поставщиком удостоверений**.
* Nitro Productivity Suite поддерживает **JIT**-подготовку пользователей.
* После настройки Nitro Productivity Suite вы можете применить функцию управления сеансом, которая защищает от хищения конфиденциальных данных вашей организации и несанкционированного доступа к ним в реальном времени. Управление сеансом является расширением функции условного доступа. Подробные сведения см. в статье об [управлении сеансами с помощью Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-nitro-productivity-suite-from-the-gallery"></a>Добавление Nitro Productivity Suite из коллекции

Чтобы настроить интеграцию приложения Nitro Productivity Suite с Azure AD, вам нужно добавить это приложение из коллекции в список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области слева выберите **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в поле поиска введите **Nitro Productivity Suite**.
1. Выберите **Nitro Productivity Suite** в списке результатов и добавьте это приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.


## <a name="configure-and-test-azure-ad-single-sign-on-for-nitro-productivity-suite"></a>Настройка и проверка единого входа Azure AD для Nitro Productivity Suite

Настройте и проверьте единый вход Azure AD в Nitro Productivity Suite с помощью тестового пользователя **B.Simon**. Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Nitro Productivity Suite.

Чтобы настроить и проверить единый вход Azure AD в Nitro Productivity Suite, выполните действия из следующих стандартных блоков:

1. [Настройка единого входа Azure AD](#configure-azure-ad-sso) необходима, чтобы пользователи могли использовать эту функцию.
    1. [Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user) требуется для проверки работы единого входа Azure AD от имени пользователя B.Simon.
    1. [Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user) необходимо, чтобы разрешить B.Simon использовать единый вход Azure AD.
1. [Настройка единого входа в Nitro Productivity Suite](#configure-nitro-productivity-suite-sso) необходима, чтобы настроить параметры единого входа на стороне приложения.
    1. [Создание тестового пользователя Nitro Productivity Suite](#create-a-nitro-productivity-suite-test-user) требуется для того, чтобы в Nitro Productivity Suite существовал пользователь B.Simon, связанный с представлением этого пользователя в Azure AD.
1. [Проверка единого входа](#test-sso) позволяет убедиться в правильности конфигурации.

## <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Nitro Productivity Suite** найдите раздел **Управление**. Выберите **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. В разделе **Сертификат подписи SAML** найдите параметр **Сертификат (Base64)** . Щелкните **Скачать**, чтобы получить сертификат и сохранить его на локальном компьютере.

    ![Снимок экрана: раздел "Сертификат подписи SAML" с выделенной ссылкой "Скачать"](common/certificatebase64.png)
    
1. В разделе **Настройка Nitro Productivity Suite** щелкните значок копирования рядом с полем **URL-адрес для входа**.
    
    ![Снимок экрана с разделом настройки Nitro Productivity Suite, где выделены URL-адреса и значки копирования](common/copy-configuration-urls.png)
    
1. На [портале администрирования Nitro](https://admin.gonitro.com/) на странице **Enterprise Settings** (Корпоративные параметры) найдите раздел **Single Sign-On** (Единый вход). Выберите **Setup SAML SSO** (Настройка единого входа SAML).

    а. Вставьте **URL-адрес входа**, скопированный на предыдущем шаге, в поле **Sign In URL** (URL-адрес входа).
    
    b. Передайте **Сертификат (Base64)** из предыдущего шага, выбрав его в поле **X509 Signing Certificate** (Сертификат для подписи X509).
    
    c. Нажмите кнопку **Submit** (Отправить).
    
    d. Выберите пункт **Включить единый вход**.


1. Вернитесь на [портал Azure](https://portal.azure.com/). На странице **Настройка единого входа с помощью SAML** щелкните значок карандаша, чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить эти параметры.

   ![Снимок экрана настройки единого входа на странице SAML с выделенным значком пера](common/edit-urls.png)

1. Если вы хотите настроить приложение в режиме, инициируемом **поставщиком удостоверений**, в разделе **Базовая конфигурация SAML** введите значения следующих полей.

    а. В текстовом поле **Идентификатор** скопируйте и вставьте поле **Идентификатор сущности SAML** на [портале администрирования Nitro](https://admin.gonitro.com/). Он должен иметь следующий формат: `urn:auth0:gonitro-prod:<ENVIRONMENT>`

    b. В текстовом поле **URL-адрес ответа** скопируйте и вставьте поле **URL-адрес ACS** на [портале администрирования Nitro](https://admin.gonitro.com/). Он должен иметь следующий формат: `https://gonitro-prod.eu.auth0.com/login/callback?connection=<ENVIRONMENT>`

1. Чтобы настроить приложение для работы в режиме, инициируемом **поставщиком услуг**, щелкните **Задать дополнительные URL-адреса** и выполните следующие действия:

    В текстовом поле **URL-адрес входа** введите URL-адрес: `https://sso.gonitro.com/login`.

1. Щелкните **Сохранить**.

1. Приложение Nitro Productivity Suite ожидает проверочные утверждения SAML в определенном формате, поэтому следует добавить настраиваемые сопоставления атрибутов в вашу конфигурацию атрибутов токена SAML. На следующем снимке экрана показан список атрибутов по умолчанию.

    ![Снимок экрана с атрибутами по умолчанию](common/default-attributes.png)

1. Кроме описанных выше атрибутов приложение Nitro Productivity Suite ожидает несколько дополнительных атрибутов в ответе SAML. Эти атрибуты заранее заполнены, однако их можно изменить в соответствии с вашими требованиями.
    
    | Имя  |  Атрибут источника|
    | ---------------| --------------- |
    | employeeNumber |  user.objectid |


### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

В этом разделе описано, как на портале Azure создать тестового пользователя с именем B.Simon.

1. Слева на портале Azure выберите **Azure Active Directory** > **Пользователи** > **Все пользователи**.
1. В верхней части экрана выберите **Новый пользователь**.
1. В разделе **Свойства пользователя** выполните следующие действия.
   1. В поле **Имя** введите `B.Simon`.  
   1. В поле **Имя пользователя** введите username@companydomain.extension. Например, `B.Simon@contoso.com`.
   1. Установите флажок **Показать пароль** и запишите значение пароля.
   1. Нажмите кнопку **создания**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как включить единый вход Azure для пользователя B.Simon, предоставив этому пользователю доступ к Nitro Productivity Suite.

1. На портале Azure выберите **Корпоративные приложения** > **Все приложения**.
1. В списке приложений выберите **Nitro Productivity Suite**.
1. На странице сводных сведений о приложении откройте раздел **Управление** и выберите **Пользователи и группы**.

   ![Снимок экрана: раздел "Управление" с выделенным пунктом "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Снимок экрана: страница "Пользователи и группы" с выделенным элементом "Добавить пользователя"](common/add-assign-user.png)

1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** из списка пользователей. Затем нажмите кнопку **Выбрать** в нижней части экрана.
1. Если вы хотите получить значение роли в утверждении SAML, выберите в диалоговом окне **Выбор роли** подходящую роль для пользователя в предложенном списке. Затем нажмите кнопку **Выбрать** в нижней части экрана.
1. В диалоговом окне **Добавление назначения** выберите **Назначить**.

## <a name="configure-nitro-productivity-suite-sso"></a>Настройка единого входа в Nitro Productivity Suite

Чтобы настроить единый вход на стороне Nitro Productivity Suite, отправьте скачанный **сертификат (Base64)** и соответствующие URL-адреса, скопированные с портала Azure, [группе поддержки Nitro Productivity Suite](https://www.gonitro.com/support). Группа поддержки обеспечит правильную настройку подключения для единого входа SAML с обеих сторон.

### <a name="create-a-nitro-productivity-suite-test-user"></a>Создание тестового пользователя Nitro Productivity Suite

Приложение Nitro Productivity Suite поддерживает JIT-подготовку пользователей, которая включена по умолчанию. Никакие действия с вашей стороны не требуются. Если пользователь еще не существует в Nitro Productivity Suite, он создается после проверки подлинности.

## <a name="test-sso"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью Панели доступа.

Щелкнув плитку Nitro Productivity Suite на Панели доступа, вы автоматически войдете в приложение Nitro Productivity Suite, для которого настроили единый вход. Дополнительные сведения см. в статье о [входе на портал "Мои приложения" и запуске приложений на нем](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Попробуйте использовать Nitro Productivity Suite с Azure AD](https://aad.portal.azure.com/)

- [Что такое управление сеансами в Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Защита приложений с помощью функции управления настройками условного доступа для приложений в Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

