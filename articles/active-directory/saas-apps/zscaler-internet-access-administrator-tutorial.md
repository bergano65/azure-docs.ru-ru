---
title: Руководство. Интеграция Azure Active Directory с Zscaler Internet Access Administrator | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Zscaler Internet Access Administrator.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: ea555097-bf62-45dd-9b45-b75c50324a69
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 767c0dac960a67208a7f9f08a6158453fe1a576d
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56880042"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-internet-access-administrator"></a>Руководство. Интеграция Azure Active Directory с Zscaler Internet Access Administrator

В этом руководстве описано, как интегрировать Zscaler Internet Access Administrator с Azure Active Directory (Azure AD).
Интеграция Azure Active Directory с Zscaler Internet Access Administrator обеспечивает следующие преимущества.

* С помощью Azure Active Directory вы можете контролировать доступ к Zscaler Internet Access Administrator.
* Вы можете включить автоматический вход пользователей в Zscaler Private Internet Administrator (единый вход) через их учетные записи Azure Active Directory.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure Active Directory с Zscaler Internet Access Administrator, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* Подписка Zscaler Internet Access Administrator

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Zscaler Internet Access Administrator поддерживает единый вход, инициированный **IDP**

## <a name="adding-zscaler-internet-access-administrator-from-the-gallery"></a>Добавление Zscaler Internet Access Administrator из коллекции

Чтобы настроить интеграцию Zscaler Internet Access Administrator с Azure Active Directory, необходимо добавить Zscaler Internet Access Administrator из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Zscaler Internet Access Administrator из коллекции, сделайте следующее.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Zscaler Internet Access Administrator**, выберите **Zscaler Internet Access Administrator** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![Zscaler Internet Access Administrator в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure Active Directory в Zscaler Internet Access Administrator с использованием тестового пользователя **Britta Simon**.
Для работы единого входа необходимо установить связь между пользователем Azure Active Directory и соответствующим пользователем в Zscaler Internet Access Administrator.

Чтобы настроить и проверить единый вход в Zscaler Internet Access Administrator через Azure Active Directory, вам потребуется выполнить следующие блоки.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в Zscaler Internet Access Administrator](#configure-zscaler-internet-access-administrator-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя Zscaler Internet Access Administrator](#create-zscaler-internet-access-administrator-test-user)** требуется, чтобы в Zscaler Internet Access Administrator существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure Active Directory.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход в Zscaler Internet Access Administrator через Azure Active Directory, сделайте следующее.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Zscaler Internet Access Administrator** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. На странице **Настройка единого входа с помощью SAML** нажмите кнопку **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Сведения о домене и URL-адресах единого входа для Zscaler Internet Access Administrator](common/idp-intiated.png)

    a. В текстовом поле **Идентификатор** введите требуемый URL-адрес:

    | |
    |--|
    | `https://admin.zscaler.net` |
    | `https://admin.zscalerone.net` |
    | `https://admin.zscalertwo.net` |
    | `https://admin.zscalerthree.net` |
    | `https://admin.zscloud.net` |
    | `https://admin.zscalerbeta.net` |

    b. В текстовом поле **URL-адрес ответа** введите требуемый URL-адрес:

    | |
    |--|
    | `https://admin.zscaler.net/adminsso.do` |
    | `https://admin.zscalerone.net/adminsso.do` |
    | `https://admin.zscalertwo.net/adminsso.do` |
    | `https://admin.zscalerthree.net/adminsso.do` |
    | `https://admin.zscloud.net/adminsso.do` |
    | `https://admin.zscalerbeta.net/adminsso.do` |

5. Приложение Zscaler Internet Access Administrator ожидает утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно в разделе **User Attributes & Claims** (Атрибуты пользователя и утверждения) на странице интеграции приложения. На странице **Настройка единого входа с помощью SAML** нажмите кнопку **Изменить**, чтобы открыть диалоговое окно **User Attributes & Claims** (Атрибуты пользователя и утверждения).

    ![Ссылка "Атрибуты"](./media/zscaler-internet-access-administrator-tutorial/tutorial_zscaler-internet_attribute.png)

6. В разделе **Утверждения пользователя** диалогового окна **Атрибуты пользователя** настройте атрибут токена SAML, как показано на рисунке выше, и выполните следующие действия.

    | ИМЯ  | Исходный атрибут  |
    | ---------| ------------ |
    | Роль     | user.assignedroles |

    a. Щелкните **Добавить новое утверждение**, чтобы открыть диалоговое окно **Управление утверждениями пользователя**.

    ![изображение](./common/new-save-attribute.png)
    
    ![изображение](./common/new-attribute-details.png)

    b. В списке **Источник атрибута** выберите значение атрибута.

    c. Нажмите кнопку **ОК**.

    4.3. Выберите команду **Сохранить**.

    > [!NOTE]
    > Перейдите по [этой ссылке](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management), чтобы прочитать о настройке роли в Azure Active Directory.

7. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Загрузить**, чтобы загрузить требуемый **сертификат (Base64)** из предложенных вариантов, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

8. Скопируйте требуемый URL-адрес из раздела **Настройка Zscaler Internet Access Administrator**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    б) Идентификатор Azure AD.

    в) URL-адрес выхода.

### <a name="configure-zscaler-internet-access-administrator-single-sign-on"></a>Настройка единого входа в Zscaler Internet Access Administrator

1. В другом окне веб-браузера войдите в пользовательский интерфейс администратора Zscaler Internet Access.

2. Выберите **Administration > Administrator Management** (Администрирование) > Управление администраторами), выполните следующие действия и щелкните Save (Сохранить):

    ![Администрирование](./media/zscaler-internet-access-administrator-tutorial/AdminSSO.png "Администрирование")

    a. Установите флажок **Enable SAML Authentication** (Включить аутентификацию SAML).

    b. Нажмите **Upload** (Отправить), чтобы загрузить сертификат для подписи SAML, который вы скачали на портале Azure в разделе **Public SSL Certificate** (Публичный SSL-сертификат).

    c. При необходимости для обеспечения дополнительной безопасности добавьте сведения об **издателе** для проверки издателя ответа SAML.

3. В пользовательском интерфейсе администратора выполните следующие действия:

    ![Администрирование](./media/zscaler-internet-access-administrator-tutorial/ic800207.png)

    a. Наведите указатель мыши на меню **Activation** (Активация) в нижнем левом углу.

    b. Щелкните **Активировать**.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD 

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    а. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon@yourcompanydomain.extension**.  
    Например BrittaSimon@contoso.com.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Zscaler Internet Access Administrator.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем **Zscaler Internet Access Administrator**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений введите и выберите **Zscaler Internet Access Administrator**.

    ![Ссылка на Zscaler Internet Access Administrator в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-zscaler-internet-access-administrator-test-user"></a>Создание тестового пользователя Zscaler Internet Access Administrator

Цель этого раздела — создать пользователя с именем Britta Simon в Zscaler Internet Access Administrator. Zscaler Internet Access не поддерживает JIT-подготовку для единого входа администратора. Необходимо вручную создать учетную запись администратора.
Инструкции по созданию учетной записи администратора см. в документации по Zscaler:

https://help.zscaler.com/zia/adding-admins

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Zscaler Internet Access Administrator на панели доступа, вы автоматически войдете в пользовательский интерфейс администратора Zscaler Internet Access, для которого настроен единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
