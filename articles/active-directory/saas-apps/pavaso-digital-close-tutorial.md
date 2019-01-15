---
title: Руководство. Интеграция Azure Active Directory с Pavaso Digital Close | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Pavaso Digital Close.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 23d83f82-39c3-4fc2-88c9-f8074cc44d43
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/03/2019
ms.author: jeedes
ms.openlocfilehash: d07e203203b4edd1bd62be084f245825371559b1
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/07/2019
ms.locfileid: "54067554"
---
# <a name="tutorial-azure-active-directory-integration-with-pavaso-digital-close"></a>Руководство. Интеграция Azure Active Directory с Pavaso Digital Close

В этом руководстве описано, как интегрировать Pavaso Digital Close с Azure Active Directory (Azure AD).
Интеграция Azure AD с Pavaso Digital Close обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к Pavaso Digital Close.
* Вы можете включить автоматический вход пользователей в Pavaso Digital Close (единый вход) с помощью учетных записей Azure AD.
* Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Pavaso Digital Close, вам потребуется:

* подписка Azure AD; Если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).
* Подписка Pavaso Digital Close с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства проводится настройка и проверка единого входа Azure AD в тестовой среде.

* Pavaso Digital Close поддерживает единый вход инициированного **пакета обновления и выдающейся точки распространения**.

## <a name="adding-pavaso-digital-close-from-the-gallery"></a>Добавление Pavaso Digital Close из коллекции

Чтобы настроить интеграцию Pavaso Digital Close с Azure AD, необходимо добавить Pavaso Digital Close из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Pavaso Digital Close из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"](common/add-new-app.png)

4. В поле поиска введите **Pavaso Digital Close** , на панели результатов выберите **Pavaso Digital Close**  и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![Pavaso Digital Close в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в [название приложения] с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в [название приложения].

Чтобы настроить и проверить единый вход Azure AD в [название приложения], вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в Pavaso Digital Close ](#configure-pavaso-digital-close-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя приложения Pavaso Digital Close ](#create-pavaso-digital-close-test-user)** требуется для того, чтобы в Pavaso Digital Close существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в [название приложения], выполните следующие действия.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Pavaso Digital Close** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. Если вы хотите настроить приложение в режиме, инициируемом **IDP**, в разделе **Базовая конфигурация SAML** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа приложения Pavaso Digital Close](common/idp-intiated.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в формате `https://<SUBDOMAIN>.pavaso.com/AuthServices`.

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<SUBDOMAIN>.pavaso.com/AuthServices/Acs`.

5. Чтобы настроить приложение для работы в режиме, инициируемом **поставщиком услуг**, щелкните **Задать дополнительные URL-адреса** и выполните следующее действие:

    В текстовом поле **URL-адрес входа** введите URL-адрес в следующем формате: `https://<SUBDOMAIN>.pavaso.com`.

    ![Сведения о домене и URL-адресах единого входа приложения Pavaso Digital Close](common/metadata-upload-additional-signon.png)

    > [!NOTE]
    > Эти значения приведены в качестве примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Чтобы получить эти значения, обратитесь в [техническую поддержку клиентов Pavaso Digital Close](mailto:support@pavaso.com). Можно также обратиться к шаблонам, указанным в разделе **Базовая конфигурация SAML** на портале Azure.

6. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать нужный вам **XML метаданных федерации**, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

7. Требуемый URL-адрес можно скопировать из раздела **Настройка Pavaso Digital Close**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    a. URL-адрес входа.

    b. Идентификатор Azure AD.

    c. URL-адрес выхода

### <a name="configure-pavaso-digital-close-single-sign-on"></a>Настройка единого входа Pavaso Digital Close

Для настройки единого входа на стороне **Pavaso Digital Close** необходимо отправить скачанный **XML метаданных федерации** и соответствующие скопированные URL-адреса с портала Azure [технической поддержке Pavaso Digital Close](mailto:support@pavaso.com). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее.

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    a. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon@yourcompanydomain.extension**.  
    Например, BrittaSimon@contoso.com

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как предоставить пользователю Britta Simon доступ к Pavaso Digital Close, чтобы он мог использовать единый вход Azure.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **Pavaso Digital Close**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Pavaso Digital Close**.

    ![Ссылка Pavaso Digital Close в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-pavaso-digital-close-test-user"></a>Создание тестового пользователя Pavaso Digital Close

В этом разделе описано, как создать пользователя Britta Simon в приложении Pavaso Digital Close. Чтобы добавить пользователей на платформу Pavaso Digital Close, необходимо сотрудничать с  [технической поддержкой Pavaso Digital Close](mailto:support@pavaso.com). Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="test-single-sign-on"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку "Pavaso Digital Close" на панели доступа, вы автоматически войдете в приложение Pavaso Digital Close, для которого настроили единый вход. Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
