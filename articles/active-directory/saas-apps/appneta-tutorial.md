---
title: Руководство. Интеграция Azure Active Directory с AppNeta Performance Monitor | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в AppNeta Performance Monitor.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 643a45fb-d6fc-4b32-b721-68899f8c7d44
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 491140900dc74ade68109f3c9de586b0f3709b4f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56172626"
---
# <a name="tutorial-azure-active-directory-integration-with-appneta-performance-monitor"></a>Руководство. Интеграция Azure Active Directory с AppNeta Performance Monitor

В этом руководстве описано, как интегрировать Azure Active Directory (Azure AD) с AppNeta Performance Monitor.
Интеграция AppNeta Performance Monitor с Azure AD обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к AppNeta Performance Monitor.
* Вы можете включить автоматический вход пользователей в AppNeta Performance Monitor (единый вход) с учетной записью Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с AppNeta Performance Monitor, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* Подписка AppNeta Performance Monitor с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Приложение AppNeta Performance Monitor поддерживает единый вход, инициируемый **поставщиком услуг**.
* Приложение AppNeta Performance Monitor поддерживает **JIT**-подготовку пользователей.

## <a name="adding-appneta-performance-monitor-from-the-gallery"></a>Добавление AppNeta Performance Monitor из коллекции.

Чтобы настроить интеграцию AppNeta Performance Monitor с Azure AD, необходимо добавить AppNeta Performance Monitor из коллекции в список управляемых приложений SaaS.

**Чтобы добавить AppNeta Performance Monitor из коллекции, сделайте следующее.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **AppNeta Performance Monitor**, выберите **AppNeta Performance Monitor** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить приложение.

     ![AppNeta Performance Monitor в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в AppNeta Performance Monitor с использованием тестового пользователя **Britta Simon**.
Чтобы обеспечить единый вход, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в AppNeta Performance Monitor.

Чтобы настроить и проверить единый вход в Azure AD с AppNeta Performance Monitor, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в AppNeta Performance Monitor](#configure-appneta-performance-monitor-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя AppNeta Performance Monitor](#create-appneta-performance-monitor-test-user)** нужно для того, чтобы в AppNeta Performance Monitor существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в AppNeta Performance Monitor, сделайте следующее:

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **AppNeta Performance Monitor** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    ![Сведения о домене и URL-адресах единого входа для AppNeta Performance Monitor](common/sp-identifier.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<subdomain>.pm.appneta.com`.

    б) В текстовом поле **Идентификатор (сущности)** введите значение: `PingConnect`

    > [!NOTE]
    > Значение URL-адреса входа приведено для примера. Вместо него необходимо указать фактический URL-адрес входа. Чтобы получить это значение, свяжитесь со [службой поддержки клиентов AppNeta Performance Monitor](mailto:support@appneta.com). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

5. Оператор утверждения SAML необходимо передавать приложению AppNeta Performance Monitor в определенном формате. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно в разделе **Атрибуты пользователя** на странице интеграции приложения. На странице **Настройка единого входа с помощью SAML** нажмите кнопку **Изменить**, чтобы открыть диалоговое окно **Атрибуты пользователя**.

    ![изображение](common/edit-attribute.png)

6. В разделе **Утверждения пользователя** диалогового окна **Атрибуты пользователя** измените утверждения, щелкнув значок **Изменить**, или добавьте утверждение, нажав кнопку **Добавить новое утверждение**, чтобы настроить атрибут токена SAML, как показано на рисунке выше, и выполните следующие действия. 

    | ИМЯ | Исходный атрибут|
    | --------| ----------------|
    | firstName| user.givenname|
    | lastName| user.surname|
    | email| user.userprincipalname|
    | name| user.userprincipalname|
    | groups  | user.assignedroles |
    | phone| user.telephonenumber |
    | title| user.jobtitle|
    | | |

    > [!NOTE]
    > **groups** — это группа безопасности в Appneta, которая сопоставлена с **ролью** в Azure AD. Подробные сведения о создании пользовательских ролей в Azure AD см. в [этом](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) документе.

    a. Щелкните **Добавить новое утверждение**, чтобы открыть диалоговое окно **Управление утверждениями пользователя**.

    ![изображение](common/new-save-attribute.png)

    ![изображение](common/new-attribute-details.png)

    б) В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.

    c. Оставьте пустым поле **Пространство имен**.

    4.3. В качестве источника выберите **Атрибут**.

    д. В списке **Атрибут источника** введите значение атрибута, отображаемое для этой строки.

    Е. Нажмите кнопку **ОК**.

    ж. Выберите команду **Сохранить**.

7. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать нужный вам **XML метаданных федерации**, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

8. Требуемый URL-адрес можно скопировать из раздела **настройки AppNeta Performance Monitor**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    б) Идентификатор Azure AD.

    в) URL-адрес выхода.

### <a name="configure-appneta-performance-monitor-single-sign-on"></a>Настройка единого входа в AppNeta Performance Monitor

Чтобы настроить единый вход на стороне **AppNeta Performance Monitor**, нужно отправить загруженный файл **XML метаданных федерации** и соответствующие URL-адреса, скопированные на портале Azure, в [службу технической поддержки AppNeta Performance Monitor](mailto:support@appneta.com). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD 

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    а) В поле **Имя** введите **BrittaSimon**.
  
    б) В поле **Имя пользователя** введите **brittasimon@yourcompanydomain.extension**.  
    Например BrittaSimon@contoso.com.

    в) Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    г) Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к AppNeta Performance Monitor.

1. На портале Azure последовательно выберите **Корпоративные приложения**, **Все приложения** и **AppNeta Performance Monitor**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **AppNeta Performance Monitor**.

    ![Ссылка AppNeta Performance Monitor в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-appneta-performance-monitor-test-user"></a>Создание тестового пользователя в приложении AppNeta Performance Monitor

В рамках этого раздела в AppNeta Performance Monitor создается пользователь с именем Britta Simon. Приложение AppNeta Performance Monitor поддерживает JIT-подготовку пользователей. Эта функция включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Если пользователь еще не существует в AppNeta Performance Monitor, он создается после проверки подлинности.

> [!Note]
> Если необходимо вручную создать пользователя, обратитесь в  [службу технической поддержки AppNeta Performance Monitor](mailto:support@appneta.com).

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент AppNeta Performance Monitor на панели доступа, вы автоматически войдете в приложение AppNeta Performance Monitor, для которого настроен единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
