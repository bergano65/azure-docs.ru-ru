---
title: Руководство. Интеграция Azure Active Directory с MobileIron | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в приложении MobileIron.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 3e4bbd5b-290e-4951-971b-ec0c1c11aaa2
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.openlocfilehash: c70d0d077bb3b3002226bc05eae1fedebe29b03f
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54827488"
---
# <a name="tutorial-azure-active-directory-integration-with-mobileiron"></a>Руководство. Интеграция Azure Active Directory с MobileIron

Из этого руководства вы узнаете, как интегрировать MobileIron с Azure Active Directory (Azure AD).
Интеграция Azure AD с приложением MobileIron обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к MobileIron.
* Вы можете включить автоматический вход пользователей в MobileIron (единый вход) с помощью учетных записей Azure AD.
* Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с MobileIron, вам потребуется следующее:

* подписка Azure AD; Если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).
* Подписка MobileIron с поддержкой единого входа

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства проводится настройка и проверка единого входа Azure AD в тестовой среде.

* MobileIron поддерживает единый вход инициированного **пакета обновления и выдающей точки распространения**.

## <a name="adding-mobileiron-from-the-gallery"></a>Добавление MobileIron из коллекции

Чтобы настроить интеграцию MobileIron с Azure AD, необходимо добавить MobileIron из коллекции в список управляемых приложений SaaS.

**Чтобы добавить MobileIron из коллекции, выполните инструкции ниже.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"](common/add-new-app.png)

4. В поле поиска введите **MobileIron**, выберите **MobileIron** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![MobileIron в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в MobileIron с помощью тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в MobileIron.

Чтобы настроить и проверить единый вход Azure AD в MobileIron, вам потребуется выполнить действия в указанных ниже стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в MobileIron](#configure-mobileiron-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Создав тестового пользователя MobileIron](#create-mobileiron-test-user)** Britta Simon, мы сможем связать его с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD с MobileIron, выполните инструкции, приведенные ниже.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **MobileIron** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. Если вы хотите настроить приложение в инициируемом режиме  **выдающейся точки распространения** , в разделе **Базовая конфигурация SAML** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения MobileIron](common/idp-intiated.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в формате `https://www.mobileiron.com/<key>`.

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<host>.mobileiron.com/saml/SSO/alias/<key>`.

    c. Чтобы настроить приложение для работы в режиме, инициируемом **поставщиком услуг**, щелкните **Задать дополнительные URL-адреса** и выполните следующее действие:

    ![Сведения о домене и URL-адресах единого входа для приложения MobileIron](common/metadata-upload-additional-signon.png)

    В текстовом поле **URL-адрес входа** введите URL-адрес в следующем формате: `https://<host>.mobileiron.com/user/login.html`.

    > [!NOTE]
    > Эти значения приведены в качестве примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Значения ключа и узла вы получите на портале администрирования MobileIron, как описано далее в этом руководстве.

5. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать нужный вам **XML метаданных федерации**, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

### <a name="configure-mobileiron-single-sign-on"></a>Настройка единого входа в MobileIron

1. В другом окне веб-браузера войдите на корпоративный сайт MobileIron в качестве администратора.

2. Перейдите в **Администратор** > **Идентификатор** и выберите параметр **AAD** в поле **Info on Cloud IDP Setup** (Информация о настройке IDP в облаке).

    ![Кнопка настройки единого входа для администратора](./media/mobileiron-tutorial/tutorial_mobileiron_admin.png)

3. Скопируйте значения **Ключ** и **Узел** и дополните ими URL-адреса в разделе **Базовая настройка SAML** на портале Azure.

    ![Кнопка настройки единого входа для администратора](./media/mobileiron-tutorial/key.png)

4. В разделе **Export metadata file from AAD and import to MobileIron Cloud Field** (Экспорт файла метаданных из AAD и импорт данных в облако MobileIron) и щелкните **Choose File** (Выбрать файл), чтобы отправить метаданные, скачанные с портала Azure. Когда процесс завершится, нажмите кнопку **Done** (Готово).

    ![Настройка единого входа для администратора — кнопка метаданных](./media/mobileiron-tutorial/tutorial_mobileiron_adminmetadata.png)

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

В этом разделе пользователю Britta Simon предоставляется разрешение на использование единого входа Azure для доступа к MobileIron.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **MobileIron**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений введите и выберите **MobileIron**.

    ![Ссылка на MobileIron в списке "Приложения"](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-mobileiron-test-user"></a>Создание тестового пользователя MobileIron

Чтобы пользователи Azure AD могли выполнять вход в MobileIron, они должны быть подготовлены для MobileIron.  
Для MobileIron подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Выполните вход на корпоративный веб-сайт MobileIron в качестве администратора.

1. Выберите пункт **Users** (Пользователи) и щелкните **Add** (Добавить)  > **Single User** (Один пользователь).

    ![Настройка единого входа — кнопка "Пользователь"](./media/mobileiron-tutorial/tutorial_mobileiron_user.png)

1. На странице диалогового окна **Single User** (Один пользователь) выполните инструкции ниже.

    ![Настройка единого входа— кнопка "Добавить пользователя"](./media/mobileiron-tutorial/tutorial_mobileiron_useradd.png)

    a. В текстовое поле **Email address** (Адрес электронной почты) введите адрес электронной почты пользователя, например brittasimon@contoso.com.

    b. В текстовое поле **First Name** (Имя) введите имя пользователя, например Britta.

    c. В текстовое поле **Last Name** (Фамилия) введите фамилию пользователя, например Simon.

    d. Нажмите кнопку **Done**(Готово).

### <a name="test-single-sign-on"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку "MobileIron" на панели доступа, вы автоматически войдете в приложение MobileIron, для которого настроили единый вход. Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

