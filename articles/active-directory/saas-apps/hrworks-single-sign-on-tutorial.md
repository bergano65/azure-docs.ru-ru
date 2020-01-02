---
title: Руководство по Интеграция Azure Active Directory с HRworks для включения единого входа | Документация Майкрософт
description: Из этой статьи вы узнаете, как настроить единый вход Azure Active Directory в HRworks.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c4c5d434-3f8a-411e-83a5-c3d5276ddc0a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: a36266c14531f935779266829402392dc4a03411
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74706015"
---
# <a name="tutorial-azure-active-directory-integration-with-hrworks-single-sign-on"></a>Руководство по Интеграция Azure Active Directory с HRworks для включения единого входа

В этом руководстве описано, как интегрировать Azure Active Directory (Azure AD) с HRworks для включения единого входа.
Интеграция Azure AD с HRworks для включения единого входа обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к единому входу в HRworks.
* Вы можете включить автоматический вход для пользователей в HRworks (единый вход) с помощью учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с HRworks для включения единого входа, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* подписка HRworks с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Приложение HRworks поддерживает единый вход, инициированный **поставщиком услуг**.

## <a name="adding-hrworks-single-sign-on-from-the-gallery"></a>Добавление HRworks из коллекции

Чтобы настроить интеграцию Azure AD с HRworks для включения единого входа, вам необходимо добавить это приложение из коллекции в список управляемых приложений SaaS.

**Чтобы добавить HRworks из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Единый вход в HRworks**, выберите **Единый вход в HRworks** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![HRworks в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в HRworks с использованием тестового пользователя **Britta Simon**.
Чтобы единый вход функционировал, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в HRworks.

Чтобы настроить и проверить единый вход Azure AD в HRworks для включения единого входа, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка HRworks](#configure-hrworks-single-sign-on-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя в HRworks](#create-hrworks-single-sign-on-test-user)** требуется для того, чтобы в HRworks существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в HRworks, выполните следующие действия:

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с **HRworks** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    ![Сведения о домене и URL-адресах HRworks](common/sp-signonurl.png)

    В текстовом поле **URL-адрес входа** введите URL-адрес в формате `https://login.hrworks.de/?companyId=<companyId>&directssologin=true`.

    > [!NOTE]
    > Это значение приведено для примера. Вместо него необходимо указать фактический URL-адрес входа. Чтобы получить это значение, обратитесь в [службу поддержки клиентов HRworks](https://www.hrworks.de/dienstleistungen/support/). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

5. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать нужный вам **XML метаданных федерации**, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

6. Требуемый URL-адрес можно скопировать из раздела о **настройке HRworks**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    b. Идентификатор Azure AD

    c. URL-адрес выхода.

### <a name="configure-hrworks-single-sign-on-single-sign-on"></a>Настройка единого входа в HRworks

1. В другом окне веб-браузера войдите в HRworks с правами администратора.

2. Выберите **Administrator (Администратор)**  > **Basics (Основы)**  > **Security (Безопасность)**  > **Single Sign-on (Единый вход)** в левой части строки меню и выполните следующие шаги:

    ![Настройка единого входа](./media/hrworks-single-sign-on-tutorial/configure01.png)

    a. Установите флажок **Use Single Sign-on** (Использовать единый вход).

    b. Выберите значение **XML Metadata** (Метаданные XML) для параметра **Meta data input method** (Метод ввода метаданных).

    c. Выберите значение **Individual NameID identifier** (Индивидуальный идентификатор NameID) для параметра **Value for NameID** (Значение NameID).

    d. В Блокноте откройте скачанный с портала Azure XML-файл метаданных, скопируйте его содержимое, а затем вставьте его в текстовое поле **Metadata** (Метаданные).

    д. Выберите команду **Сохранить**.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD 

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    a. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **User name** (Имя пользователя) введите имя пользователя, например BrittaSimon@contoso.com.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив доступ к HRworks.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения** и **Единый вход в HRworks**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Единый вход в HRworks**.

    ![Ссылка на HRworks в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-hrworks-single-sign-on-test-user"></a>Создание тестового пользователя для HRworks для включения единого входа

Чтобы пользователи Azure AD могли выполнять вход в HRworks (единый вход), они должны быть подготовлены в нем. В HRworks подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Войдите в HRworks с правами администратора.

2. Выберите **Administrator (Администратор)**  > **Persons (Лица)**  > **Persons (Лица)**  > **New person (Создать лицо)** в левой части строки меню.

     ![Настройка единого входа](./media/hrworks-single-sign-on-tutorial/configure02.png)

3. Во всплывающем окне нажмите **Next** (Далее).

    ![Настройка единого входа](./media/hrworks-single-sign-on-tutorial/configure03.png)

4. Во всплывающем элементе **Create new person with country for legal terms** (Создание нового лица с указанием страны для соответствия юридическим условиям) введите нужные данные, например **First name** (Имя), **Last name** (Фамилия), и нажмите **Create** (Создать).
    
    ![Настройка единого входа](./media/hrworks-single-sign-on-tutorial/configure04.png)

### <a name="test-single-sign-on"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку HRworks на панели доступа, вы автоматически войдете в HRworks, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

