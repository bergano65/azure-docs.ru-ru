---
title: Руководство. Интеграция Azure Active Directory с KnowBe4 Security Awareness Training | Документация Майкрософт
description: Узнайте, как настраивать единый вход между Azure Active Directory и KnowBe4 Security Awareness Training.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b80d2212-cc5f-4adb-836c-570640810c39
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.openlocfilehash: 983571daa19a3658b134902ee328af142a3383c3
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/07/2019
ms.locfileid: "54065585"
---
# <a name="tutorial-azure-active-directory-integration-with-knowbe4-security-awareness-training"></a>Руководство. Интеграция Azure Active Directory с KnowBe4 Security Awareness Training

В этом руководстве вы узнаете, как интегрировать KnowBe4 Security Awareness Training с Azure Active Directory (Azure AD).
Интеграция KnowBe4 Security Awareness Training с Azure AD предоставляет следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к KnowBe4 Security Awareness Training.
* Вы можете позволить пользователям автоматически входить в KnowBe4 Security Awareness Training (единый вход) с использованием учетных записей Azure AD.
* Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с KnowBe4 Security Awareness Training, вам потребуется:

* подписка Azure AD; Если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).
* Подписка KnowBe4 Security Awareness Training с поддержкой единого входа

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства проводится настройка и проверка единого входа Azure AD в тестовой среде.

* Приложение KnowBe4 Security Awareness Training поддерживает единый вход инициированного **пакета обновления**.

* Приложение KnowBe4 Security Awareness Training поддерживает **JIT**-подготовку пользователей.

## <a name="adding-knowbe4-security-awareness-training-from-the-gallery"></a>Добавление KnowBe4 Security Awareness Training из коллекции.

Для настройки интеграции KnowBe4 Security Awareness Training с Azure AD необходимо добавить KnowBe4 Security Awareness Training из коллекции в список управляемых приложений SaaS.

**Чтобы добавить KnowBe4 Security Awareness Training из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"](common/add-new-app.png)

4. В поле поиска введите **KnowBe4 Security Awareness Training**, на панели результатов выберите **KnowBe4 Security Awareness Training** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![KnowBe4 Security Awareness Training в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD с KnowBe4 Security Awareness Training с использованием тестового пользователя **Britta Simon**.
Иными словами, для создания единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в KnowBe4 Security Awareness Training.

Чтобы настроить и проверить единый вход Azure AD в KnowBe4 Security Awareness Training, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа для приложения KnowBe4 Security Awareness Training](#configure-knowbe4-security-awareness-training-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя KnowBe4 Security Awareness Training](#create-knowbe4-security-awareness-training-test-user)** требуется для создания пользователя Britta Simon в KnowBe4 Security Awareness Training, связанного с представлением этого же пользователя в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в KnowBe4 Security Awareness Training, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **KnowBe4 Security Awareness Training** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    ![Сведения о домене и URL-адресах единого входа для приложения KnowBe4 Security Awareness Training](common/sp-identifier.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<companyname>.KnowBe4.com/auth/saml/<instancename>`.

    > [!NOTE]
    > Значение URL-адреса входа приведено только для примера. Вместо него необходимо указать фактический URL-адрес для входа. Обратитесь в [службу поддержки клиентов KnowBe4 Security Awareness Training](mailto:support@KnowBe4.com), чтобы получить это значение. Можно также обратиться к шаблонам, указанным в разделе **Базовая конфигурация SAML** на портале Azure.

    b. В текстовом поле **Идентификатор (сущности)** введите значение строки `KnowBe4`.

    > [!NOTE]
    > Это значение следует вводить с учетом регистра.

5. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Загрузить**, чтобы загрузить требуемый **сертификат (необработанный)** из предложенных вариантов и сохранить его на компьютере.

    ![Ссылка для скачивания сертификата](common/certificateraw.png)

6. Требуемый URL-адрес можно скопировать из раздела **Настройка приложения KnowBe4 Security Awareness Training**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    a. URL-адрес входа.

    b. Идентификатор Azure AD.

    c. URL-адрес выхода

### <a name="configure-knowbe4-security-awareness-training-single-sign-on"></a>Настройка единого входа для приложения KnowBe4 Security Awareness Training

Чтобы настроить единый вход на стороне **KnowBe4 Security Awareness Training**, необходимо отправить скачанный **сертификат (необработанный)** и соответствующие URL-адреса, скопированные с портала Azure в [группу поддержки KnowBe4 Security Awareness Training](mailto:support@KnowBe4.com). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

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

В этом разделе описано, как предоставить пользователю Britta Simon доступ к KnowBe4 Security Awareness Training, чтобы он мог использовать единый вход Azure.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем **KnowBe4 Security Awareness Training**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **KnowBe4 Security Awareness Training**.

    ![Ссылка на KnowBe4 Security Awareness Training в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-knowbe4-security-awareness-training-test-user"></a>Создание тестового пользователя KnowBe4 Security Awareness Training

В этом разделе описано, как создать пользователя с именем Britta Simon в приложении KnowBe4 Security Awareness Training. Приложение KnowBe4 Security Awareness Training поддерживает JIT-подготовку, включенную по умолчанию.

В этом разделе никакие действия с вашей стороны не требуются. Новый пользователь будет создан при попытке получить доступ к приложению KnowBe4 Security Awareness Training (если он еще не создан).

> [!NOTE]
> Если необходимо создать пользователя вручную, свяжитесь со [службой поддержки KnowBe4 Security Awareness Training](mailto:support@KnowBe4.com).

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "KnowBe4 Security Awareness Training" на панели доступа, вы автоматически войдете в приложение KnowBe4 Security Awareness Training, для которого был настроен единый вход. Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)