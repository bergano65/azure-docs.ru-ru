---
title: Руководство. Интеграция Azure Active Directory с MyWorkDrive | Документация Майкрософт
description: Сведения о том, как настроить единый вход между Azure Active Directory и MyWorkDrive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4d049778-3c7b-46c0-92a4-f2633a32334b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 14cd7bea6707e6f724829a28b57604553e7405c0
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56199771"
---
# <a name="tutorial-azure-active-directory-integration-with-myworkdrive"></a>Руководство. Интеграция Azure Active Directory с MyWorkDrive

В этом руководстве описано, как интегрировать MyWorkDrive с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением MyWorkDrive обеспечивает следующие преимущества.

- C помощью Azure AD вы можете контролировать доступ к MyWorkDrive.
- Вы можете включить автоматический вход пользователей в MyWorkDrive (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с MyWorkDrive, вам потребуется:

- подписка Azure AD;
- подписка с поддержкой единого входа MyWorkDrive.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление MyWorkDrive из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-myworkdrive-from-the-gallery"></a>Добавление MyWorkDrive из коллекции

Чтобы настроить интеграцию MyWorkDrive с Azure AD, вам потребуется добавить MyWorkDrive из коллекции в список управляемых приложений SaaS.

**Чтобы добавить MyWorkDrive из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"][3]

4. В поле поиска введите **MyWorkDrive**, выберите **MyWorkDrive** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![MyWorkDrive в списке результатов](./media/myworkdrive-tutorial/tutorial_myworkdrive_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в MyWorkDrive с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в MyWorkDrive соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в MyWorkDrive.

Чтобы настроить и проверить единый вход Azure AD в MyWorkDrive, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя приложения MyWorkDrive](#creating-a-myworkdrive-test-user)** требуется для того, чтобы в MyWorkDrive существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении MyWorkDrive.

**Чтобы настроить единый вход Azure AD в MyWorkDrive, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **MyWorkDrive** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Выбрать метод единого входа** щелкните **Выбрать** для режима **SAML**, чтобы включить единый вход.

    ![Настройка единого входа](common/tutorial_general_301.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Настройка единого входа](common/editconfigure.png)

4. Если вы хотите настроить приложение в режиме, инициируемом **поставщиком удостоверений**, в разделе **Базовая конфигурация SAML** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа приложения MyWorkDrive](./media/myworkdrive-tutorial/tutorial_myworkdrive_url.png)

    В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<SERVER.DOMAIN.COM>/SAML/AssertionConsumerService.aspx`.

5. Чтобы настроить приложение для работы в режиме, инициируемом **поставщиком услуг**, щелкните **Задать дополнительные URL-адреса** и выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа приложения MyWorkDrive](./media/myworkdrive-tutorial/tutorial_myworkdrive_url1.png)

     В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<SERVER.DOMAIN.COM>/Account/Login-saml` 

    > [!NOTE]
    > Эти значения приведены для примера. Измените их на фактические значения URL-адреса ответа и URL-адреса входа.  Введите имя узла корпоративного сервера MyWorkDrive, например так:
    > 
    > URL-адрес ответа: `https://yourserver.yourdomain.com/SAML/AssertionConsumerService.aspx`.
    > 
    > URL-адрес для входа: `https://yourserver.yourdomain.com/Account/Login-saml`.
    > 
    > Если вы не знаете, как указать в этих параметрах имя узла и SSL-сертификат, обратитесь к группе поддержки клиентов MyWorkDrive.

6. На странице **Сертификат подписи SAML** в разделе **Сертификат подписи SAML** щелкните **значок** копирования, чтобы скопировать **URL-адрес метаданных федерации приложений** и сохранить его на компьютере.

    ![Ссылка для скачивания сертификата](./media/myworkdrive-tutorial/tutorial_myworkdrive_certificate.png)

7. В другом окне браузера войдите в MyWorkDrive с правами администратора безопасности.

8. На сервере MyWorkDrive на панели администрирования щелкните **ENTERPRISE** (Корпоративное приложение) и выполните следующие действия:

    ![Администратор](./media/myworkdrive-tutorial/tutorial_myworkdrive_admin.png)

    a. Включите **SAML/ADFS SSO** (единый вход в SAML и распределенную файловую систему Azure).

    б) Выберите **SAML – Azure AD**

    c. В текстовое поле **Azure App Federation Metadata Url** (URL-адрес метаданных федерации приложения Azure) вставьте **URL-адрес метаданных федерации приложения**, скопированный на портале Azure.

    4.3. Нажмите кнопку **Сохранить**

    >[!NOTE]
    >Дополнительные сведения см. в справочной статье об [интеграции MyWorkDrive с Azure AD](https://www.myworkdrive.com/support/saml-single-sign-on-azure-ad/).

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Создание пользователя Azure AD][100]

2. В верхней части экрана выберите **Новый пользователь**.

    ![Создание тестового пользователя Azure AD](common/create_aaduser_01.png) 

3. В разделе свойств пользователя сделайте следующее.

    ![Создание тестового пользователя Azure AD](common/create_aaduser_02.png)

    a. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon@yourcompanydomain.extension**.  
    Например, BrittaSimon@contoso.com

    c. Выберите **Свойства**, установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="creating-a-myworkdrive-test-user"></a>Создание тестового пользователя MyWorkDrive

В этом разделе описано, как создать пользователя Britta Simon в приложении MyWorkDrive. Обратитесь в  [службу поддержки MyWorkDrive](mailto:support@myworkdrive.com), чтобы добавить пользователей на платформу MyWorkDrive. Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к MyWorkDrive.

1. На портале Azure перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Назначение пользователя][201]

2. В списке приложений выберите **MyWorkDrive**.

    ![Настройка единого входа](./media/myworkdrive-tutorial/tutorial_myworkdrive_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент MyWorkDrive на панели доступа, вы автоматически войдете в приложение MyWorkDrive.
Дополнительные сведения о панели доступа см. в статье с [общими сведениями о панели доступа](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
