---
title: Руководство. Интеграция Azure Active Directory с CorpTax | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в CorpTax.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: fdad2893-41f4-4056-b6d2-ec3d050eb350
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/17/2018
ms.author: jeedes
ms.openlocfilehash: 1949e8fc648aac5772c12d2c38e233dc53ca8b17
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/27/2018
ms.locfileid: "53797119"
---
# <a name="tutorial-azure-active-directory-integration-with-corptax"></a>Руководство. Интеграция Azure Active Directory с CorpTax

В этом руководстве описано, как интегрировать CorpTax с Azure Active Directory (Azure AD).
Интеграция Azure AD с CorpTax обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к CorpTax.
* Вы можете включить автоматический вход пользователей в CorpTax (единый вход) с помощью учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с CorpTax, вам потребуется:

* подписка Azure AD; Если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).
* подписка с поддержкой единого входа в CorpTax.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства проводится настройка и проверка единого входа Azure AD в тестовой среде.

* CorpTax поддерживает единый вход, инициированный **поставщиком услуг**.

## <a name="adding-corptax-from-the-gallery"></a>Добавление CorpTax из коллекции

Чтобы настроить интеграцию CorpTax с Azure AD, необходимо добавить CorpTax из коллекции в список управляемых приложений SaaS.

**Чтобы добавить CorpTax из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](common/select_azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise_applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"](common/add_new_app.png)

4. В поле поиска введите **CorpTax**, выберите **CorpTax** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![CorpTax в списке результатов](common/search_new_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в CorpTax с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в CorpTax.

Чтобы настроить и проверить единый вход Azure AD в CorpTax, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в CorpTax](#configure-corptax-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Создание тестового пользователя приложения CorpTax](#create-corptax-test-user)** требуется, чтобы в CorpTax существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
5. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в CorpTax, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **CorpTax** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select_sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select_saml_option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit_urls.png)

4. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    ![Сведения о домене и URL-адресах для единого входа в приложение CorpTax](common/sp_intiated.png)

    В текстовом поле **URL-адрес входа** введите URL-адрес: `https://asp.corptax.com`.

8. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** нажмите кнопку **Скачать**, чтобы скачать **XML метаданных федерации** и сохранить его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

### <a name="configure-corptax-single-sign-on"></a>Настройка единого входа CorpTax

Чтобы настроить единый вход на стороне **CorpTax**, нужно отправить скачанный **XML метаданных федерации** [группе поддержки CorpTax](mailto:bgraves@corptax.com). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new_user.png)

3. В разделе свойств пользователя сделайте следующее.

    ![Диалоговое окно "Пользователь"](common/user_properties.png)

    a. В поле **Имя** введите **BrittaSimon**.

    b. В поле **Имя пользователя** введите **brittasimon@yourcompanydomain.extension**.  
    Например, BrittaSimon@contoso.com

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к CorpTax.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем **CorpTax**.

    ![Колонка "Корпоративные приложения"](common/enterprise_applications.png)

2. В списке приложений введите и выберите **CorpTax**.

    ![Ссылка на CorpTax в списке приложений](common/all_applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users_groups_blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add_assign_user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-corptax-test-user"></a>Создание тестового пользователя CorpTax

В этом разделе описано, как создать пользователя Britta Simon в приложении CorpTax. Обратитесь в  [службу поддержки CorpTax](mailto:bgraves@corptax.com), чтобы добавить пользователей на платформу CorpTax. Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.
Когда вы щелкните плитку CorpTax на панели доступа, вы будете перенаправлены на страницу CorpTax. 

![изображение](media/corptax-tutorial/corptaxlogin.png)

В текстовом поле **Среда** введите соответствующую среду и вы автоматически войдете в приложение CorpTax, для которого настроили единый вход. Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
