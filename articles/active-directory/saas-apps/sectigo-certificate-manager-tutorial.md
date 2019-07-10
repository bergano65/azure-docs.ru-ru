---
title: Руководство по Интеграция Azure Active Directory со Sectigo Certificate Manager | Документация Майкрософт
description: Из этой статьи вы узнаете, как настроить единый вход Azure Active Directory в Sectigo Certificate Manager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 62cd6987-3373-4b58-b1ff-589f4a3d70a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 15-04-2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d7c7cf4972b1ee0a5add3b4611dc4c8655da875
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67091539"
---
# <a name="tutorial-azure-active-directory-integration-with-sectigo-certificate-manager"></a>Руководство по Интеграция Azure Active Directory со Sectigo Certificate Manager

В этом руководстве описано, как интегрировать Sectigo Certificate Manager с Azure Active Directory (Azure AD).

Интеграция Azure AD с Sectigo Certificate Manager обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к Sectigo Certificate Manager.
* Пользователи могут автоматически входить в Sectigo Certificate Manager с учетными записями Azure AD (единый вход).
* Вы можете управлять учетными записями централизованно, через портал Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье о [едином входе в приложения в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с приложением Sectigo Certificate Manager, вам потребуется следующее:

* подписка Azure AD Если у вас еще нет подписки Azure AD, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.
* Подписка Sectigo Certificate Manager с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде и интеграцию Sectigo Certificate Manager с Azure AD.

Sectigo Certificate Manager поддерживает следующие функции:

* **единый вход, инициированный поставщиком службы**;
* **единый вход, инициированный поставщиком удостоверений**;

## <a name="add-sectigo-certificate-manager-in-the-azure-portal"></a>Добавление Sectigo Certificate Manager на портале Azure

Чтобы интегрировать Sectigo Certificate Manager с Azure AD, нужно добавить Sectigo Certificate Manager в список управляемых приложений SaaS.

1. Войдите на [портале Azure](https://portal.azure.com).

1. В меню слева выберите **Azure Active Directory**.

    ![Пункт Azure Active Directory](common/select-azuread.png)

1. Щелкните **Корпоративные приложения** > **Все приложения**.

    ![Область "Корпоративные приложения"](common/enterprise-applications.png)

1. Чтобы добавить приложение, выберите **Новое приложение**.

    ![Элемент "Новое приложение"](common/add-new-app.png)

1. В поле поиска введите **Sectigo Certificate Manager**. В результатах поиска выберите **Sectigo Certificate Manager** и щелкните **Добавить**.

    ![Sectigo Certificate Manager в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Sectigo Certificate Manager для пользователя **Britta Simon**. Чтобы обеспечить работу единого входа, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Sectigo Certificate Manager.

Чтобы настроить и проверить единый вход Azure AD в Sectigo Certificate Manager, потребуется выполнить действия в следующих стандартных блоках:

| Задача | ОПИСАНИЕ |
| --- | --- |
| **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** | Предоставление пользователям возможности использовать эту функцию. |
| **[Настройка единого входа для Sectigo Certificate Manager](#configure-sectigo-certificate-manager-single-sign-on)** | Настройка параметров единого входа в приложении. |
| **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** | Проверка работы единого входа Azure AD на примере пользователя Britta Simon. |
| **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** | Предоставление пользователю Britta Simon возможности использовать единый вход Azure AD. |
| **[Создание тестового пользователя Sectigo Certificate Manager](#create-a-sectigo-certificate-manager-test-user)** | Создание пользователя Britta Simon в Sectigo Certificate Manager, связанного с соответствующим пользователем в Azure AD. |
| **[Проверка единого входа](#test-single-sign-on)** | необходима, чтобы убедиться, что конфигурация работает правильно. |

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описывается настройка единого входа Azure AD в Sectigo Certificate Manager на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Sectigo Certificate Manager** выберите **Единый вход**.

    ![Параметр для настройки единого входа](common/select-sso.png)

1. На панели **Выбрать метод единого входа** выберите режим **SAML** или **SAML/WS-FED**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

1. На панели **Настройка единого входа с помощью SAML** щелкните **Изменить** (значок карандаша), чтобы открыть панель **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

1. Чтобы настроить *режим, инициируемый поставщиком удостоверений*, в области **Базовая конфигурация SAML** выполните приведенные ниже действия:

    1. В поле **Идентификатор** введите один из следующих URL-адресов:
       * https:\//cert-manager.com/shibboleth
       * https:\//hard.cert-manager.com/shibboleth

    1. В поле **URL-адрес ответа** введите один из следующих URL-адресов:
        * https:\//cert-manager.com/Shibboleth.sso/SAML2/POST
        * https:\//hard.cert-manager.com/Shibboleth.sso/SAML2/POST

    1. Щелкните **Задать дополнительные URL-адреса**.

    1. В поле **Состояние ретранслятора** введите один из следующих URL-адресов:
       * https:\//cert-manager.com/customer/SSLSupport/idp
       * https:\//hard.cert-manager.com/customer/SSLSupport/idp

    ![Сведения о домене и URL-адресах единого входа для приложения Sectigo Certificate Manager](common/idp-relay.png)

1.  Чтобы настроить приложение в режиме, инициируемом *поставщиком служб*, выполните следующие действия:

    * В поле **URL-адрес для входа** введите один из следующих URL-адресов:
      * https:\//cert-manager.com/Shibboleth.sso/Login
      * https:\//hard.cert-manager.com/Shibboleth.sso/Login

      ![Сведения о домене и URL-адресах единого входа для приложения Sectigo Certificate Manager](common/both-signonurl.png)

1. В области **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** нажмите кнопку **Скачать**, расположенную рядом с полем **Сертификат (Base64)** . Выберите вариант для скачивания в соответствии с вашими требованиями. Сохраните сертификат на своем компьютере.

    ![Вариант для скачивания сертификата в кодировке Base64](common/certificatebase64.png)

1. Скопируйте требуемые URL-адреса в разделе **Настройка Sectigo Certificate Manager**:

    * URL-адрес входа.
    * Идентификатор Azure AD
    * URL-адрес выхода.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

### <a name="configure-sectigo-certificate-manager-single-sign-on"></a>Настройка единого входа для Sectigo Certificate Manager

Чтобы настроить единый вход на стороне Sectigo Certificate Manager, нужно отправить скачанный файл сертификата (Base64) и соответствующие URL-адреса, скопированные на портале Azure, [службе поддержки Sectigo Certificate Manager](https://sectigo.com/support). Специалисты службы поддержки Sectigo Certificate Manager на основе полученной информации выполнят все необходимое для правильной настройки единого входа SAML с обеих сторон.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD 

В этом разделе описано, как создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure выберите **Azure Active Directory** > **Пользователи** > **Все пользователи**.

    ![Пункты меню "Пользователи" и "Все пользователи"](common/users.png)

1. Выберите **Новый пользователь**.

    ![Элемент "Новый пользователь"](common/new-user.png)

1. В области **Пользователь** выполните приведенные ниже действия.

    1. В поле **Имя** введите **BrittaSimon**.
  
    1. В поле **Имя пользователя** введите значение **brittasimon\@\<домен_вашей_компании>.\<доменная_зона>\>** . Например, **brittasimon\@contoso.com**.

    1. Установите флажок **Показать пароль**. Запишите значение, которое отображается в поле **Пароль**.

    1. Нажмите кнопку **Создать**.

    ![Панель "Пользователь"](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе вы предоставите Britta Simon доступ к Sectigo Certificate Manager, чтобы разрешить этому пользователю использовать единый вход Azure.

1. На портале Azure выберите **Корпоративные приложения** > **Все приложения** > **Sectigo Certificate Manager**.

    ![Область "Корпоративные приложения"](common/enterprise-applications.png)

1. В списке приложений выберите **Sectigo Certificate Manager**.

    ![Sectigo Certificate Manager в списке приложений](common/all-applications.png)

1. В меню выберите **Пользователи и группы**.

    ![Пункт меню "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**. Затем в области **Добавление назначения** щелкните **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

1. На панели **Пользователи и группы** в списке пользователей выберите **Britta Simon**. Щелкните **Выбрать**.

1. Если вы планируете получать значение роли в утверждении SAML, выберите на панели **Выбор роли** подходящую роль для пользователя из предложенного списка. Щелкните **Выбрать**.

1. В области **Добавление назначения** выберите **Назначить**.

### <a name="create-a-sectigo-certificate-manager-test-user"></a>Создание тестового пользователя Sectigo Certificate Manager

В этом разделе описано, как создать пользователя Britta Simon на платформе Sectigo Certificate Manager. Чтобы добавить пользователя на платформу Sectigo Certificate Manager, обратитесь в [службу поддержки Sectigo Certificate Manager](https://sectigo.com/support). Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе объясняется, как проверить конфигурацию единого входа Azure AD с помощью портала "Мои приложения".

После настройки единого входа вы сможете автоматически войти в приложение Sectigo Certificate Manager, выбрав элемент **Sectigo Certificate Manager** на портале "Мои приложения". Дополнительные сведения о портале "Мои приложения" см. в статье [Access and use apps on the My Apps portal](../user-help/my-apps-portal-end-user-access.md) (Доступ к приложениям и их использование на портале "Мои приложения").

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения см. в статьях ниже:

- [Список руководств по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Что такое условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


