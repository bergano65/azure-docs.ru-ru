---
title: Руководство. Интеграция Azure Active Directory с решением EthicsPoint Incident Management (EPIM) | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и EthicsPoint Incident Management (EPIM).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8cb31a4c-9309-469b-93ac-daf0d3c7a3e6
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ededde4f8458051fafd09c388ce556dedaea3efc
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56870641"
---
# <a name="tutorial-azure-active-directory-integration-with-ethicspoint-incident-management-epim"></a>Руководство. Интеграция Azure Active Directory с решением EthicsPoint Incident Management (EPIM)

В этом руководстве описано, как интегрировать решение EthicsPoint Incident Management (EPIM) с Azure Active Directory (Azure AD).
Интеграция EthicsPoint Incident Management (EPIM) с Azure AD обеспечивает следующие преимущества.

* С помощью Azure Active Directory можно контролировать доступ к EthicsPoint Incident Management (EPIM).
* Вы можете включить автоматический вход пользователей в EthicsPoint Incident Management (EPIM) (единый вход) с учетной записью Azure Active Directory.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с EthicsPoint Incident Management (EPIM), вам потребуется следующее:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* подписка EthicsPoint Incident Management (EPIM) с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* EthicsPoint Incident Management (EPIM) поддерживает единый вход, инициированный **поставщиком услуг**.

## <a name="adding-ethicspoint-incident-management-epim-from-the-gallery"></a>Добавление решения EthicsPoint Incident Management (EPIM) из коллекции.

Чтобы настроить интеграцию EthicsPoint Incident Management (EPIM) с Azure AD, вам потребуется добавить EthicsPoint Incident Management (EPIM) из коллекции в список управляемых приложений SaaS.

**Чтобы добавить EthicsPoint Incident Management (EPIM) из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **EthicsPoint Incident Management (EPIM)**, на панели результатов выберите **EthicsPoint Incident Management (EPIM)**, а затем нажмите кнопку **Добавить**, чтобы добавить приложение.

     ![EthicsPoint Incident Management (EPIM) в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описывается настройка и проверка единого входа Azure Active Directory в EthicsPoint Incident Management (EPIM) с помощью тестового пользователя **Britta Simon**.
Для работы единого входа необходимо установить связь между пользователем Azure Active Directory и соответствующим пользователем в EthicsPoint Incident Management (EPIM).

Чтобы настроить и проверить единый вход Azure AD в EthicsPoint Incident Management (EPIM), вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа для EthicsPoint Incident Management (EPIM)](#configure-ethicspoint-incident-management-epim-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя EthicsPoint Incident Management (EPIM)](#create-ethicspoint-incident-management-epim-test-user)** требуется, чтобы в EthicsPoint Incident Management (EPIM) существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure Active Directory.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure Active Directory в EthicsPoint Incident Management (EPIM), сделайте следующее:

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **EthicsPoint Incident Management (EPIM)** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    ![Сведения о домене и URL-адресах единого входа для EthicsPoint Incident Management (EPIM)](common/sp-identifier-reply.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате:
    | |
    |--|
    | `https://<companyname>.navexglobal.com`|
    | `https://<companyname>.ethicspointvp.com`|

    b. В поле **Идентификатор** введите URL-адрес в следующем формате: `https://<companyname>.navexglobal.com/adfs/services/trust`.

    c. В текстовом поле **URL-адрес ответа** введите URL-адрес в формате `https://<servername>.navexglobal.com/adfs/ls/`.

    > [!NOTE]
    > Эти значения приведены для примера. Укажите вместо них фактические значения URL-адреса для входа, идентификатора и URL-адреса ответа. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов EthicsPoint Incident Management (EPIM)](https://www.navexglobal.com/company/contact-us). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

5. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать нужный вам **XML метаданных федерации**, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

6. В разделе **Настройка EthicsPoint Incident Management (EPIM)** скопируйте соответствующие URL-адреса в соответствии со своими требованиями.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    б) Идентификатор Azure AD.

    в) URL-адрес выхода.

### <a name="configure-ethicspoint-incident-management-epim-single-sign-on"></a>Настройка единого входа в EthicsPoint Incident Management (EPIM)

Для настройки единого входа на стороне **EthicsPoint Incident Management (EPIM)** необходимо отправить скачанный **XML-файл метаданных федерации** и соответствующие скопированные URL-адреса на портале Azure в [группу поддержки EthicsPoint Incident Management (EPIM)](https://www.navexglobal.com/company/contact-us). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD 

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    а. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon@yourcompanydomain.extension**.  
    Например BrittaSimon@contoso.com.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив доступ к EthicsPoint Incident Management (EPIM).

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **EthicsPoint Incident Management (EPIM)**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **EthicsPoint Incident Management (EPIM)**.

    ![Ссылка на EthicsPoint Incident Management (EPIM) в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-ethicspoint-incident-management-epim-test-user"></a>Создание тестового пользователя в EthicsPoint Incident Management (EPIM)

В этом разделе описано, как создать пользователя Britta Simon в EthicsPoint Incident Management (EPIM). Чтобы добавить пользователей на платформу EthicsPoint Incident Management (EPIM), обратитесь к  [группе поддержки EthicsPoint Incident Management (EPIM)](https://www.navexglobal.com/company/contact-us). Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="test-single-sign-on"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку EthicsPoint Incident Management (EPIM) на Панели доступа, вы автоматически войдете в решение EthicsPoint Incident Management (EPIM), для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

