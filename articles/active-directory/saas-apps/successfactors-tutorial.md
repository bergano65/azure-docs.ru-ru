---
title: Руководство. Интеграция Azure Active Directory с SuccessFactors | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и SuccessFactors.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/3/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2281fb046ca9b96aa5800150d630b9086236c5c5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57846928"
---
# <a name="tutorial-azure-active-directory-integration-with-successfactors"></a>Руководство по Интеграция Azure Active Directory с SuccessFactors

В этом руководстве описано, как интегрировать SuccessFactors с Azure Active Directory (Azure AD).
Интеграция SuccessFactors с Azure AD обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к приложению SuccessFactors.
* Вы можете включить автоматический вход для пользователей в SuccessFactors (единый вход) с помощью учетных записей Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с SuccessFactors, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* Подписка SuccessFactors с поддержкой единого входа

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* SuccessFactors поддерживает единый вход инициированного **пакета обновления**.

## <a name="adding-successfactors-from-the-gallery"></a>Добавление SuccessFactors из коллекции.

Чтобы настроить интеграцию SuccessFactors с Azure AD, необходимо добавить SuccessFactors из коллекции в список управляемых приложений SaaS.

**Чтобы добавить SuccessFactors из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **SuccessFactors**, выберите **SuccessFactors** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![SuccessFactors в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD с помощью SuccessFactors с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в SuccessFactors.

Чтобы настроить и проверить единый вход Azure AD в SuccessFactors, вам потребуется выполнить действия, описанные в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в SuccessFactors](#configure-successfactors-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя SuccessFactors](#create-successfactors-test-user)** требуется для создания пользователя Britta Simon в SuccessFactors, связанного с соответствующим представлением пользователя в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в SuccessFactors, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **SuccessFactors** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    ![Сведения о домене и URL-адресах единого входа приложения SuccessFactors](common/sp-identifier-reply.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате:

    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.eu`|

    b. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате:

    | |
    |--|
    | `https://www.successfactors.com/<companyname>`|
    | `https://www.successfactors.com`|
    | `https://<companyname>.successfactors.eu`|
    | `https://www.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://hcm4preview.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.eu`|
    | `https://www.successfactors.cn`|
    | `https://www.successfactors.cn/<companyname>`|

    c. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате:

    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.successfactors.com`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.successfactors.eu`|
    | `https://<companyname>.sapsf.eu`|
    | `https://<companyname>.sapsf.eu/<companyname>`|
    | `https://<companyname>.sapsf.cn`|
    | `https://<companyname>.sapsf.cn/<companyname>`|

    > [!NOTE]
    > Эти значения приведены для примера. Укажите вместо них фактические значения URL-адреса для входа, идентификатора и URL-адреса ответа. Чтобы узнать эти адреса, обратитесь в [службу поддержки клиентов SuccessFactors](https://www.successfactors.com/content/ssf-site/en/support.html).

5. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Загрузить**, чтобы загрузить требуемый **сертификат (Base64)** из предложенных вариантов, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

6. Скопируйте требуемый URL-адрес из раздела **Set up SuccessFactors** (Настройка SuccessFactors).

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    б) Идентификатор Azure AD.

    в) URL-адрес выхода.

### <a name="configure-successfactors-single-sign-on"></a>Настройка единого входа в SuccessFactors

1. В другом окне веб-браузера войдите на **портал администрирования SuccessFactors** с правами администратора.

2. Откройте раздел **Application Security** (Безопасность приложения) и найдите в нем **функцию единого входа**.

3. Введите любое значение в поле **Reset Token** (Сброс маркера) и нажмите кнопку **Save Token** (Сохранить маркер), чтобы включить единый вход SAML.

    ![Настройка единого входа на стороне приложения][11]

    > [!NOTE]
    > Это значение используется как признак включения. Если здесь сохранено любое значение, единый вход SAML включен. Если сохранено пустое значение, единый вход SAML отключен.

4. Перейдите на экран, представленный на снимке ниже, и сделайте следующее.

    ![Настройка единого входа на стороне приложения][12]
  
    a. Установите переключатель **SAML v2 SSO** (Единый вход SAML версии 2).
  
    b. Задайте **имя утверждающей стороны SAML** (например, "Издатель SAML + название компании").

    c. В текстовое поле **Issuer URL** (URL-адрес издателя) вставьте значение **Идентификатор Azure AD**, скопированное на портале Azure.

    d. Выберите **Assertion** (Утверждение) в качестве значения **Require Mandatory Signature** (Требуется обязательная подпись).

    д. Выберите значение **Enabled** (Включено) для параметра **Enable SAML Flag** (Включить флаг SAML).

    Е. Выберите значение **No** (Нет) для параметра **Login Request Signature (SF Generated/SP/RP)** (Подпись запроса на вход (создается SF/SP/RP)).

    ж. Выберите значение **Browser/Post Profile** (Браузер/пост-профилирование) для параметра **SAML Profile** (Профиль SAML).

    h. Выберите значение **No** (Нет) для параметра **Enforce Certificate Valid Period** (Применять период действия сертификата).

    i. Скопируйте содержимое загруженного файла сертификата с портала Azure и вставьте его в текстовое поле **SAML Verifying Certificate** (Сертификат для проверки SAML).

    > [!NOTE] 
    > Содержимое сертификата должно включать открывающий и закрывающий теги сертификата.

5. Перейдите в раздел SAML V2 и выполните следующие действия.

    ![Настройка единого входа на стороне приложения][13]

    a. Выберите значение **Yes** (Да) для параметра **Support SP-initiated Global Logout** (Поддержка глобального выхода, инициированного SP).

    b. В текстовое поле **Global Logout Service URL (LogoutRequest destination)** (URL-адрес службы глобального выхода) вставьте значение **URL-адреса выхода**, скопированное с портала Azure.

    c. Выберите значение **No** (Нет) для параметра **Require sp must encrypt all NameID element** (Требуется шифрование всех элементов NameID на стороне SP).

    d. Выберите значение **unspecified** (Не определено) для параметра **NameID Format** (Формат идентификатора имени).

    д. Выберите значение **Yes** (Да) для параметра **Enable sp initiated login (AuthnRequest)** (Включить вход по требованию SP).

    Е. В текстовое поле **Send request as Company-Wide issuer** (Отправлять запрос как издатель в пределах компании) вставьте значение **URL-адрес входа**, скопированное на портале Azure.

6. Чтобы в именах для входа не учитывался регистр, сделайте следующее.

    ![Настройка единого входа][29]

    a. Найдите в нижней части экрана раздел **Company Settings**(Параметры компании).

    b. Установите флажок **Enable Non-Case-Sensitive Username** (Разрешить имя пользователя без учета регистра).

    c. Выберите команду **Сохранить**.

    > [!NOTE]
    > При попытке включить этот параметр система проверит, не возникнут ли повторяющиеся имена для входа SAML. Предположим, что у клиента есть пользователи с именами User1 и user1. Если отменить учет регистра, эти имена будут считаться одинаковыми. В таком случае система выдаст сообщение об ошибке и параметр не будет включен. Клиенту потребуется изменить одно из таких имен пользователя, чтобы они имели различное написание.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    а. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon\@<домен_вашей_компании>.<доменная_зона>**.  
    Например BrittaSimon@contoso.com.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как предоставить пользователю Britta Simon доступ к SuccessFactors, чтобы он мог использовать единый вход Azure.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем **SuccessFactors**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. Из списка приложений выберите **SuccessFactors**.

    ![Ссылка SuccessFactors в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-successfactors-test-user"></a>Создание тестового пользователя SuccessFactors

Чтобы пользователи Azure AD могли выполнять вход в SuccessFactors, они должны быть подготовлены для работы с SuccessFactors. В случае SuccessFactors подготовка пользователей осуществляется вручную.

Чтобы создать пользователей в SuccessFactors, необходимо обратиться в [службу поддержки SuccessFactors](https://www.successfactors.com/content/ssf-site/en/support.html).

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку "SuccessFactors" на панели доступа, вы автоматически войдете в приложение SuccessFactors, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[11]: ./media/successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/successfactors-tutorial/tutorial_successfactors_09.png
[29]: ./media/successfactors-tutorial/tutorial_successfactors_10.png
