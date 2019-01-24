---
title: Руководство. Интеграция Azure Active Directory с OpenAthens | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и OpenAthens.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: dd4adfc7-e238-41d5-8b25-1811f08078b6
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/4/2019
ms.author: jeedes
ms.openlocfilehash: 9fe00ad1b567fff28301c981413d281869d2aec1
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54808406"
---
# <a name="tutorial-azure-active-directory-integration-with-openathens"></a>Руководство. Интеграция Azure Active Directory с OpenAthens

В этом руководстве описано, как интегрировать OpenAthens с Azure Active Directory (Azure AD).
Интеграция Azure AD с приложением OpenAthens обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к OpenAthens.
* Вы можете включить автоматический вход пользователей в OpenAthens (единый вход) с помощью учетных записей Azure AD.
* Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с OpenAthens, вам потребуется:

* подписка Azure AD; Если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).
* Подписка OpenAthens с поддержкой единого входа

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства проводится настройка и проверка единого входа Azure AD в тестовой среде.

* OpenAthens поддерживает единый вход инициированной **выдающей точки распространения**.

* OpenAthens поддерживает **JIT**-подготовку пользователей.

## <a name="adding-openathens-from-the-gallery"></a>Добавление OpenAthens из коллекции

Чтобы настроить интеграцию OpenAthens с Azure AD, необходимо добавить OpenAthens из коллекции в список управляемых приложений SaaS.

**Чтобы добавить OpenAthens из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"](common/add-new-app.png)

4. В поле поиска введите **OpenAthens**, выберите **OpenAthens** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![OpenAthens в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в OpenAthens с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в OpenAthens.

Чтобы настроить и проверить единый вход Azure AD в OpenAthens, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в OpenAthen](#configure-openathens-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя OpenAthens](#create-openathens-test-user)** требуется для того, чтобы в OpenAthens существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в OpenAthens, выполните следующие действия:

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **OpenAthens** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

5. В разделе **Базовая конфигурация SAML** отправьте **файл метаданных Поставщика услуг**, следуя инструкциям, упомянутым далее в этом руководстве.

    a. Щелкните **Отправить файл метаданных**.

    ![Отправка метаданных OpenAthens](common/upload-metadata.png)

    b. Щелкните **значок папки**, выберите файл метаданных и нажмите кнопку **Отправить**.

    ![Обзор отправки метаданных OpenAthens](common/browse-upload-metadata.png)

    c. После успешной передачи файла метаданных значение **Идентификатор** автоматически заполняется в разделе текстового поля **Базовая конфигурация SAML**.

    ![Сведения о домене и URL-адресах единого входа для приложения OpenAthens](common/idp-identifier.png)

6. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать нужный вам **XML метаданных федерации**, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

### <a name="configure-openathens-single-sign-on"></a>Настройка единого входа OpenAthens

1. В другом окне веб-браузера войдите на свой корпоративный сайт OpenAthens в качестве администратора.

2. Выберите **Connections** (Подключения) в списке на вкладке **Management** (Управление). 

    ![Настройка единого входа](./media/openathens-tutorial/tutorial_openathens_application1.png)

3. Выберите **SAML 1.1/2.0**, а затем нажмите кнопку **Configure** (Настроить).

    ![Настройка единого входа](./media/openathens-tutorial/tutorial_openathens_application2.png)
    
4. Чтобы добавить конфигурацию, нажмите кнопку **​​Browse** (Обзор), чтобы передать XML-файл метаданных, скачанный на портале Azure, а затем нажмите кнопку **Add** (Добавить).

    ![Настройка единого входа](./media/openathens-tutorial/tutorial_openathens_application3.png)

5. Выполните следующие действия на вкладке **Details** (Сведения).

    ![Настройка единого входа](./media/openathens-tutorial/tutorial_openathens_application4.png)

    a. Из списка **Display name mapping** (Сопоставление отображаемого имени) выберите **Use Attribute** (Использовать атрибут).

    b. В текстовом поле **Display name attribute** (Атрибут отображаемого имени) введите значение `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    c. Из списка **Unique user mapping** (Сопоставление уникального пользователя) выберите **Use Attribute** (Использовать атрибут).

    d. В текстовом поле **Unique user attribute** (Атрибут уникального пользователя имени) введите значение `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    д. В разделе **Status** (Состояние) установите все три флажка.

    Е. Из списка **Create local accounts** (Создание локальных учетных записей) выберите **automatically** (Автоматически).

    ж. Щелкните **Save changes** (Сохранить изменения).

    h. Из вкладки **</> Проверяющая сторона** скопируйте **URL-адрес метаданных** и откройте его в браузере, чтобы скачать файл **XML метаданных пакета обновления**. Отправьте этот файл метаданных пакета обновления в раздел **Базовая конфигурация SAML** Azure AD.

    ![Настройка единого входа](./media/openathens-tutorial/tutorial_openathens_application5.png)

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

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к OpenAthens.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **OpenAthens**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений введите и выберите **OpenAthens**.

    ![Ссылка на OpenAthens в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-openathens-test-user"></a>Создание тестового пользователя OpenAthens

В этом разделе вы создадите в OpenAthens пользователя с именем Britta Simon. Приложение OpenAthens поддерживает **JIT-подготовку пользователей**, которая включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Если пользователь еще не существует в OpenAthens, он создается после проверки подлинности.

### <a name="test-single-sign-on"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку "OpenAthens" на панели доступа, вы автоматически войдете в приложение OpenAthens, для которого настроили единый вход. Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

