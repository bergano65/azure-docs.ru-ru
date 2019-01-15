---
title: Руководство. Интеграция Azure Active Directory с Palo Alto Networks (Captive Portal) | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Palo Alto Networks (Captive Portal).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 67a0b476-2305-4157-8658-2ec3625850d5
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.openlocfilehash: eff08cc17f475e2b6ad6406e463de27371bbe5b1
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/07/2019
ms.locfileid: "54064735"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---captive-portal"></a>Руководство. Интеграция Azure Active Directory с Palo Alto Networks (Captive Portal)

В этом руководстве описано, как интегрировать Palo Alto Networks (Captive Portal) с Azure Active Directory (Azure AD).
Интеграция Azure AD с Palo Alto Networks (Captive Portal) обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к Palo Alto Networks (Captive Portal).
* Вы можете включить автоматический вход пользователей в Palo Alto Networks (Captive Portal) (единый вход) с использованием учетных записей Azure AD.
* Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Palo Alto Networks (Captive Portal), вам потребуется:

* подписка Azure AD; Если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).
* Подписка Palo Alto Networks (Captive Portal) с поддержкой единого входа

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства проводится настройка и проверка единого входа Azure AD в тестовой среде.

* Palo Alto Networks (Captive Portal) — единый вход инициированной **выдающей точки распространения**

* Palo Alto Networks (Captive Portal) поддерживает **JIT**-подготовку пользователей.

## <a name="adding-palo-alto-networks---captive-portal-from-the-gallery"></a>Добавление Palo Alto Networks (Captive Portal) из коллекции

Чтобы настроить интеграцию Palo Alto Networks (Captive Portal) с Azure AD, необходимо добавить Palo Alto Networks (Captive Portal) из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Palo Alto Networks (Captive Portal) из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"](common/add-new-app.png)

4. В поле поиска введите **Palo Alto Networks (Captive Portal)** и выберите **Palo Alto Networks (Captive Portal)** на панели результатов, а затем нажмите кнопку **Добавить**, чтобы добавить приложение.

     ![Palo Alto Networks (Captive Portal) в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Palo Alto Networks (Captive Portal) с использованием тестового пользователя **Britta Simon**.
Для правильной работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем Palo Alto Networks (Captive Portal).

Чтобы настроить и проверить единый вход Azure AD в Palo Alto Networks (Captive Portal), вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в Palo Alto Networks (Captive Portal)](#configure-palo-alto-networks---captive-portal-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя Palo Alto Networks (Captive Portal)](#create-palo-alto-networks---captive-portal-test-user)** требуется для того, чтобы в Palo Alto Networks (Captive Portal) существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в Palo Alto Networks (Captive Portal), сделайте следующее.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Palo Alto Networks (Captive Portal)** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. В диалоговом окне **Базовая конфигурация SAML** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения Palo Alto Networks (Captive Portal)](common/idp-intiated.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в формате `https://<Customer Firewall Hostname>/SAML20/SP`.

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<Customer Firewall Hostname>/SAML20/SP/ACS`.

    > [!NOTE]
    > Эти значения приведены в качестве примера. Измените их на фактические значения идентификатора и URL-адреса ответа. Чтобы получить эти значения, обратитесь в [группу поддержки клиентов Palo Alto Networks (Captive Portal)](https://support.paloaltonetworks.com/support). Можно также обратиться к шаблонам, указанным в разделе **Базовая конфигурация SAML** на портале Azure.

5. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать нужный вам **XML метаданных федерации**, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

### <a name="configure-palo-alto-networks---captive-portal-single-sign-on"></a>Настройка единого входа для Palo Alto Networks (Captive Portal)

1. В другом окне браузера откройте сайт Palo Alto с правами администратора.

2. Щелкните **Device** (Устройство).

    ![Настройка единого входа в Palo Alto](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

3. Чтобы импортировать файл метаданных, в левой области навигации выберите **SAML Identity Provider** (Поставщик удостоверений SAML) и нажмите кнопку Import (Импорт).

    ![Настройка единого входа в Palo Alto](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Сделайте следующее в окне Import (Импорт).

    ![Настройка единого входа в Palo Alto](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    a. Укажите имя в текстовом поле **Profile Name** (Имя профиля), например "Пользовательский интерфейс администратора Azure AD".
    
    b. В разделе **Identity Provider Metadata** (Метаданные поставщика удостоверений) щелкните **Browse** (Обзор) и выберите файл metadata.xml, загруженный ранее с портала Azure.
    
    c. Щелкните **ОК**

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

В этом разделе описано, как предоставить пользователю Britta Simon доступ к Palo Alto Networks (Captive Portal), чтобы он мог использовать единый вход Azure.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **Palo Alto Networks (Captive Portal)**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений введите и выберите **Palo Alto Networks (Captive Portal)**.

    ![Ссылка на Palo Alto Networks (Captive Portal) в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-palo-alto-networks---captive-portal-test-user"></a>Создание тестового пользователя в Palo Alto Networks (Captive Portal)

В этом разделе создается пользователь Britta Simon в приложении Palo Alto Networks Captive Portal. Palo Alto Networks (Captive Portal) поддерживает **JIT-подготовку**. Эта функция включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Если пользователь еще не существует в Palo Alto Networks (Captive Portal), он создается после проверки подлинности.

> [!NOTE]
> Чтобы создать пользователя вручную, обратитесь в [группу поддержки клиентов Palo Alto Networks (Captive Portal)](https://support.paloaltonetworks.com/support).

### <a name="test-single-sign-on"></a>Проверка единого входа 

На виртуальной машине Windows портал авторизации Captive Portal настроен за брандмауэром. Для тестирования единого входа на портал Captive Portal войдите на виртуальную машину Windows с помощью RDP. Откройте в браузере любой веб-сайт в рамках сеанса RDP. Браузер должен автоматически открыть URL-адрес единого входа и запросить проверку подлинности. После завершения проверки подлинности должна появиться возможность перехода на веб-сайты.

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

