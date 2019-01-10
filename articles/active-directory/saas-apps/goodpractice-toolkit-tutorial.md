---
title: Руководство. Интеграция Azure Active Directory с GoodPractice Toolkit | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и GoodPractice Toolkit.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 65b2979d-9e2f-4530-bc08-546975269ebc
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.openlocfilehash: 699a46dbb404e39c1f2005140b88a773832eb50c
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/27/2018
ms.locfileid: "53797193"
---
# <a name="tutorial-azure-active-directory-integration-with-goodpractice-toolkit"></a>Руководство. Интеграция Azure Active Directory с GoodPractice Toolkit

В этом руководстве описано, как интегрировать GoodPractice Toolkit с Azure Active Directory (Azure AD).
Интеграция Azure AD с GoodPractice Toolkit обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к GoodPractice Toolkit.
* Вы можете включить автоматический вход пользователей в GoodPractice Toolkit (единый вход) через учетную запись Azure Active Directory.
* Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с GoodPractice Toolkit, вам потребуется:

* подписка Azure AD; Если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).
* Подписка GoodPractice Toolkit с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства проводится настройка и проверка единого входа Azure AD в тестовой среде.

* GoodPractice Toolkit поддерживает единый вход, инициированный **поставщиком услуг**.
* GoodPractice Toolkit поддерживает **JIT**-подготовку пользователей.

## <a name="adding-goodpractice-toolkit-from-the-gallery"></a>Добавление GoodPractice Toolkit из коллекции

Чтобы настроить интеграцию GoodPractice Toolkit с Azure AD, необходимо добавить GoodPractice Toolkit из коллекции в список управляемых приложений SaaS.

**Чтобы добавить GoodPractice Toolkit из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"](common/add-new-app.png)

4. В поле поиска введите **GoodPractice Toolkit**, выберите **GoodPractice Toolkit** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![GoodPractice Toolkit в списке результатов](common/search-new-app.png)
## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в GoodPractice Toolkit с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure Active Directory и соответствующим пользователем GoodPractice Toolkit.

Чтобы настроить и проверить единый вход Azure AD в GoodPractice Toolkit, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в GoodPractice Toolkit](#configure-goodpractice-toolkit-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Создание тестового пользователя GoodPractice Toolkit](#create-goodpractice-toolkit-test-user)** требуется для того, чтобы в GoodPractice Toolkit существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
5. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в GoodPractice Toolkit, выполните следующие действия:

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **GoodPractice Toolkit** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    ![Сведения о домене и URL-адресах единого входа для приложения GoodPractice Toolkit](common/sp-intiated.png)

    В текстовом поле **URL-адрес входа** введите URL-адрес в следующем формате: `https://app.goodpractice.net/#/<subscriptionUrl>/s/<locationId> `.

    > [!Note]
    > Значение URL-адреса входа приведено для примера. Вместо него необходимо указать фактический URL-адрес входа. Чтобы получить это значение, обратитесь к [группе поддержки клиентов GoodPractice Toolkit](mailto:support@goodpractice.com).

5. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать нужный вам **XML метаданных федерации**, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

6. Скопируйте требуемый URL-адрес из раздела **Настройка GoodPractice Toolkit**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    a. URL-адрес входа.

    b. Идентификатор Azure AD.

    c. URL-адрес выхода

### <a name="configure-goodpractice-toolkit-single-sign-on"></a>Настройка единого входа GoodPractice Toolkit

Для настройки единого входа на стороне **GoodPractice Toolkit** необходимо отправить скачанный **XML метаданных федерации** и соответствующие скопированные URL-адреса с портала Azure [группе поддержки GoodPractice Toolkit](mailto:support@goodpractice.com). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

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

В этом разделе описано, как предоставить пользователю Britta Simon доступ к GoodPractice Toolkit, чтобы он мог использовать единый вход Azure.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **GoodPractice Toolkit**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **GoodPractice Toolkit**.

    ![Ссылка на GoodPractice Toolkit в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-goodpractice-toolkit-test-user"></a>Создание тестового пользователя GoodPractice Toolkit

В этом разделе вы создадите в GoodPractice Toolkit пользователя с именем Britta Simon. GoodPractice Toolkit поддерживает **JIT-подготовку**. Эта функция включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Если пользователь в GoodPractice Toolkit еще не существует, он создается при попытке доступа к GoodPractice Toolkit.

### <a name="test-single-sign-on"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку GoodPractice Toolkit на панели доступа, вы автоматически войдете в приложение GoodPractice Toolkit, для которого настроили единый вход. Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

