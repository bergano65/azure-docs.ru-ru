---
title: Руководство по Интеграция Azure Active Directory с TOPdesk — Secure | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и TOPdesk - Secure.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8e06ee33-18f9-4c05-9168-e6b162079d88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3125f5b9075a69289bd6a44d62c3d06d5e6d910d
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161124"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>Руководство по Интеграция Azure Active Directory с TOPdesk — Secure

В этом руководстве описано, как интегрировать TOPdesk - Secure с Azure Active Directory (Azure AD).
Интеграция Azure AD с приложением TOPdesk - Secure обеспечивает следующие преимущества.

* C помощью Azure AD вы можете контролировать доступ к TOPdesk - Secure.
* Вы можете включить автоматический вход пользователей в TOPdesk — Secure (единый вход) с помощью учетных записей Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с TOPdesk - Secure, вам потребуется следующее:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* Подписка TOPdesk — Secure с поддержкой единого входа

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* TOPdesk — Secure поддерживает единый вход инициированного **пакета обновления**.

## <a name="adding-topdesk---secure-from-the-gallery"></a>Добавление TOPdesk - Secure из коллекции

Чтобы настроить интеграцию TOPdesk - Secure с Azure AD, необходимо добавить TOPdesk - Secure из коллекции в список управляемых приложений SaaS.

**Чтобы добавить TOPdesk - Secure из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **TOPdesk - Secure**, выберите **TOPdesk - Secure** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![TOPdesk - Secure в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD с TOPdesk — Secure с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в TOPdesk — Secure.

Чтобы настроить и проверить единый вход Azure AD в TOPdesk - Secure, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в TOPdesk — Secure](#configure-topdesk---secure-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя TOPdesk — Secure](#create-topdesk---secure-test-user)** требуется для того, чтобы в TOPdesk — Secure существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в TOPdesk — Secure, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **TOPdesk — Secure** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    ![Сведения о домене и URL-адресах единого входа для приложения TOPdesk - Secure](common/sp-identifier-reply.png)

    a. В текстовое поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<companyname>.topdesk.net`.

    b. В поле **URL-адрес идентификатора** введите URL-адрес метаданных TOPdesk, который вы можете скопировать из конфигурации TOPdesk. При этом нужно использовать такой формат: `https://<companyname>.topdesk.net/saml-metadata/<identifier>`.

    c. В текстовом поле **URL-адрес ответа** введите URL-адрес в формате `https://<companyname>.topdesk.net/tas/secure/login/verify`.

    > [!NOTE]
    > Эти значения приведены для примера. Укажите вместо них фактические значения URL-адреса для входа, идентификатора и URL-адреса ответа. Чтобы получить эти данные, обратитесь к [группе поддержки клиентов TOPdesk - Secure](https://www.topdesk.com/us/support/). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

5. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать нужный вам **XML метаданных федерации**, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

6. Скопируйте соответствующий вашим требованиям URL-адрес из раздела **Настройка TOPdesk — Secure**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    б) Идентификатор Azure AD.

    в) URL-адрес выхода.

### <a name="configure-topdesk---secure-single-sign-on"></a>Настройка единого входа в TOPdesk — Secure

1. Выполните вход на веб-сайт **TOPdesk — Secure** своей компании в качестве администратора.

2. В меню **TOPdesk** выберите пункт **Settings** (Параметры).

    ![Параметры](./media/topdesk-secure-tutorial/ic790598.png "Параметры")

3. Щелкните **Параметры входа**.

    ![Параметры входа](./media/topdesk-secure-tutorial/ic790599.png "Параметры входа")

4. Разверните меню **Login Settings** (Параметры входа), а затем выберите **General** (Общие).

    ![Общие сведения](./media/topdesk-secure-tutorial/ic790600.png "Общие")

5. В разделе **Secure** (Безопасность) для конфигурации **SAML login** (Вход SAML) сделайте следующее.

    ![Технические параметры](./media/topdesk-secure-tutorial/ic790855.png "Технические параметры")

    a. Нажмите кнопку **Скачать** , чтобы скачать общедоступный файл метаданных, а затем сохраните его локально на компьютере.

    b. Откройте файл метаданных и определите местоположение узла **AssertionConsumerService** .

    ![Assertion Consumer Service](./media/topdesk-secure-tutorial/ic790856.png "Assertion Consumer Service")

    c. Скопируйте значение **AssertionConsumerService**, вставьте это значение в текстовое поле "URL-адрес ответа" в разделе **Домен и URL-адреса TOPdesk - Secure**.

6. Чтобы создать файл сертификата, выполните следующие действия:

    ![Certificate](./media/topdesk-secure-tutorial/ic790606.png "Сертификат")

    a. Откройте скачанный файл метаданных на портале Azure.

    b. Разверните узел **RoleDescriptor**, содержащий **xsi:type** со значением **fed:ApplicationServiceType**.

    c. Скопируйте значение узла **X509Certificate** .

    d. Сохраните скопированное значение узла **X509Certificate** локально в файл на компьютере.

7. В разделе **Public** (Общедоступные) щелкните **Add** (Добавить).

    ![Добавление](./media/topdesk-secure-tutorial/ic790607.png "Добавить")

8. На странице диалогового окна **Помощник настройки SAML** сделайте следующее:

    ![Помощник настройки SAML](./media/topdesk-secure-tutorial/ic790608.png "Помощник настройки SAML")

    a. Чтобы отправить скачанный файл метаданных через портал Azure, напротив пункта **Federation Metadata** (Метаданные федерации) нажмите кнопку **Browse** (Обзор).

    b. Чтобы отправить файл сертификата, напротив пункта **Certificate (RSA)** (Сертификат (RSA)) нажмите кнопку **Browse** (Обзор).

    c. Для **закрытого ключа (RSA, PKCS8, DER)** вы можете загрузить свой собственный закрытый ключ или связаться с [группой поддержки клиентов TOPdesk — Secure ](https://www.topdesk.com/us/support), чтобы получить закрытый ключ.

    d. Чтобы отправить файл с логотипом, полученный от службы поддержки TOPdesk, напротив пункта **Logo icon** (Значок логотипа) нажмите кнопку **Browse** (Обзор).

    д. В текстовое поле **Атрибут имени пользователя** введите значение `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    Е. В текстовом поле **Отображаемое имя** введите имя конфигурации.

    ж. Выберите команду **Сохранить**.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD 

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    а. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon\@домен_вашей_компании.доменная_зона**.  
    Например BrittaSimon@contoso.com.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к TOPdesk - Secure.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **TOPdesk — Secure**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений введите и выберите **TOPdesk — Secure**.

    ![Ссылка на TOPdesk - Secure в списке "Приложения"](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-topdesk---secure-test-user"></a>Создание тестового пользователя TOPdesk — Secure

Чтобы пользователи Azure AD могли выполнять вход в систему TOPdesk — Secure, они должны быть подготовлены для нее.  
В случае TOPdesk — Secure подготовка пользователей осуществляется вручную.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.

1. Выполните вход на веб-сайт **TOPdesk — Secure** компании в качестве администратора.

2. В меню в верхней части страницы щелкните **TOPdesk \> New (Создать) \> Support Files (Файлы поддержки) \> Operator (Оператор)** .

    ![Оператор](./media/topdesk-secure-tutorial/ic790610.png "Оператор")

3. В диалоговом окне **Новый оператор** выполните следующие действия:

    ![Создание оператора](./media/topdesk-secure-tutorial/ic790611.png "Новый оператор")

    a. Перейдите на вкладку **General** (Общие).

    b. В текстовое поле **Last Name** (Фамилия) введите фамилию пользователя, например **Simon**.

    c. Выберите для этой учетной записи **Site** (Веб-сайт) в разделе **Location** (Расположение).

    d. В текстовом поле **Login Name** (Имя для входа в систему) в разделе **TOPdesk Login** (Вход в TOPdesk) введите имя для входа пользователя.

    д. Выберите команду **Сохранить**.

> [!NOTE]
> Вы можете использовать любые другие инструменты создания учетных записей пользователей TOPdesk — Secure или API-интерфейсы, предоставляемые TOPdesk — Secure для подготовки учетных записей пользователя AAD.

### <a name="test-single-sign-on"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

При нажатии на плитку "TOPdesk — Secure" на панели доступа вы автоматически войдете в TOPdesk — Secure, для которой настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

