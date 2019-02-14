---
title: Руководство. Интеграция Azure Active Directory с GoToMeeting | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и GoToMeeting.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: bcaf19f2-5809-4e1c-acbc-21a8d3498ccf
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: be72216f33912f8740979e11edc9427a83fb0350
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56174730"
---
# <a name="tutorial-azure-active-directory-integration-with-gotomeeting"></a>Руководство по Интеграция Azure Active Directory с GoToMeeting

В этом руководстве описано, как интегрировать GoToMeeting с Azure Active Directory (Azure AD).
Интеграция GoToMeeting с Azure AD обеспечивает перечисленные ниже преимущества.

* С помощью Azure AD вы можете контролировать доступ к GoToMeeting.
* Вы можете включить автоматический вход пользователей в GoToMeeting (единый вход) с помощью учетных записей Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с GoToMeeting, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* Подписка GoToMeeting с поддержкой единого входа

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* GoToMeeting поддерживает единый вход инициированного **пакета обновления**.

## <a name="adding-gotomeeting-from-the-gallery"></a>Добавление GoToMeeting из коллекции.

Чтобы настроить интеграцию GoToMeeting с Azure AD, вам нужно добавить GoToMeeting из коллекции в список управляемых приложений SaaS.

**Чтобы добавить GoToMeeting из коллекции, выполните указанные ниже действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **GoToMeeting**, выберите **GoToMeeting** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![GoToMeeting в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в GoToMeeting с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в GoToMeeting.

Чтобы настроить и проверить единый вход Azure AD в GoToMeeting, вам нужно выполнить действия в перечисленных ниже стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в GoToMeeting](#configure-gotomeeting-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя приложения GoToMeeting](#create-gotomeeting-test-user)** требуется для того, чтобы в GoToMeeting существовал пользователь Britta Simon, связанный с представлением такого же пользователя в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в GoToMeeting, выполните указанные ниже действия.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **GoToMeeting** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. На странице **Настройка единого входа с помощью SAML** нажмите кнопку **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Сведения о домене и URL-адресах единого входа для приложения GoToMeeting](common/both-allurls.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в формате `https://authentication.logmeininc.com/saml/sp`.

    б) В текстовом поле **URL-адрес ответа** введите URL-адрес в формате `https://authentication.logmeininc.com/saml/acs`.

    c. Щелкните **Задать дополнительные URL-адреса** и настройте следующие URL-адреса.

    4.3. **URL-адрес входа** (оставьте это поле пустым).

    д. В текстовом поле **RelayState** введите URL-адрес в таком формате:

    - Для приложения GoToMeeting используйте `https://global.gotomeeting.com`

    - Для GoToTraining используйте `https://global.gototraining.com`

    - Для GoToWebinar используйте `https://global.gotowebinar.com` 

    - Для GoToAssist используйте `https://app.gotoassist.com`

    > [!NOTE]
    > Эти значения приведены для примера. Измените их на фактические значения идентификатора и URL-адреса ответа. Чтобы получить эти значения, обратитесь в [группу поддержки клиентов GoToMeeting](https://go.microsoft.com/fwlink/?linkid=845985). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

5. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Загрузить**, чтобы загрузить требуемый **сертификат (Base64)** из предложенных вариантов, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

6. Требуемый URL-адрес можно скопировать из раздела **Настройка GoToMeeting**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    б) Идентификатор Azure AD.

    в) URL-адрес выхода.

### <a name="configure-gotomeeting-single-sign-on"></a>Настройка единого входа в GoToMeeting

1. В другом окне браузера войдите в [центр организации GoToMeeting](https://organization.logmeininc.com/). Вы увидите запрос на подтверждение обновления поставщика удостоверений.

2. Установите флажок My Identity Provider has been updated with the new domain (Мой поставщик удостоверений обновлен с помощью нового домена). По завершении нажмите кнопку **Готово**.

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

В этом разделе описано, как предоставить пользователю Britta Simon доступ к GoToMeeting, чтобы он мог использовать единый вход Azure.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **GoToMeeting**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **GoToMeeting**.

    ![Ссылка на GoToMeeting в списке "Приложения"](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-gotomeeting-test-user"></a>Создание тестового пользователя GoToMeeting

В этом разделе вы создадите в GoToMeeting пользователя с именем Britta Simon. Приложение GoToMeeting поддерживает JIT-подготовку. Эта функция включена по умолчанию.

В этом разделе никакие действия с вашей стороны не требуются. Если пользователь в GoToMeeting еще не существует, он будет создан при попытке доступа к приложению GoToMeeting.

> [!NOTE]
> Чтобы создать пользователя вручную, обратитесь в [службу поддержки GoToMeeting](https://support.logmeininc.com/gotomeeting).

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку "GoToMeeting" на панели доступа, вы автоматически войдете в приложение GoToMeeting, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

