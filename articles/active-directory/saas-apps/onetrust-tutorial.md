---
title: Руководство по Интеграция Azure Active Directory с OneTrust Privacy Management Software | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и OneTrust Privacy Management Software.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 71c2b6d0-3d28-4130-a2c8-1e72ab3d5814
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/13/2019
ms.author: jeedes
ms.openlocfilehash: 5fbec8b6a1a21826896f8e2499b1e8b7237d0ff9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67095898"
---
# <a name="tutorial-azure-active-directory-integration-with-onetrust-privacy-management-software"></a>Руководство по Интеграция Azure Active Directory с OneTrust Privacy Management Software

В этом руководстве описано, как интегрировать OneTrust Privacy Management Software с Azure Active Directory (Azure AD).
Интеграция OneTrust Privacy Management Software с Azure AD обеспечивает следующие преимущества.

* С помощью Azure AD можно контролировать доступ к OneTrust Privacy Management Software.
* Вы можете включить автоматический вход пользователей в OneTrust Privacy Management Software (единый вход) с помощью учетных записей Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с OneTrust Privacy Management Software, вам потребуется следующее:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* подписка OneTrust Privacy Management Software с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* OneTrust Privacy Management Software поддерживает единый вход, инициированный **поставщиком услуг** и **поставщиком удостоверений**.

* Приложение OneTrust Privacy Management Software поддерживает **JIT**-подготовку пользователей.

## <a name="adding-onetrust-privacy-management-software-from-the-gallery"></a>Добавление OneTrust Privacy Management Software из коллекции

Чтобы настроить интеграцию OneTrust Privacy Management Software с Azure AD, необходимо добавить OneTrust Privacy Management Software из коллекции в список управляемых приложений SaaS.

**Чтобы добавить OneTrust Privacy Management Software из коллекции, выполните следующее.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **OneTrust Privacy Management Software**, выберите **OneTrust Privacy Management Software** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![OneTrust Privacy Management Software в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в OneTrust Privacy Management Software с использованием тестового пользователя **Britta Simon**.
Чтобы обеспечить единый вход, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в OneTrust Privacy Management Software.

Чтобы настроить и проверить единый вход в Azure AD в OneTrust Privacy Management Software, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в OneTrust Privacy Management Software](#configure-onetrust-privacy-management-software-single-sign-on)** требуется, чтобы определить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя OneTrust Privacy Management Software](#create-onetrust-privacy-management-software-test-user)** требуется для создания пользователя Britta Simon в OneTrust Privacy Management Software, связанного с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в OneTrust Privacy Management Software, сделайте следующее.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **OneTrust Privacy Management Software** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. Если вы хотите настроить приложение в режиме, инициируемом **поставщиком удостоверений**, в разделе **Базовая конфигурация SAML** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения OneTrust Privacy Management Software](common/idp-intiated.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес: `https://www.onetrust.com/saml2`

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в формате `https://<subdomain>.onetrust.com/auth/consumerservice`.

5. Чтобы настроить приложение для работы в режиме, инициируемом **поставщиком услуг**, щелкните **Задать дополнительные URL-адреса** и выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения OneTrust Privacy Management Software](common/metadata-upload-additional-signon.png)

    В текстовом поле **URL-адрес входа** введите URL-адрес в формате `https://<subdomain>.onetrust.com/auth/login`.

    > [!NOTE]
    > Эти значения приведены для примера. Замените их фактическими значениями URL-адреса ответа и URL-адреса входа. Обратитесь к [группе поддержки клиентов OneTrust Privacy Management Software](mailto:support@onetrust.com), чтобы получить эти значения. Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

6. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать нужный вам **XML метаданных федерации**, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

7. Требуемые URL-адреса можно скопировать из раздела **Настройка OneTrust Privacy Management Software**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    b. Идентификатор Azure AD

    c. URL-адрес выхода.

### <a name="configure-onetrust-privacy-management-software-single-sign-on"></a>Настройка единого входа OneTrust Privacy Management Software

Чтобы настроить единый вход на стороне **OneTrust Privacy Management Software**, нужно отправить скачанный файл **XML метаданных федерации** и соответствующие URL-адреса, скопированные на портале Azure, в [группу поддержки OneTrust Privacy Management Software](mailto:support@onetrust.com). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD 

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    а. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon@yourcompanydomain.extension** .  
    Например BrittaSimon@contoso.com.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как включить единый вход Azure для пользователя Britta Simon и предоставить этому пользователю доступ к OneTrust Privacy Management Software.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **OneTrust Privacy Management Software**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. Из списка приложений выберите **OneTrust Privacy Management Software**.

    ![Ссылка на OneTrust Privacy Management Software в списке "Приложения"](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-onetrust-privacy-management-software-test-user"></a>Создание тестового пользователя OneTrust Privacy Management Software

В этом разделе вы создадите в OneTrust Privacy Management Software пользователя с именем Britta Simon. Приложение OneTrust Privacy Management Software поддерживает JIT-подготовку пользователей. Эта функция включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Если пользователь еще не существует в OneTrust Privacy Management Software, он создается после проверки подлинности.

>[!Note]
>Если необходимо вручную создать пользователя, обратитесь к [группе поддержки OneTrust Privacy Management Software](mailto:support@onetrust.com).

### <a name="test-single-sign-on"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку OneTrust Privacy Management Software на Панели доступа, вы автоматически войдете в приложение OneTrust Privacy Management Software, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что такое условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

