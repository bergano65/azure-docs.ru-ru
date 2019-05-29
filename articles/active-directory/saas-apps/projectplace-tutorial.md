---
title: Руководство по Интеграция Azure Active Directory с Projectplace | Документация Майкрософт
description: Из этого руководства вы узнаете, как настроить единый вход Azure Active Directory в Projectplace.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 298059ca-b652-4577-916a-c31393d53d7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 17fbc6bc4f022a15c34c5ca7b9465be392cdc639
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2019
ms.locfileid: "65560630"
---
# <a name="tutorial-azure-active-directory-integration-with-projectplace"></a>Руководство по Интеграция Azure Active Directory с Projectplace

В данном руководстве описано, как интегрировать Projectplace с Azure Active Directory (Azure AD).

Такая интеграция обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к Projectplace.
* Вы можете включить автоматический вход (единый вход) пользователей в Projectplace с помощью учетных записей Azure AD.
* Вы можете управлять учетными записями централизованно — через портал Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Projectplace, вам потребуется:

* подписка Azure AD Если у вас нет среды Azure AD, вы можете получить [пробную подписку на один месяц](https://azure.microsoft.com/pricing/free-trial/).
* подписка Projectplace с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure Active Directory в тестовой среде.

* Projectplace поддерживает единый вход, инициируемый поставщиком услуг.

## <a name="add-projectplace-from-the-gallery"></a>Добавление Projectplace из коллекции

Чтобы настроить интеграцию Projectplace с Azure AD, необходимо добавить Projectplace из коллекции в список управляемых приложений SaaS.

1. На [портале Azure](https://portal.azure.com) в области слева щелкните **Azure Active Directory**.

    ![Выберите Azure Active Directory.](common/select-azuread.png)

2. Выберите **Корпоративные приложения** > **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, выберите **Новое приложение** в верхней части окна.

    ![Выбор элемента "Новое приложение"](common/add-new-app.png)

4. В поле поиска введите **Projectplace**. В результатах поиска выберите **Projectplace**, а затем щелкните **Добавить**.

     ![Результаты поиска](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Projectplace с использованием тестового пользователя Britta Simon.
Для единого входа необходимо установить связь между пользователем в Azure AD и соответствующим пользователем в Projectplace.

Чтобы настроить и проверить единый вход Azure AD в Projectplace, вам потребуется выполнить следующие действия.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в Projectplace](#configure-projectplace-single-sign-on)** на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить ему использовать единый вход Azure AD.
5. **[Создание тестового пользователя Projectplace](#create-a-projectplace-test-user)** , связанного с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться, что конфигурация работает правильно.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure Active Directory в Projectplace, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Projectplace** выберите **Единый вход**.

    ![Выбор пункта "Единый вход"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Выбор метода единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Значок "Изменить"](common/edit-urls.png)

4. В диалоговом окне **Базовая конфигурация SAML** в поле **URL-адрес для входа** введите URL-адрес в следующем формате:

    `https://<company>.projectplace.com`

   ![Диалоговое окно "Базовая конфигурация SAML"](common/sp-signonurl.png)
    > [!NOTE]
    > Это значение является заполнителем. Необходимо использовать фактический URL-адрес для входа. Для получения этого значения обратитесь к [группе поддержки Projectplace](https://success.planview.com/Projectplace/Support). Можно также ознакомиться с шаблонами в диалоговом окне **Базовая конфигурация SAML** на портале Azure.

5. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните ссылку **Скачать** рядом с пунктом **XML метаданных федерации** и сохраните сертификат на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

6. Требуемые URL-адреса можно скопировать из раздела **Настройка Projectplace**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    1. **URL-адрес входа**.

    1. **Идентификатор Azure AD**

    1. **URL-адрес выхода**.

### <a name="configure-projectplace-single-sign-on"></a>Настройка единого входа в Projectplace

Чтобы настроить единый вход на стороне **Projectplace**, нужно отправить скачанный **XML-файл метаданных федерации**, сертификат и соответствующие URL-адреса, скопированные на портале Azure, [группе поддержки Projectplace](https://success.planview.com/Projectplace/Support). Эта группа обеспечит правильную настройку подключения для единого входа SAML с обеих сторон.

>[!NOTE]
>Настройку единого входа должна выполнять [служба поддержки Projectplace](https://success.planview.com/Projectplace/Support). Сразу же после завершения настройки вы получите уведомление.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

В этом разделе описано, как создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Выбор "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Выбор "Новый пользователь"](common/new-user.png)

3. В диалоговом окне **Пользователь** сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    1. В поле **Имя** введите **BrittaSimon**.
  
    1. В поле **Имя пользователя** введите **BrittaSimon @\<ваш_домен>.\<доменная_зона>** . (Например, BrittaSimon@contoso.com).

    1. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    1. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Projectplace.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **Projectplace**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. Из списка приложений выберите **Projectplace**.

    ![Список приложений](common/all-applications.png)

3. В области слева выберите **Пользователи и группы**.

    ![Выбор параметра "Пользователи и группы"](common/users-groups-blade.png)

4. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** — **Пользователи и группы**.

    ![Выбор элемента "Добавить пользователя"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** выберите **Britta Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка. В нижней части экрана нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** выберите **Назначить**.

### <a name="create-a-projectplace-test-user"></a>Создание тестового пользователя Projectplace

Чтобы разрешить пользователям Azure AD входить в Projectplace, их нужно добавить в Projectplace. Необходимо добавить их вручную.

Чтобы создать учетную запись пользователя, выполните следующие действия.

1. Выполните вход на корпоративный сайт **Projectplace** с правами администратора.

2. Перейдите к разделу **People** (Люди) и щелкните **Members** (Участники).
   
    ![Переход к разделу "People" (Люди) и выбор "Members" (Участники)](./media/projectplace-tutorial/ic790228.png "\"People\" (Люди)")

3. Щелкните **Add Member** (Добавить участника).
   
    ![Выбор "Add Member" (Добавить участника)](./media/projectplace-tutorial/ic790232.png "\"Add Members\" (Добавление участников)")

4. В разделе **Add Member** (Добавление участника) выполните следующие действия.
   
    ![Раздел "Add Member" (Добавление участника)](./media/projectplace-tutorial/ic790233.png "\"New Members\" (Новые участники)")
   
    1. В поле **New Members** (Новые участники) введите адрес электронной почты действующей учетной записи Azure AD, которую вы хотите добавить.
   
    1. Нажмите кнопку **Отправить**.

   Владельцу учетной записи Azure AD будет отправлено электронное сообщение со ссылкой для подтверждения учетной записи перед ее активацией.

>[!NOTE]
>Вы можете использовать любые другие инструменты или API, предоставленные Projectplace, для подготовки учетных записей пользователей Azure AD.

### <a name="test-single-sign-on"></a>Проверка единого входа

Теперь необходимо проверить конфигурацию единого входа Azure AD с помощью Панели доступа.

Щелкнув элемент "Projectplace" на Панели доступа, вы автоматически войдете в экземпляр Projectplace, для которого настроили единый вход. Дополнительные сведения см. в разделе [Доступ и использование приложений на портале "Мои приложения"](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
