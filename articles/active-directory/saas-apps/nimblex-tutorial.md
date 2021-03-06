---
title: Руководство по Интеграция Azure Active Directory с Nimblex | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Nimblex.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d3e165a5-f062-4b50-ac0b-b400838e99cd
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: jeedes
ms.openlocfilehash: cd7199e94a58b3f0c121a0fd9401bff94406ed84
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67096099"
---
# <a name="tutorial-azure-active-directory-integration-with-nimblex"></a>Руководство по Интеграция Azure Active Directory с Nimblex

В этом руководстве описано, как интегрировать Nimblex с Azure Active Directory (Azure AD).
Интеграция Azure AD с приложением Nimblex обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к Nimblex.
* Вы можете включить автоматический вход пользователей в Nimblex (единый вход) с помощью учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Nimblex, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* подписка Nimblex с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Nimblex поддерживает единый вход, инициируемый **поставщиком услуг**.

* Nimblex поддерживает **JIT**-подготовку пользователей.

## <a name="adding-nimblex-from-the-gallery"></a>Добавление Nimblex из коллекции

Чтобы настроить интеграцию Nimblex с Azure AD, необходимо добавить Nimblex из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Nimblex из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Nimblex**, выберите **Nimblex** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![Nimblex в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Nimblex с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Nimblex.

Чтобы настроить и проверить единый вход Azure AD в Nimblex, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в Nimblex](#configure-nimblex-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя Nimblex](#create-nimblex-test-user)** требуется для того, чтобы в Nimblex существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в Nimblex, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Nimblex** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    ![Сведения о домене и URL-адресах единого входа приложения Nimblex](common/sp-identifier-reply.png)

    a. В текстовое поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<YOUR APPLICATION PATH>/Login.aspx`.

    b. В поле **Идентификатор** введите URL-адрес в следующем формате: `https://<YOUR APPLICATION PATH>/`.

    c. В текстовом поле **URL-адрес ответа** введите URL-адрес в формате `https://<path-to-application>/SamlReply.aspx`.

    > [!NOTE]
    > Эти значения приведены для примера. Укажите вместо них фактические значения URL-адреса для входа, идентификатора и URL-адреса ответа. Чтобы получить эти значения, обратитесь к [группе поддержки Nimblex](mailto:support@ebms.com.au). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

4. На странице **настройки единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Загрузить**, чтобы загрузить требуемый **сертификат (Base64)** из предложенных вариантов, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

6. Требуемые URL-адреса вы можете скопировать из раздела **Настройка Nimblex**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    b. Идентификатор Azure AD

    c. URL-адрес выхода.

### <a name="configure-nimblex-single-sign-on"></a>Настройка единого входа в Nimblex

1. В другом окне веб-браузера войдите в Nimblex в качестве администратора безопасности.

2. В правом верхнем углу страницы щелкните значок **Settings** (Параметры).

    ![Параметры Nimblex](./media/nimblex-tutorial/tutorial_nimblex_settings.png)

3. На странице **Панель управления** в разделе **Безопасность** нажмите **Единый вход**.

    ![Параметры Nimblex](./media/nimblex-tutorial/tutorial_nimblex_single.png)

4. На странице **Управление единым входом** выберите имя экземпляра и щелкните **Изменить**.

    ![Nimblex saml](./media/nimblex-tutorial/tutorial_nimblex_saml.png)

5. На странице **Изменение поставщика SAML** выполните следующие действия:

    ![Nimblex saml](./media/nimblex-tutorial/tutorial_nimblex_sso.png)

    a. В текстовом поле **Описание** введите имя экземпляра.

    b. В Блокноте откройте скачанный с портала Azure сертификат в кодировке base-64, скопируйте его содержимое, а затем вставьте его в поле **Сертификат**.

    c. В текстовое поле **Identity Provider Sso Target Url** (Целевой URL-адрес единого входа поставщика удостоверений) вставьте **URL-адрес входа**, скопированный на портале Azure.

    d. Выберите команду **Сохранить**.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD 

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    a. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon@yourcompanydomain.extension** .  
    Например, BrittaSimon@contoso.com

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Nimblex.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **Nimblex**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Nimblex**.

    ![Ссылка на Nimblex в списке "Приложения"](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-nimblex-test-user"></a>Создание тестового пользователя Nimblex

В этом разделе вы создадите в Nimblex пользователя с именем Britta Simon. Приложение Nimblex поддерживает JIT-подготовку пользователей, которая включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Если пользователь еще не существует в Nimblex, он создается после выполнения аутентификации.

>[!Note]
>Чтобы создать учетную запись пользователя вручную, обратитесь в [службу поддержки Nimblex](mailto:support@ebms.com.au).

### <a name="test-single-sign-on"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "Nimblex" на панели доступа, вы автоматически войдете в приложение Nimblex, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что такое условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

