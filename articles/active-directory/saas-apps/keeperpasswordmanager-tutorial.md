---
title: Руководство по Интеграция Azure Active Directory с Keeper Password Manager & Digital Vault | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в приложении Keeper Password Manager & Digital Vault.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: e1a98f6a-2dae-4734-bdbf-4fba742a61d2
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e55a50a1a4ae1db995a6054cf9dc13382236b8e
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56189486"
---
# <a name="tutorial-azure-active-directory-integration-with-keeper-password-manager--digital-vault"></a>Руководство по Интеграция Azure Active Directory с Keeper Password Manager & Digital Vault

В этом учебнике описано, как интегрировать Keeper Password Manager & Digital Vault с Azure Active Directory (Azure AD).
Интеграция Keeper Password Manager & Digital Vault с Azure AD обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к Keeper Password Manager & Digital Vault.
* Вы можете включить автоматический вход пользователей в Keeper Password Manager & Digital Vault (единый вход) с помощью учетных записей Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Keeper Password Manager & Digital Vault, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* Подписка на Keeper Password Manager & Digital Vault с поддержкой единого входа

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Keeper Password Manager & Digital Vault поддерживает единый вход инициированного **пакета обновления**.

* Keeper Password Manager & Digital Vault поддерживает **JIT-** подготовку пользователей.

## <a name="adding-keeper-password-manager--digital-vault-from-the-gallery"></a>Добавление Keeper Password Manager & Digital Vault из коллекции

Чтобы настроить интеграцию Keeper Password Manager & Digital Vault с Azure AD, необходимо добавить Keeper Password Manager & Digital Vault из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Keeper Password Manager & Digital Vault из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Keeper Password Manager & Digital Vault**, на панели результатов выберите **Keeper Password Manager & Digital Vault** и нажмите кнопку **Добавить**, чтобы добавить приложение.

     ![Keeper Password Manager & Digital Vault в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Keeper Password Manager & Digital Vault с использованием тестового пользователя **Britta Simon**.
Для работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Keeper Password Manager & Digital Vault.

Чтобы настроить и проверить единый вход Azure AD в Keeper Password Manager & Digital Vault, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **Настройка единого входа в Keeper Password Manager & Digital Vault** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя Keeper Password Manager & Digital Vault](#create-keeper-password-manager--digital-vault-test-user)** требуется для создания в Keeper Password Manager & Digital Vault пользователя Britta Simon, связанного с соответствующим представлением в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в Keeper Password Manager & Digital Vault, выполните следующие действия:

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Keeper Password Manager & Digital Vault** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    ![Сведения о едином входе на домен и URL-адреса Keeper Password Manager & Digital Vault](common/sp-identifier-reply.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://{SSO CONNECT SERVER}/sso-connect/saml/login`.

    б) В текстовом поле **Идентификатор (сущности)** введите URL-адрес в следующем формате: `https://{SSO CONNECT SERVER}/sso-connect`.

    c. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://{SSO CONNECT SERVER}/sso-connect/saml/sso`.

    > [!NOTE]
    > Эти значения приведены для примера. Вместо них необходимо указать фактические значения URL-адреса входа, идентификатора и URL-адреса ответа. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов Keeper Password Manager & Digital Vault](https://keepersecurity.com/contact.html). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

5. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать нужный вам **XML метаданных федерации**, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

6. Требуемый URL-адрес можно скопировать из раздела **Настройка Keeper Password Manager & Digital Vault**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    б) Идентификатор Azure AD.

    в) URL-адрес выхода.

### <a name="configure-keeper-password-manager--digital-vault-single-sign-on"></a>Настройка единого входа на Keeper Password Manager & Digital Vault

Чтобы настроить единый вход на стороне **Keeper Password Manager & Digital Vault**, выполните инструкции в [Руководстве по поддержке Keeper](https://keepersecurity.com/assets/pdf/KeeperSSOConnect_v11.pdf).

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

В этом разделе описано, как позволить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к Keeper Password Manager & Digital Vault.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **Keeper Password Manager & Digital Vault**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Keeper Password Manager & Digital Vault**.

    ![Keeper Password Manager & Digital Vault в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-keeper-password-manager--digital-vault-test-user"></a>Создание тестового пользователя Keeper Password Manager & Digital Vault

Чтобы пользователи Azure AD могли выполнять вход в Keeper Password Manager & Digital Vault, они должны быть подготовлены в Keeper Password Manager & Digital Vault. Приложение поддерживает JIT-подготовку пользователей, поэтому после аутентификации пользователи будут созданы в приложении автоматически. Если вы хотите настроить пользователей вручную, обратитесь в [службу поддержки Keeper](https://keepersecurity.com/contact.html).

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

При нажатии на плитку "Keeper Password Manager & Digital Vault" на панели доступа вы автоматически войдете в Keeper Password Manager & Digital Vault, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

