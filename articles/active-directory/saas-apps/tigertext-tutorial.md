---
title: Руководство по Интеграция Azure Active Directory с TigerText Secure Messenger | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и TigerText Secure Messenger.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 03f1e128-5bcb-4e49-b6a3-fe22eedc6d5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: 840b1fe55556cfd853e0928164891d6b21b17cc2
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65956863"
---
# <a name="tutorial-azure-active-directory-integration-with-tigertext-secure-messenger"></a>Руководство по Интеграция Azure Active Directory с TigerText Secure Messenger

В этом руководстве описано, как интегрировать приложение TigerText Secure Messenger с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением TigerText Secure Messenger обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к TigerText Secure Messenger.
* Вы можете включить автоматический вход пользователей в TigerText Secure Messenger (единый вход) с помощью учетных записей Azure AD.
* Вы можете управлять учетными записями централизованно — через портал Azure.

Дополнительные сведения об интеграции приложения SaaS с Azure AD см. в статье [о доступе к приложениям и едином входе с помощью Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с TigerText Secure Messenger, вам потребуется:

* подписка Azure AD Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.
* подписка TigerText Secure Messenger с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде и интеграцию TigerText Secure Messenger с Azure AD.

TigerText Secure Messenger поддерживает единый вход, инициируемый поставщиком услуг.

## <a name="add-tigertext-secure-messenger-from-the-azure-marketplace"></a>Добавление TigerText Secure Messenger из Azure Marketplace

Чтобы настроить интеграцию TigerText Secure Messenger с Azure AD, необходимо добавить TigerText Secure Messenger из Azure Marketplace в список управляемых приложений SaaS.

1. Войдите на [портале Azure](https://portal.azure.com?azure-portal=true).
1. В области слева выберите **Azure Active Directory**.

    ![Пункт Azure Active Directory](common/select-azuread.png)

1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Область "Корпоративные приложения"](common/enterprise-applications.png)

1. Чтобы добавить новое приложение, выберите **+ Новое приложение** в верхней части панели.

    ![Элемент "Новое приложение"](common/add-new-app.png)

1. В поле поиска введите **TigerText Secure Messenger**. В результатах поиска выберите **TigerText Secure Messenger** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![TigerText Secure Messenger в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в TigerText Secure Messenger с использованием тестового пользователя **Britta Simon**. Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в TigerText Secure Messenger.

Чтобы настроить и проверить единый вход Azure AD в TigerText Secure Messenger, выполните действия в следующих стандартных блоках.

1. **[Настройте единый вход в Azure AD](#configure-azure-ad-single-sign-on)** , чтобы пользователи могли использовать эту функцию.
1. **[Настройка единого входа в TigerText Secure Messenger](#configure-tigertext-secure-messenger-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
1. **[Создайте тестового пользователя Azure AD](#create-an-azure-ad-test-user)** для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
1. **[Назначьте тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** , чтобы позволить Britta Simon использовать единый вход в Azure AD.
1. **[Создание тестового пользователя TigerText Secure Messenger](#create-a-tigertext-secure-messenger-test-user)** требуется для того, чтобы в TigerText Secure Messenger существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
1. **[Проверьте единый вход](#test-single-sign-on)** , чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в TigerText Secure Messenger, сделайте следующее.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **TigerText Secure Messenger** выберите **Единый вход**.

    ![Параметр для настройки единого входа](common/select-sso.png)

1. В области **Выбрать метод единого входа** выберите режим **SAML/WS-FED**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

1. На панели **Настройка единого входа с помощью SAML** щелкните **Изменить** (значок карандаша), чтобы открыть панель **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

1. В области **Базовая конфигурация SAML** выполните приведенные ниже действия.

    ![Сведения о домене и URL-адресах единого входа для TigerText Secure Messenger](common/sp-identifier.png)

    1. В поле **URL-адрес для входа** введите URL-адрес:

       `https://home.tigertext.com`

    1. В текстовом поле **Идентификатор (сущности)** введите URL-адрес в следующем формате:

       `https://saml-lb.tigertext.me/v1/organization/<instance ID>`

    > [!NOTE]
    > Указано ненастоящее значение **Идентификатор (сущности)** . Замените его реальным идентификатором. Чтобы получить это значение, обратитесь к [группе поддержки TigerText Secure Messenger](mailto:prosupport@tigertext.com). Можно также посмотреть шаблоны на панели **Базовая конфигурация SAML** на портале Azure.

1. На панели **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать подходящий **XML-файл метаданных федерации** из предложенных вариантов и сохранить его на своем компьютере.

    ![Элемент для скачивания XML-файла метаданных федерации](common/metadataxml.png)

1. Требуемые URL-адреса можно скопировать в разделе **Настройка TigerText Secure Messenger**.

   * **Login URL** (URL-адрес для входа)
   * **идентификатор Azure AD**;
   * **URL-адрес выхода**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

### <a name="configure-tigertext-secure-messenger-single-sign-on"></a>Настройка единого входа в TigerText Secure Messenger

Чтобы настроить единый вход на стороне TigerText Secure Messenger, отправьте скачанный XML-файл метаданных федерации и URL-адреса, скопированные на портале Azure, [группе поддержки TigerText Secure Messenger](mailto:prosupport@tigertext.com). Группа поддержки TigerText Secure Messenger обеспечит правильную настройку подключения для единого входа SAML с обеих сторон.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

В этом разделе описано, как создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure на панели слева выберите **Azure Active Directory**   > **Пользователи** > **Все пользователи**.

    ![Пункты меню "Пользователи" и "Все пользователи"](common/users.png)

1. В верхней части экрана щелкните **+ Новый пользователь**.

    ![Элемент "Новый пользователь"](common/new-user.png)

1. На панели **Пользователь** выполните следующие действия:

    ![Панель "Пользователь"](common/user-properties.png)

    1. В поле **Имя** введите **BrittaSimon**.
  
    1. В поле **Имя пользователя** введите **BrittaSimon\@\<домен_вашей_компании>.\<доменная_зона>** . Например **BrittaSimon\@contoso.com**.

    1. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    1. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к TigerText Secure Messenger.

1. На портале Azure выберите **Корпоративные приложения** > **Все приложения** > **TigerText Secure Messenger**.

    ![Область "Корпоративные приложения"](common/enterprise-applications.png)

1. В списке приложений выберите **TigerText Secure Messenger**.

    ![Ссылка на TigerText Secure Messenger в списке "Приложения"](common/all-applications.png)

1. В области слева последовательно выберите **Управление** и **Пользователи и группы**.

    ![Пункт меню "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **+ Добавить пользователя**, а в области **Добавление назначения** щелкните **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

1. На панели **Пользователи и группы** выберите **Britta Simon** в списке **Пользователи**, а в нижней панели щелкните **Выбрать**.

1. Если вы планируете получать значение роли в утверждении SAML, на панели **Выбор роли** выберите в списке соответствующую роль для пользователя. В нижней части панели нажмите кнопку **Выбрать**.

1. В области **Добавление назначения** выберите **Назначить**.

### <a name="create-a-tigertext-secure-messenger-test-user"></a>Создание тестового пользователя TigerText Secure Messenger

В этом разделе описано, как создать пользователя Britta Simon в приложении TigerText Secure Messenger. Обратитесь к [группе поддержки TigerText Secure Messenger](mailto:prosupport@tigertext.com), чтобы добавить пользователя Britta Simon в TigerText Secure Messenger. Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе объясняется, как проверить конфигурацию единого входа Azure AD с помощью портала "Мои приложения".

Щелкнув **TigerText Secure Messenger** на портале "Мои приложения", вы автоматически выполните вход с подпиской TigerText Secure Messenger, для которой настроили единый вход. Дополнительные сведения о портале "Мои приложения" см. в статье [Access and use apps on the My Apps portal](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Доступ к приложениям и их использование на портале "Мои приложения").

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список руководств по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
