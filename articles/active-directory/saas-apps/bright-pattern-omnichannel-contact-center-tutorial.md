---
title: Руководство по Интеграция Azure Active Directory с Bright Pattern Omnichannel Contact Center | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Bright Pattern Omnichannel Contact Center.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: de1e0091-ca10-4e7e-8014-f4579d8eabf6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 205b1746bac30a015d4efe4bde573be44563e2f1
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67451715"
---
# <a name="tutorial-integrate-bright-pattern-omnichannel-contact-center-with-azure-active-directory"></a>Руководство по Интеграция Azure Active Directory с Bright Pattern Omnichannel Contact Center

В этом руководстве вы узнаете, как интегрировать Bright Pattern Omnichannel Contact Center с Azure Active Directory (Azure AD). Когда вы интегрируете Bright Pattern Omnichannel Contact Center с Azure AD, вы можете сделать следующее.

* Управлять доступом к Bright Pattern Omnichannel Contact Center из Azure AD.
* Разрешать пользователям автоматически входить в систему Bright Pattern Omnichannel Contact Center с учетной записью Azure AD.
* Централизованное управление учетными записями через портал Azure.

Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее:

* подписка Azure AD Если у вас нет подписки, [здесь](https://azure.microsoft.com/pricing/free-trial/) вы можете получить бесплатную пробную версию сроком на один месяц.
* Подписка с поддержкой единого входа (SSO) в Bright Pattern Omnichannel Contact Center

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Bright Pattern Omnichannel Contact Center поддерживает **SP и IDP** единый вход
* Bright Pattern Omnichannel Contact Center поддерживает **Just In Time** подготовку пользователей.

## <a name="adding-bright-pattern-omnichannel-contact-center-from-the-gallery"></a>Добавление Bright Pattern Omnichannel Contact Center из коллекции

Чтобы настроить интеграцию Bright Pattern Omnichannel Contact Center в Azure AD, вам потребуется добавить Bright Pattern Omnichannel Contact Center из коллекции в список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавить из коллекции** введите **Bright Pattern Omnichannel Contact Center** в поле поиска.
1. Выберите **Bright Pattern Omnichannel Contact Center** на панели результатов и затем добавьте это приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

Настройте и проверьте единый вход Azure AD в Bright Pattern Omnichannel Contact Center с использованием имени тестового пользователя **B.Simon**. Чтобы единый вход работал, необходимо установить связь между пользователем Azure AD и соответствующим пользователем Bright Pattern Omnichannel Contact Center.

Чтобы настроить и проверить единый вход Azure AD в Bright Pattern Omnichannel Contact Center, выполните следующие типичные действия.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройте единый вход в Configure Bright Pattern Omnichannel Contact Center](#configure-bright-pattern-omnichannel-contact-center-sso)**  — настройте параметры единого входа на стороне приложения.
3. **[Создайте тестового пользователя Azure AD](#create-an-azure-ad-test-user)**  — для проверки работы единого входа Azure AD от имени пользователя B.Simon.
4. **[Назначьте тестового пользователя Azure AD](#assign-the-azure-ad-test-user)**  — чтобы позволить пользователю B.Simon использовать единый вход Azure AD.
5. **[Создайте тестового пользователя Bright Pattern Omnichannel Contact Center](#create-bright-pattern-omnichannel-contact-center-test-user)**  — чтобы иметь аналог B.Simon в Bright Pattern Omnichannel Contact Center, который связан с представлением пользователя в Azure AD.
6. **[Проверка единого входа](#test-sso)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции приложения **Bright Pattern Omnichannel Contact Center** найдите раздел **Управление** и выберите **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

   ![Правка базовой конфигурации SAML](common/edit-urls.png)

1. Если вы хотите настроить приложение в режиме, инициируемом **поставщиком удостоверений**, в разделе **Базовая конфигурация SAML** выполните следующие действия.

    a. В текстовом поле **Идентификатор** введите URL-адрес в формате `<SUBDOMAIN>_sso`.

    б) В текстовом поле **URL-адрес ответа** введите URL-адрес в формате `https://<SUBDOMAIN>.brightpattern.com/agentdesktop/sso/redirect`.

1. Чтобы настроить приложение для работы в режиме, инициируемом **поставщиком услуг**, щелкните **Задать дополнительные URL-адреса** и выполните следующие действия.

    В текстовом поле **URL-адрес входа** введите URL-адрес в формате `https://<SUBDOMAIN>.brightpattern.com/`.

    > [!NOTE]
    > Эти значения приведены для примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Обратитесь в [службу поддержки Bright Pattern Omnichannel Contact Center](mailto:support@brightpattern.com), чтобы получить эти значения. Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

1. Приложение Bright Pattern Omnichannel Contact Center ожидает утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно в разделе **Атрибуты пользователя** на странице интеграции приложения. На странице **Настройка единого входа с помощью SAML** нажмите кнопку **Изменить**, чтобы открыть диалоговое окно **Атрибуты пользователя**.

    ![image](common/edit-attribute.png)

1. В разделе **Утверждения пользователя** диалогового окна **Атрибуты пользователя** измените утверждения, щелкнув значок **Изменить**, или добавьте утверждение, нажав кнопку **Добавить новое утверждение**, чтобы настроить атрибут токена SAML, как показано на рисунке выше, и выполните следующие действия. 

    | ИМЯ | Пространство имен  |
    | ---------------| --------------- |
    | firstName | user.givenname |
    | lastName | user.surname |
    | email | user.mail |

    a. Щелкните **Добавить новое утверждение**, чтобы открыть диалоговое окно **Управление утверждениями пользователя**.

    b. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.

    c. Оставьте пустым поле **Пространство имен**.

    d. В качестве источника выберите **Атрибут**.

    д. В списке **Атрибут источника** введите значение атрибута, отображаемое для этой строки.

    Е. Нажмите кнопку **ОК**.

    ж. Выберите команду **Сохранить**.

1. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** найдите пункт **Сертификат (Base64)** и щелкните **Скачать**, чтобы скачать сертификат. Сохраните этот сертификат на компьютере.

   ![Ссылка для скачивания сертификата](common/certificatebase64.png)

1. В разделе **Настройки Bright Pattern Omnichannel Contact Center** скопируйте соответствующие URL-адреса в соответствии с требованиями.

   ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

### <a name="configure-bright-pattern-omnichannel-contact-center-sso"></a>Настройка единого входа в Bright Pattern Omnichannel Contact Center

Чтобы настроить единый вход в **Bright Pattern Omnichannel Contact Center**, нужно отправить скачанный **Сертификат (Base64)** и соответствующий скопированный URL-адрес из портала Azure в [Службу поддержки Bright Pattern Omnichannel Contact Center](mailto:support@brightpattern.com). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

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

В этом разделе описывается, как разрешить пользователю B.Simon использовать единый вход Azure, предоставив доступ к Bright Pattern Omnichannel Contact Center.

1. На портале Azure выберите **Корпоративные приложения**, а затем —**Все приложения**.
1. В списке приложений выберите **Bright Pattern Omnichannel Contact Center**.
1. На странице "Обзор" приложения найдите раздел **Управление** и выберите **Пользователи и группы**.

   ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Ссылка "Добавить пользователя"](common/add-assign-user.png)

1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
1. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.
1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-bright-pattern-omnichannel-contact-center-test-user"></a>Создание тестового пользователя в Bright Pattern Omnichannel Contact Center

В этом разделе описывается создание пользователя с именем B.Simon в Bright Pattern Omnichannel Contact Center. Bright Pattern Omnichannel Contact Center поддерживает JIT-подготовку пользователя, которая включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Если пользователь еще не существует в Bright Pattern Omnichannel Contact Center, то новый пользователь будет создан после аутентификации.

### <a name="test-sso"></a>Проверка единого входа

При выборе на Панели доступа плитки Bright Pattern Omnichannel Contact Center вы автоматически войдете в Bright Pattern Omnichannel Contact Center, для которого настраивается единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)