---
title: Руководство по по интеграции Azure Active Directory с Tableau Online | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Tableau Online.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.openlocfilehash: 035bc13ee44a8a003ae860eb2bdd67432fa91f14
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88542529"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-online"></a>Руководство по Интеграция единого входа Azure Active Directory с Tableau Online

В этом руководстве вы узнаете, как интегрировать Tableau Online с Azure Active Directory (Azure AD). Интеграция Tableau Online с Azure AD обеспечивает приведенные ниже возможности.

* Контроль доступа к Tableau Online с помощью Azure AD.
* Автоматический вход пользователей в Tableau Online с помощью учетных записей Azure AD.
* Централизованное управление учетными записями через портал Azure.

Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее.

* Подписка Azure AD. Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* Подписка Tableau Online с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Tableau Online поддерживает единый вход, инициируемый **поставщиком услуг**.
* После настройки Tableau Online можно применять элементы управления сеансами, которые защищают от хищения и несанкционированного доступа к конфиденциальным данным вашей организации в режиме реального времени. Управление сеансом является расширением функции условного доступа. [Узнайте, как применять управление сеансами с помощью Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad).

## <a name="adding-tableau-online-from-the-gallery"></a>Добавление Tableau Online из коллекции.

Чтобы настроить интеграцию Tableau Online с Azure AD, необходимо добавить Tableau Online из коллекции в список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в поле поиска введите **Tableau Online**.
1. Выберите **Tableau Online** в области результатов и добавьте это приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Tableau Online с использованием тестового пользователя **Britta Simon**.
Чтобы единый вход функционировал, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Tableau Online.

Чтобы настроить и проверить единый вход Azure AD в Tableau Online, выполните действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
    1. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD с помощью пользователя B.Simon.
    1. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить пользователю B.Simon использовать единый вход Azure AD.
1. **[Настройка единого входа в Tableau Online](#configure-tableau-online-sso)** необходима, чтобы настроить параметры единого входа на стороне приложения.
    1. **[Создание тестового пользователя Tableau Online](#create-tableau-online-test-user)** нужно для того, чтобы в Tableau Online существовал пользователь B.Simon, связанный с одноименным пользователем в Azure AD.
1. **[Проверка единого входа](#test-sso)** позволяет убедиться в правильности конфигурации.

### <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в Tableau Online, сделайте следующее:

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Tableau Online** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Изменение базовой конфигурации SAML](common/edit-urls.png)

4. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    ![Сведения о домене и URL-адресах единого входа для приложения Tableau Online](common/sp-identifier.png)

    а. В текстовом поле **URL-адрес для входа** введите URL-адрес: `https://sso.online.tableau.com/public/sp/login?alias=<entityid>`

    b. В текстовом поле **Идентификатор (сущности)** введите URL-адрес: `https://sso.online.tableau.com/public/sp/metadata?alias=<entityid>`.

    > [!NOTE]
    > Значение `<entityid>` см. на странице **настройки Tableau Online**. Значением идентификатора сущности является **идентификатор Azure AD** на странице **настройки Tableau Online**.

5. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать нужный вам **XML метаданных федерации**, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

6. Требуемые URL-адреса вы можете скопировать на странице **настройки Tableau Online**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а. URL-адрес входа.

    b. Идентификатор Azure AD

    c. URL-адрес выхода.

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
    Например, BrittaSimon\@contoso.com.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Tableau Online.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **Tableau Online**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Tableau Online**.

    ![Ссылка на Tableau Online в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

## <a name="configure-tableau-online-sso"></a>Настройка единого входа в Tableau Online

1. В другом окне браузера войдите в приложение Tableau Online. Выберите **Параметры**, а затем щелкните **Проверка подлинности**.

    ![Настройка единого входа](./media/tableauonline-tutorial/tutorial_tableauonline_09.png)

2. Чтобы включить SAML, в разделе **Типы проверки подлинности** установите флажок **Enable an additional authentication method** (Включить дополнительный метод проверки подлинности) и флажок**SAML**.

    ![Настройка единого входа](./media/tableauonline-tutorial/tutorial_tableauonline_12.png)

3. Прокрутите вниз до раздела **Import metadata file into Tableau Online** (Импорт файла метаданных в Tableau Online).  Нажмите кнопку "Обзор" и импортируйте файл метаданных, скачанный из Azure AD. Нажмите кнопку **Применить**.

   ![Настройка единого входа](./media/tableauonline-tutorial/tutorial_tableauonline_13.png)

4. В разделе **Match assertions** (Сопоставление утверждений) вставьте соответствующее имя утверждения поставщика удостоверений для **электронной почты**, **имени** и **фамилии**. Чтобы получить эти сведения из Azure AD, сделайте следующее: 
  
    а. Перейдите на страницу интеграции с приложением **Tableau Online** портала Azure.

    b. В разделе **Атрибуты и утверждения пользователя** щелкните значок правки.

   ![Настройка единого входа](./media/tableauonline-tutorial/attributesection.png)

    c. Скопируйте значение пространства имен для этих атрибутов: имя, электронная почта и фамилия, выполнив следующие действия:

   ![единого входа Azure AD](./media/tableauonline-tutorial/tutorial_tableauonline_10.png)

    d. Щелкните значение **user.givenname**.

    д) Скопируйте значение из текстового поля **Пространство имен**.

    ![Настройка единого входа](./media/tableauonline-tutorial/attributesection2.png)

    е) Чтобы скопировать значения пространства имен для электронной почты и фамилии, повторите описанные выше шаги.

    ж. Перейдите к приложению Tableau Online и задайте в разделе **Утверждения и атрибуты пользователя** следующее:

    * электронный адрес: **mail** или **userprincipalname**;

    * имя: **givenname**

    * фамилия: **surname**

    ![Настройка единого входа](./media/tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="create-tableau-online-test-user"></a>Создание тестового пользователя Tableau Online

В этом разделе описано, как создать пользователя Britta Simon в приложении Tableau Online.

1. В приложении **Tableau Online** щелкните **Settings** (Параметры), а затем выберите раздел **Authentication** (Проверка подлинности). Прокрутите вниз до раздела **Управление пользователями**. Щелкните **Add Users** (Добавить пользователей), а затем — **Enter Email Addresses** (Ввести адреса электронной почты).
  
    ![Создание тестового пользователя Azure AD](./media/tableauonline-tutorial/tutorial_tableauonline_15.png)

2. Выберите **Add users for (SAML) authentication** (Добавить пользователей для проверки подлинности (SAML). В текстовое поле **Enter email addresses** (Введите адреса электронной почты) добавьте britta.simon\@contoso.com
  
    ![Создание тестового пользователя Azure AD](./media/tableauonline-tutorial/tutorial_tableauonline_11.png)

3. Щелкните **Добавить пользователей**.

### <a name="test-sso"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Tableau Online на Панели доступа, вы автоматически войдете в приложение Tableau Online, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Что такое управление сеансами в Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)