---
title: Руководство по Интеграция Azure Active Directory с Halogen Software | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Halogen Software.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2ca2298d-9a0c-4f14-925c-fa23f2659d28
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfcf637810a3cbbc70216af39918847e703ca32f
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56869740"
---
# <a name="tutorial-azure-active-directory-integration-with-halogen-software"></a>Руководство. Интеграция Azure Active Directory с Halogen Software

В этом руководстве описано, как интегрировать Halogen Software с Azure Active Directory (Azure AD).
Интеграция Halogen Software с Azure AD обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к Halogen Software.
* Учетные записи Azure AD позволяют включить автоматический вход пользователей в Halogen Software (единый вход).
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Halogen Software, вам потребуется следующее:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* подписка Halogen Software с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* В Halogen Software поддерживается единый вход, инициированный **поставщиком услуг**

## <a name="adding-halogen-software-from-the-gallery"></a>Добавление Halogen Software из коллекции.

Чтобы настроить интеграцию Halogen Software с Azure AD, вам потребуется добавить Halogen Software из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Halogen Software из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Halogen Software**, выберите **Halogen Software** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![Halogen Software в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В рамках этого раздела вы настроите и проверите единый вход Azure AD в Halogen Software для тестового пользователя **Britta Simon**.
Чтобы обеспечить единый вход, свяжите пользователя Azure AD с соответствующим пользователем в Halogen Software.

Чтобы настроить и проверить единый вход в Azure AD в Halogen Software, вам потребуется выполнить действия в следующих блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в Halogen Software](#configure-halogen-software-single-sign-on)** требуется, чтобы определить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя Halogen Software](#create-halogen-software-test-user)** требуется, чтобы в Halogen Software существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход в Azure AD с помощью Halogen Software, сделайте следующее:

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Halogen Software** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    ![Сведения о домене и URL-адресах единого входа приложения Halogen](common/sp-identifier.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://global.hgncloud.com/<companyname>`.

    b. В текстовом поле **Идентификатор (сущности)** введите URL-адрес в следующем формате:

    | |
    |--|
    | `https://global.halogensoftware.com/<companyname>`|
    | `https://global.hgncloud.com/<companyname>`|
    | |

    > [!NOTE]
    > Эти значения приведены для примера. Необходимо обновить эти значения действующим URL-адресом для входа и идентификатором. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов Halogen Software](https://support.halogensoftware.com/). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

4. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать нужный вам **XML метаданных федерации**, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

6. Требуемые URL-адреса можно скопировать из раздела **настройки Halogen Software**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    б) Идентификатор Azure AD.

    в) URL-адрес выхода.

### <a name="configure-halogen-software-single-sign-on"></a>Настройка единого входа в Halogen Software

1. В отдельном окне браузера войдите в приложение **Halogen Software** под учетной записью администратора.

2. Откройте вкладку **Параметры** .
  
    ![Что такое Azure AD Connect?](./media/halogen-software-tutorial/tutorial_halogen_12.png)

3. На панели навигации слева щелкните **Настройка SAML**.
  
    ![Что такое Azure AD Connect?](./media/halogen-software-tutorial/tutorial_halogen_13.png)

4. На странице **Настройка SAML** сделайте следующее:

    ![Что такое Azure AD Connect?](./media/halogen-software-tutorial/tutorial_halogen_14.png)

    a. В качестве значения поля **Уникальный идентификатор** выберите **NameID**.

    b. В качестве значения поля **Unique Identifier Maps To** (Уникальный идентификатор сопоставляется с) выберите **Имя пользователя**.
  
    c. Для отправки скачанного файла метаданных нажмите кнопку **Обзор**, чтобы выбрать файл, а затем щелкните **Отправить файл**.

    4.3. Чтобы проверить конфигурацию, нажмите кнопку **Запустить тест**.

    > [!NOTE]
    > Подождите, пока не появится сообщение "*Проверка SAML завершена. Закройте это окно*". Закройте окно браузера. Флажок **Включить SAML** установлен, только если проверка завершена.

    д. Выберите **Включить SAML**.

    Е. Нажмите кнопку **Сохранить изменения**.

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

В этом разделе описано, как включить единый вход Azure для пользователя Britta Simon и предоставить этому пользователю доступ к Halogen Software.

1. На портале Azure последовательно выберите **Корпоративные приложения**, **Все приложения** и **Halogen Software**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Halogen Software**.

    ![Ссылка на Halogen Software в списке "Приложения"](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-halogen-software-test-user"></a>Создание тестового пользователя Halogen Software

Цель этого раздела — создать пользователя с именем Britta Simon в Halogen Software.

**Чтобы создать пользователя с именем Britta Simon в Halogen Software, выполните следующие действия:**

1. Войдите в приложение **Halogen Software** под учетной записью администратора.

2. Щелкните вкладку **Центр пользователей**, затем щелкните **Создать пользователя**.

    ![Что такое Azure AD Connect?](./media/halogen-software-tutorial/tutorial_halogen_300.png)  

3. На странице диалогового окна **Новый пользователь** выполните следующие действия.

    ![Что такое Azure AD Connect?](./media/halogen-software-tutorial/tutorial_halogen_301.png)

    a. В текстовом поле **Имя** введите имя, например **Britta**.

    b. В текстовом поле **Фамилия** введите фамилию, например **Simon**.

    c. В текстовом поле **Имя пользователя** введите **Britta Simon**, имя пользователя на портале Azure.

    4.3. В текстовом поле **Пароль** введите пароль для пользователя Britta.

    д. Выберите команду **Сохранить**.

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Halogen Software на Панели доступа, вы автоматически войдете в приложение Halogen Software, для которого выполнялась настройка единого входа. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)