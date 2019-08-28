---
title: Руководство по Руководство по интеграции единого входа Azure Active Directory с ProNovos Analytics | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и ProNovos Analytics.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 38b06fdd-2e9d-4208-bfe5-7f72cd7c371d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d2c6719cf37959d01375ec099cdf1c2cea3afa5
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69543900"
---
# <a name="tutorial-tutorial-azure-active-directory-single-sign-on-sso-integration-with-pronovos-analytics"></a>Руководство по Руководство по интеграции единого входа Azure Active Directory с ProNovos Analytics

В этом учебнике описано, как интегрировать ProNovos Analytics с Azure Active Directory (Azure AD). Интеграция ProNovos Analytics с Azure AD обеспечивает следующие возможности.

* С помощью Azure AD можно контролировать доступ к ProNovos Analytics.
* Вы можете включить автоматический вход пользователей в ProNovos Analytics с помощью учетных записей Azure AD.
* Централизованное управление учетными записями через портал Azure.

Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее:

* подписка Azure AD Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* Подписка ProNovos Analytics с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* ProNovos Analytics поддерживает единый вход, инициированный **поставщиком услуг и поставщиком удостоверений**.
* ProNovos Analytics поддерживает **JIT**-подготовку пользователей.

## <a name="adding-pronovos-analytics-from-the-gallery"></a>Добавление ProNovos Analytics из коллекции

Чтобы настроить интеграцию ProNovos Analytics с Azure AD, необходимо добавить ProNovos Analytics из коллекции в список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в поле поиска введите **ProNovos Analytics**.
1. Выберите **ProNovos Analytics** в области результатов и добавьте это приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.


## <a name="configure-and-test-azure-ad-sso-for-pronovos-analytics"></a>Настройка и проверка единого входа Azure AD для ProNovos Analytics

Настройте и проверьте единый вход Azure AD в ProNovos Analytics с помощью тестового пользователя **B. Simon**. Чтобы обеспечить работу единого входа, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в ProNovos Analytics.

Чтобы настроить и проверить единый вход Azure AD в ProNovos Analytics, выполните действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
    1. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD с помощью пользователя B.Simon.
    1. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить пользователю B.Simon использовать единый вход Azure AD.
2. **[Настройка единого входа в ProNovos Analytics](#configure-pronovos-analytics-sso)** необходима, чтобы настроить параметры единого входа на стороне приложения.
    1. **[Создание тестового пользователя ProNovos Analytics](#create-pronovos-analytics-test-user)** требуется для того, чтобы в ProNovos Analytics существовал пользователь B. Simon, связанный с одноименным пользователем в Azure AD.
3. **[Проверка единого входа](#test-sso)** необходима, чтобы убедиться в корректной работе конфигурации.

## <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **ProNovos Analytics** найдите раздел **Управление** и выберите **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

   ![Правка базовой конфигурации SAML](common/edit-urls.png)

1. В разделе  **Базовая конфигурация SAML** приложение предварительно настроено в режиме, инициированном **поставщиком удостоверений** , и для приложения заданы требуемые URL-адреса в Azure. Пользователь должен сохранить конфигурацию, нажав кнопку **Сохранить** .

1. Чтобы настроить приложение для работы в режиме, инициируемом **поставщиком услуг**, щелкните **Задать дополнительные URL-адреса** и выполните следующие действия.

    В текстовом поле **URL-адрес входа** введите URL-адрес: `https://analytics.pronovos.com/Pronovos/servlet/mstrWeb`.

4. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** найдите элемент **Сертификат (необработанный)** и щелкните **Скачать**, чтобы скачать сертификат. Сохраните этот сертификат на компьютере.

    ![Ссылка для скачивания сертификата](common/certificateraw.png)

6. Скопируйте требуемый URL-адрес из раздела **Настройка ProNovos Analytics**.

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

В этом разделе описано, как предоставить пользователю B. Simon доступ к ProNovos Analytics, чтобы он мог использовать единый вход Azure.

1. На портале Azure выберите **Корпоративные приложения**, а затем —**Все приложения**.
1. В списке приложений выберите **ProNovos Analytics**.
1. На странице "Обзор" приложения найдите раздел **Управление** и выберите **Пользователи и группы**.

   ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Ссылка "Добавить пользователя"](common/add-assign-user.png)

1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
1. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.
1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

## <a name="configure-pronovos-analytics-sso"></a>Настройка единого входа в ProNovos Analytics

Чтобы настроить единый вход на стороне **ProNovos Analytics**, нужно отправить скачанный **сертификат (необработанный)** и соответствующие URL-адреса, скопированные на портале Azure, в [группу поддержки ProNovos Analytics](mailto:support@pronovos.com). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-pronovos-analytics-test-user"></a>Создание тестового пользователя в ProNovos Analytics

В этом разделе описано, как в приложении ProNovos Analytics создать пользователя B. Simon. Приложение ProNovos Analytics поддерживает JIT-подготовку пользователей, которая включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Если пользователь еще не существует в ProNovos Analytics, он создается после проверки подлинности.

## <a name="test-sso"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку ProNovos Analytics на Панели доступа, вы автоматически войдете в приложение ProNovos Analytics, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

