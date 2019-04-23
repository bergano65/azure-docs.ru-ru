---
title: Руководство по Интеграция Azure Active Directory c IQNavigator VMS | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в IQNavigator VMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a8a09b25-dfa5-4c31-aea2-53bf1853b365
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: jeedes
ms.openlocfilehash: ad6bf2576d7f033f8ae029338dc94635dbba0fe7
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59267435"
---
# <a name="tutorial-azure-active-directory-integration-with-iqnavigator-vms"></a>Руководство по Интеграция Azure Active Directory c IQNavigator VMS

В этом руководстве описано, как интегрировать IQNavigator VMS с Azure Active Directory (Azure AD).
Интеграция Azure AD с приложением IQNavigator VMS обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к IQNavigator VMS.
* Вы можете включить автоматический вход пользователей в IQNavigator VMS (единый вход) с учетными записями Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с IQNavigator VMS, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* подписка IQNavigator VMS с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* В IQNavigator VMS поддерживается единый вход, инициируемый **поставщиком удостоверений**.

## <a name="adding-iqnavigator-vms-from-the-gallery"></a>Добавление IQNavigator VMS из коллекции

Чтобы настроить интеграцию IQNavigator VMS с Azure AD, необходимо добавить IQNavigator VMS из коллекции в список управляемых приложений SaaS.

**Чтобы добавить IQNavigator VMS из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **IQNavigator VMS**, выберите **IQNavigator VMS** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![IQNavigator VMS в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в IQNavigator VMS с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в IQNavigator VMS.

Чтобы настроить и проверить единый вход Azure AD в IQNavigator VMS, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в IQNavigator VMS](#configure-iqnavigator-vms-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя IQNavigator VMS](#create-iqnavigator-vms-test-user)** требуется для того, чтобы в IQNavigator VMS существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в IQNavigator VMS, сделайте следующее.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **IQNavigator VMS** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    ![Сведения о домене и URL-адресах единого входа для приложения IQNavigator VMS](common/idp-relay.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес: `iqn.com`

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в формате `https://<subdomain>.iqnavigator.com/security/login?client_name=https://sts.window.net/<instance name>`.

    c. Щелкните **Задать дополнительные URL-адреса**.

    d. В текстовом поле **Состояние ретранслятора** введите URL-адрес в формате `https://<subdomain>.iqnavigator.com`.

    > [!NOTE]
    > Эти значения приведены для примера. Замените их фактическими URL-адресом ответа и значением состояния ретранслятора. Чтобы получить эти значения, обратитесь к [группе поддержки IQNavigator VMS](https://www.beeline.com/iqn-product-support/). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

5. Приложение IQNavigator ожидает в утверждении идентификатора имени значение уникального идентификатора пользователя. Клиент может сопоставить правильное значение для утверждения идентификатора имени. В данном случае для примера мы сопоставили user.UserPrincipalName. Однако вам нужно сопоставить правильное значение, соответствующее параметрам вашей организации.

    ![image](common/edit-attribute.png)

6. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** нажмите кнопку "Копировать", чтобы копировать **URL-адрес метаданных федерации приложений** и сохранить его на компьютере.

    ![Ссылка для скачивания сертификата](common/copy-metadataurl.png)

### <a name="configure-iqnavigator-vms-single-sign-on"></a>Настройка единого входа в IQNavigator VMS

Чтобы настроить единый вход на стороне **IQNavigator VMS**, необходимо отправить **URL-адрес метаданных федерации приложения** в [группу поддержки IQNavigator VMS](https://www.beeline.com/iqn-product-support/). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

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

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к IQNavigator VMS.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **IQNavigator VMS**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. Из списка приложений выберите **IQNavigator VMS**.

    ![Ссылка на IQNavigator VMS в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-iqnavigator-vms-test-user"></a>Создание тестового пользователя IQNavigator VMS

В этом разделе описано, как создать пользователя Britta Simon в приложении IQNavigator VMS. Обратитесь к  [группе поддержки IQNavigator VMS](https://www.beeline.com/iqn-product-support/), чтобы добавить пользователей на платформу IQNavigator VMS. Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку IQNavigator VMS на панели доступа, вы автоматически войдете в приложение IQNavigator VMS, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)