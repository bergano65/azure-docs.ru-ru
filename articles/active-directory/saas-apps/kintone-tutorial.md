---
title: Руководство по Интеграция Azure Active Directory с Kintone | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Kintone.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c2b947dc-e1a8-4f5f-b40e-2c5180648e4f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: a9245ab5a2d51ee3995ff0c614cafaebdc20d00f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67098808"
---
# <a name="tutorial-azure-active-directory-integration-with-kintone"></a>Руководство по Интеграция Azure Active Directory с Kintone

В этом руководстве описано, как интегрировать Kintone с Azure Active Directory (Azure AD).
Интеграция Kintone с Azure AD обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ пользователей к Kintone.
* Можно включить автоматический вход пользователей в Kintone (единый вход) с помощью учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Kintone, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/));
* подписка Kintone с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Kintone поддерживает единый вход, инициированный **поставщиком службы**.

## <a name="adding-kintone-from-the-gallery"></a>Добавление Kintone из коллекции

Чтобы настроить интеграцию Kintone с Azure AD, необходимо добавить Kintone из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Kintone из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Kintone**, выберите **Kintone** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![Kintone в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Kintone для тестового пользователя с именем **Britta Simon**.
Чтобы обеспечить работу единого входа, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Kintone.

Чтобы настроить и проверить единый вход в Azure AD в Kintone, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в Kintone](#configure-kintone-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя Kintone](#create-kintone-test-user)** требуется для того, чтобы в Kintone существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в Kintone, сделайте следующее:

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Kintone** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    ![Сведения о домене и URL-адресах единого входа приложения Kintone](common/sp-identifier.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<companyname>.kintone.com`.

    b. В текстовом поле **Идентификатор (сущности)** введите URL-адрес в следующем формате:
    
    | |
    |--|
    | `https://<companyname>.cybozu.com` |
    | `https://<companyname>.kintone.com` |

    > [!NOTE]
    > Эти значения приведены для примера. Необходимо обновить эти значения действующим URL-адресом для входа и идентификатором. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов Kintone](https://www.kintone.com/contact/). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

5. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Загрузить**, чтобы загрузить требуемый **сертификат (Base64)** из предложенных вариантов, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

6. Требуемый URL-адрес можно скопировать из раздела **Настройка Kintone**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    b. Идентификатор Azure AD

    c. URL-адрес выхода.

### <a name="configure-kintone-single-sign-on"></a>Настройка единого входа в Kintone

1. В другом окне веб-браузера войдите на свой корпоративный веб-сайт **Kintone** с правами администратора.

1. Щелкните **значок параметров**.

    ![Параметры](./media/kintone-tutorial/ic785879.png "Параметры")

1. Щелкните **Users & System Administration** (Администрирование пользователей и системы).

    ![Users & System Administration](./media/kintone-tutorial/ic785880.png "Users & System Administration") (Администрирование пользователей и системы)

1. Перейдите в раздел **System Administration \> Security** (Системное администрирование > Безопасность) и щелкните **Login** (Вход).

    ![Login](./media/kintone-tutorial/ic785881.png "Login") (Вход)

1. Установите флажок **Включить проверку подлинности SAML**.

    ![Аутентификация SAML](./media/kintone-tutorial/ic785882.png "Аутентификация SAML")

1. В разделе «Проверка подлинности SAML» выполните следующие действия.

    ![Аутентификация SAML](./media/kintone-tutorial/ic785883.png "Аутентификация SAML")

    a. В текстовое поле **Login URL** (URL-адрес входа) вставьте **URL-адрес входа**, скопированный на портале Azure.

    b. В текстовое поле **Logout URL** (URL-адрес выхода) вставьте **URL-адрес выхода**, скопированный на портале Azure.

    c. Чтобы загрузить сертификат, скачанный на портале Azure, щелкните **Обзор**.

    d. Выберите команду **Сохранить**.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    а. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите `brittasimon@yourcompanydomain.extension`.  
    Например BrittaSimon@contoso.com.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как включить единый вход Azure для пользователя Britta Simon и предоставить этому пользователю доступ к Kintone.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **Kintone**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Kintone**.

    ![Ссылка на Kintone в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-kintone-test-user"></a>Создание тестового пользователя Kintone

Чтобы пользователи Azure AD могли выполнять вход в Kintone, их следует подготовить в Kintone. В случае с Kintone подготовка выполняется вручную.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Чтобы подготовить учетную запись пользователя, выполните следующие действия.

1. Выполните вход на свой корпоративный веб-сайт **Kintone** с правами администратора.

1. Щелкните **значок параметров**.

    ![Параметры](./media/kintone-tutorial/ic785879.png "Параметры")

1. Щелкните **Users & System Administration** (Администрирование пользователей и системы).

    ![Users & System Administration](./media/kintone-tutorial/ic785880.png "Users & System Administration") (Администрирование пользователей и системы)

1. В разделе **User Administration** (Администрирование пользователей) щелкните **Departments & Users** (Отделы и пользователи).

    ![Department & Users](./media/kintone-tutorial/ic785888.png "Department & Users") (Отделы и пользователи)

1. Щелкните **Новый пользователь**.

    ![Новые пользователи](./media/kintone-tutorial/ic785889.png "Новые пользователи")

1. В разделе **New User** (Новый пользователь) выполните следующие действия.

    ![Новые пользователи](./media/kintone-tutorial/ic785890.png "Новые пользователи")

    a. Задайте значения в полях **Display Name** (Отображаемое имя), **Login Name** (Имя для входа), **New Password** (Новый пароль), **Confirm Password** (Подтверждение пароля), **E-mail Address** (Адрес электронной почты) и другие данные действующей учетной записи Azure AD, которую вы хотите подготовить.

    b. Выберите команду **Сохранить**.

> [!NOTE]
> Вы можете использовать любые другие средства создания учетной записи пользователя Kintone или API, предоставляемые Kintone для подготовки учетных записей пользователя AAD.

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Kintone на Панели доступа, вы автоматически войдете в приложение Kintone, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что такое условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
