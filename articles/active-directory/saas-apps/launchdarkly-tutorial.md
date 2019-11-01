---
title: Руководство по Интеграция Azure Active Directory с LaunchDarkly | Документация Майкрософт
description: Сведения о том, как настроить единый вход между Azure Active Directory и LaunchDarkly.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 3f0671bc-f93f-496e-b465-b9ce8c6633fa
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e32f8c3ea300960893163264e99bd6c51138c7c5
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73159658"
---
# <a name="tutorial-azure-active-directory-integration-with-launchdarkly"></a>Руководство по Интеграция Azure Active Directory с LaunchDarkly

В этом руководстве описано, как интегрировать LaunchDarkly с Azure Active Directory (Azure AD).
Интеграция Azure AD с приложением LaunchDarkly обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к LaunchDarkly.
* Вы можете включить автоматический вход для пользователей в LaunchDarkly (единый вход) с помощью учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с приложением LaunchDarkly, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* подписка с поддержкой единого входа LaunchDarkly.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* LaunchDarkly поддерживает единый вход, инициированный **поставщиком услуг и поставщиком удостоверений**.
* LaunchDarkly поддерживает **JIT**-подготовку пользователей.

## <a name="adding-launchdarkly-from-the-gallery"></a>Добавление LaunchDarkly из коллекции

Чтобы настроить интеграцию LaunchDarkly с Azure AD, нужно добавить LaunchDarkly из коллекции в список управляемых приложений SaaS.

**Чтобы добавить LaunchDarkly из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **LaunchDarkly**, выберите **LaunchDarkly** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![LaunchDarkly в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в [название приложения] с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в [название приложения].

Чтобы настроить и проверить единый вход Azure AD в [название приложения], вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в LaunchDarkly](#configure-launchdarkly-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя LaunchDarkly](#create-launchdarkly-test-user)** требуется, чтобы в LaunchDarkly существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в [название приложения], выполните следующие действия.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **LaunchDarkly** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. Если вы хотите настроить приложение в режиме, инициируемом **поставщиком удостоверений**, в разделе **Базовая конфигурация SAML** выполните следующие действия.

    ![Сведения о домене и URL-адресах для единого входа в приложение LaunchDarkly](common/idp-intiated.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес: `app.launchdarkly.com`

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в формате `https://app.launchdarkly.com/trust/saml2/acs/<customers-unique-id>`.

    > [!NOTE]
    > Значение URL-адреса ответа приведено для примера. Вы замените это значение на фактический URL-адрес ответа, который описывается далее в этом учебнике. Если вы планируете использовать приложение в режиме **поставщика удостоверений**, необходимо оставить поле **URL-адрес для входа** пустым. В противном случае вы не сможете инициировать вход из **поставщика удостоверений**. Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

5. Чтобы настроить приложение для работы в режиме, инициируемом **поставщиком услуг**, щелкните **Задать дополнительные URL-адреса** и выполните следующие действия.

    В текстовом поле **URL-адрес входа** введите URL-адрес в формате `https://app.launchdarkly.com`.

    ![Сведения о домене и URL-адресах для единого входа в приложение LaunchDarkly](common/metadata-upload-additional-signon.png)

6. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Загрузить**, чтобы загрузить требуемый **сертификат (Base64)** из предложенных вариантов, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

7. Скопируйте требуемый URL-адрес из раздела **Настройка LaunchDarkly**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    б) Идентификатор Azure AD.

    в) URL-адрес выхода.

### <a name="configure-launchdarkly-single-sign-on"></a>Настройка единого входа в LaunchDarkly

1. В другом окне веб-браузера войдите на сайт LaunchDarkly вашей организации в качестве администратора.

2. Выберите **Параметры учетной записи** в области навигации слева.

    ![Конфигурация LaunchDarkly](./media/launchdarkly-tutorial/configure1.png)

3. Откройте вкладку **Безопасность**.

    ![Конфигурация LaunchDarkly](./media/launchdarkly-tutorial/configure2.png)

4. Щелкните **ENABLE SSO** (Включить единый вход) и затем **EDIT SAML CONFIGURATION** (Изменить конфигурацию SAML).

    ![Конфигурация LaunchDarkly](./media/launchdarkly-tutorial/configure3.png)

5. В разделе **Edit your SAML configuration** (Изменение конфигурации SAML) сделайте следующее:

    ![Конфигурация LaunchDarkly](./media/launchdarkly-tutorial/configure4.png)

    a. Скопируйте **SAML consumer service URL** (URL-адрес службы объекта-получателя SAML) для своего экземпляра и вставьте его в текстовое поле "URL-адрес ответа" в разделе **Домен и URL-адреса приложения LaunchDarkly** на портале Azure.

    b. В текстовое поле **URL-адрес для входа** вставьте значение **URL-адреса входа**, скопированное на портале Azure.

    c. Откройте в Блокноте скачанный с портала Azure сертификат, скопируйте его содержимое и вставьте его в поле **Сертификат X.509**. Кроме того, можно отправить сертификат напрямую, выбрав **upload one** (Отправить).

    d. Нажмите кнопку **Сохранить**

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

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к LaunchDarkly.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем **LaunchDarkly**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **LaunchDarkly**.

    ![Ссылка на LaunchDarkly в списке "Приложения"](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-launchdarkly-test-user"></a>Создание тестового пользователя приложения LaunchDarkly

Цель этого раздела — создать пользователя с именем Britta Simon в LaunchDarkly. Приложение LaunchDarkly поддерживает JIT-подготовку. Эта функция включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Пользователь создается при попытке получить доступ к приложению LaunchDarkly (если он еще не создан).

> [!Note]
> Чтобы создать учетную запись пользователя вручную, обратитесь в  [группу поддержки клиента LaunchDarkly](mailto:support@launchdarkly.com).

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку LaunchDarkly на панели доступа, вы автоматически войдете в приложение LaunchDarkly, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
