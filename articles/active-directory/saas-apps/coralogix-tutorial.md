---
title: Руководство по Интеграция Azure Active Directory с приложением Coralogix | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Coralogix.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: ba79bfc1-992e-4924-b76a-8eb0dfb97724
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/2/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8589c366c029ab51c7cd740a1b63cff7c0481a51
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73158466"
---
# <a name="tutorial-azure-active-directory-integration-with-coralogix"></a>Руководство по Интеграция Azure Active Directory с Coralogix

В этом руководстве описано, как интегрировать Coralogix с Azure Active Directory (Azure AD).
Интеграция Azure AD с приложением Coralogix обеспечивает следующие преимущества.

* В Azure AD вы можете контролировать доступ к Coralogix.
* Вы можете включить автоматический вход пользователей в Coralogix (единый вход) с помощью учетных записей Azure AD.
* Вы можете управлять учетными записями централизованно — через портал Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в руководстве по [получению доступа к приложениям и реализации единого входа с помощью Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Coralogix, вам потребуется:

- подписка Azure AD Если у вас нет среды Azure AD, вы можете получить [пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).
- Подписка Coralogix с поддержкой единого входа. 

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Coralogix поддерживает единый вход, инициированный поставщиком услуг.

## <a name="add-coralogix-from-the-gallery"></a>Добавление Coralogix из коллекции

Чтобы настроить интеграцию Coralogix в Azure AD, сначала нужно добавить Coralogix из коллекции в список управляемых приложений SaaS.

Чтобы добавить Coralogix из коллекции, сделайте следующее:

1. На [портале Azure](https://portal.azure.com) в области слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Новое приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Coralogix**. На панели результатов выберите **Coralogix** и нажмите кнопку **Добавить**, чтобы добавить приложение.

     ![Coralogix в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD с Coralogix с использованием тестового пользователя Britta Simon.
Для обеспечения единого входа необходимо установить связь между пользователем Azure Active Directory и соответствующим пользователем в Coralogix.

Чтобы настроить и проверить единый вход Azure AD в Coralogix, сначала выполните действия в следующих стандартных блоках:

1. [Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on) необходима, чтобы пользователи могли использовать эту функцию.
2. [Настройка единого входа в Coralogix](#configure-coralogix-single-sign-on) необходима, чтобы настроить параметры единого входа на стороне приложения.
3. [Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user) требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. [Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user) необходимо, чтобы разрешить Britta Simon использовать единый вход Azure AD.
5. [Создание тестового пользователя приложения Coralogix](#create-a-coralogix-test-user) требуется для того, чтобы в Coralogix существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. [Проверьте единый вход](#test-single-sign-on), чтобы убедиться, что конфигурация работает правильно.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в Coralogix, выполните следующие действия:

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Coralogix** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** нажмите значок **правки**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. В диалоговом окне **Базовая конфигурация SAML** выполните следующие действия:

    ![Сведения о домене и URL-адресах единого входа приложения Coralogix](common/sp-identifier.png)

    a. В текстовое поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<SUBDOMAIN>.coralogix.com`.

    b. В текстовом поле **Идентификатор (сущности)** введите URL-адрес следующего вида:
    
    `https://api.coralogix.com/saml/metadata.xml`

    или

    `https://aws-client-prod.coralogix.com/saml/metadata.xml` 

    > [!NOTE]
    > Значение URL-адреса входа приведено здесь только для примера. Вместо него необходимо указать фактический URL-адрес входа. Чтобы получить это значение, обратитесь в [группу поддержки клиентов Coralogix](mailto:info@coralogix.com). Можно также просмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

5. Приложение Coralogix ожидает утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Значениями этих атрибутов можно управлять в разделе **Атрибуты пользователя** на странице интеграции приложения. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Атрибуты пользователя**.

    ![image](common/edit-attribute.png)

6. В разделе **Утверждения пользователя** диалогового окна **Атрибуты пользователя** измените утверждения с помощью значка **Изменить**. Кроме того, вы можете добавлять утверждения с помощью команды **Добавить новое утверждение**, которая позволяет настроить атрибут токена SAML, как показано на предыдущем рисунке. Затем выполните следующие действия:
    
    a. Щелкните **значок редактирования**, чтобы открыть диалоговое окно **Управление утверждениями пользователя**.

    ![изображение](./media/coralogix-tutorial/tutorial_usermail.png) ![image](./media/coralogix-tutorial/tutorial_usermailedit.png)

    b. В списке **Choose name identifier format** (Выбор формата идентификатора имени) выберите значение **Email address** (Адрес электронной почты).

    c. Из списка **Атрибут источника** выберите **user.mail**.

    d. Щелкните **Сохранить**.

7. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать подходящий **XML-файл метаданных федерации** из предложенных вариантов. Сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

8. Скопируйте соответствующий URL-адрес из раздела **Настройка Coralogix**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    b. Идентификатор Azure AD

    c. URL-адрес выхода.

### <a name="configure-coralogix-single-sign-on"></a>Настройка единого входа в Coralogix

Чтобы настроить единый вход на стороне **Coralogix**, отправьте скачанный **XML-файл метаданных федерации** и URL-адреса, скопированные на портале Azure, в [группу поддержки Coralogix](mailto:info@coralogix.com). Там обеспечат правильную настройку единого входа SAML с обеих сторон.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD 

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана щелкните **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В диалоговом окне **Пользователь** сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    a. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите значение brittasimon@yourcompanydomain.extension. В нашем примере это может быть brittasimon@contoso.com.

    c. Установите флажок **Показать пароль** и сохраните значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к Coralogix.

1. На портале Azure последовательно выберите **Корпоративные приложения**, **Все приложения** и **Coralogix**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Coralogix**.

    ![Ссылка на Coralogix в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**. Теперь выберите **Пользователи и группы** в диалоговом окне **Добавление назначения**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка "Пользователи" выберите **Britta Simon**. Затем в нижней части экрана нажмите кнопку **Выбрать**.

6. Если вы планируете получать значение роли в утверждении SAML, в диалоговом окне **Выбор роли** выберите из списка соответствующую роль для пользователя. Затем в нижней части экрана нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-a-coralogix-test-user"></a>Создание тестового пользователя Coralogix

В этом разделе описано, как создать пользователя Britta Simon в приложении Coralogix. Обратитесь в  [группу поддержки Coralogix](mailto:info@coralogix.com), чтобы добавить пользователей на платформу Coralogix. Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="test-single-sign-on"></a>Проверка единого входа 

В этом разделе объясняется, как проверить конфигурацию единого входа Azure AD с помощью портала MyApps.

Щелкнув плитку Coralogix на портале MyApps, вы автоматически войдете в приложение Coralogix. Дополнительные сведения см. в статье о [портале MyApps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

