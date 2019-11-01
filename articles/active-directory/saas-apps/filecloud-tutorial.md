---
title: Руководство по Интеграция Azure Active Directory с FileCloud | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и FileCloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2263e583-3eb2-4a06-982d-33f5f54858f4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6b4eecc3429dbf43a74148f39cff5f4dc697ea5f
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73156554"
---
# <a name="tutorial-azure-active-directory-integration-with-filecloud"></a>Руководство по Интеграция Azure Active Directory с FileCloud

В этом руководстве описано, как интегрировать FileCloud с Azure Active Directory (Azure AD).
Интеграция FileCloud с Azure AD обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к FileCloud.
* Учетные записи Azure AD позволяют включить автоматический вход пользователей в приложение FileCloud (единый вход).
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с FileCloud, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* подписка FileCloud с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* В FileCloud поддерживается единый вход, инициированный **поставщиком услуг**

* В FileCloud поддерживается **JIT-подготовка** пользователей.

## <a name="adding-filecloud-from-the-gallery"></a>Добавление FileCloud из коллекции

Чтобы настроить интеграцию FileCloud с Azure AD, необходимо добавить FileCloud из коллекции в список управляемых приложений SaaS.

**Чтобы добавить FileCloud из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **FileCloud**, выберите **FileCloud** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![FileCloud в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В рамках этого раздела вы настроите и проверите единый вход Azure AD в приложение FileCloud с помощью тестового пользователя **Britta Simon**.
Чтобы обеспечить единый вход, свяжите пользователя Azure AD с соответствующим пользователем в FileCloud.

Чтобы настроить и проверить единый вход Azure AD в FileCloud, выполните следующие стандартные действия.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в FileCloud](#configure-filecloud-single-sign-on)** требуется, чтобы определить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя FileCloud](#create-filecloud-test-user)** требуется, чтобы в FileCloud также существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в FileCloud, сделайте следующее:

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **FileCloud** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    ![Сведения о домене и URL-адресах единого входа приложения FileCloud](common/sp-identifier.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<subdomain>.filecloudonline.com`.

    b. В текстовом поле **Идентификатор (сущности)** введите URL-адрес в следующем формате: `https://<subdomain>.filecloudonline.com/simplesaml/module.php/saml/sp/metadata.php/default-sp`.

    > [!NOTE]
    > Эти значения приведены для примера. Необходимо обновить эти значения действующим URL-адресом для входа и идентификатором. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов FileCloud](mailto:support@codelathe.com). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

5. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать нужный вам **XML метаданных федерации**, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

6. Требуемые URL-адреса можно скопировать из раздела **настройки FileCloud**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    б) Идентификатор Azure AD.

    в) URL-адрес выхода.

### <a name="configure-filecloud-single-sign-on"></a>Настройка единого входа в FileCloud

1. В другом окне веб-браузера войдите в свой клиент FileCloud с правами администратора.

2. В левой панели навигации щелкните **Settings**(Параметры). 
   
    ![Настройка единого входа на стороне приложения](./media/filecloud-tutorial/tutorial_filecloud_000.png)

3. Щелкните вкладку **SSO** (Единый вход) в разделе "Settings" (Параметры). 
   
    ![Настройка единого входа на стороне приложения](./media/filecloud-tutorial/tutorial_filecloud_001.png)

4. На панели **Single Sign On (SSO) Settings** (Параметры единого входа) для параметра **Default SSO Type** (Тип единого входа по умолчанию) выберите значение **SAML**.
   
    ![Настройка единого входа на стороне приложения](./media/filecloud-tutorial/tutorial_filecloud_002.png)

5. В текстовое поле **IdP End Point URL** (URL-адрес конечной точки IdP) вставьте значение **идентификатора Azure AD**, скопированное на портале Azure.

    ![Настройка единого входа на стороне приложения](./media/filecloud-tutorial/tutorial_filecloud_003.png)

6. Откройте скачанный файл метаданных в Блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте в текстовое поле **IdP Meta Data** (Метаданные IdP) на панели **SAML Settings** (Параметры SAML).

    ![Настройка единого входа на стороне приложения](./media/filecloud-tutorial/tutorial_filecloud_004.png)

7. Нажмите кнопку **Сохранить** .

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

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к FileCloud.

1. На портале Azure последовательно выберите **Корпоративные приложения**, **Все приложения** и **FileCloud**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. Из списка приложений выберите **FileCloud**.

    ![Ссылка на FileCloud в списке "Приложения"](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-filecloud-test-user"></a>Создание тестового пользователя FileCloud

В рамках этого раздела вы создадите в FileCloud пользователя Britta Simon. В FileCloud поддерживается JIT-подготовка пользователей, включенная по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Если пользователь еще не существует в FileCloud, он создается после проверки подлинности.

>[!NOTE]
>Если вам нужно вручную создать пользователя, обратитесь в [службу поддержки клиентов FileCloud](mailto:support@codelathe.com).

### <a name="test-single-sign-on"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку FileCloud на Панели доступа, вы автоматически войдете в приложение FileCloud, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

