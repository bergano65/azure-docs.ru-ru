---
title: Руководство. Интеграция Azure Active Directory с Salesforce | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в приложении Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: d2d7d420-dc91-41b8-a6b3-59579e043b35
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 239298959efa5c17d2f3d0c0aa5c4c30853c076f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57902041"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce"></a>Руководство по Интеграция Azure Active Directory с Salesforce

В этом руководстве описано, как интегрировать Salesforce с Azure Active Directory (Azure AD).
Интеграция Azure AD с приложением Salesforce обеспечивает следующие преимущества.

* С помощью Azure AD можно управлять доступом к Salesforce.
* Можно включить автоматический вход пользователей в Salesforce (единый вход) с помощью учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Salesforce, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* Подписка Salesforce с поддержкой единого входа

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Salesforce поддерживает единый вход, инициированный **поставщиком услуг**.

* Salesforce поддерживает подготовку пользователей **JIT**

* Salesforce поддерживает [**автоматическую** подготовку пользователей](salesforce-provisioning-tutorial.md).

## <a name="adding-salesforce-from-the-gallery"></a>Добавление Salesforce из коллекции

Чтобы настроить интеграцию приложения Salesforce с Azure AD, вам нужно добавить Salesforce из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Salesforce из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Salesforce**, выберите **Salesforce** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![Salesforce в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описано, как настроить и проверить единый вход Azure AD в Salesforce с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Salesforce.

Чтобы настроить и проверить единый вход Azure AD в Salesforce, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в  Salesforce](#configure-salesforce-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя Salesforce](#create-salesforce-test-user)** требуется для создания в Salesforce пользователя Britta Simon, связанного с представлением этого пользователя в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в Salesforce, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Salesforce** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    ![Сведения о домене и URL-адресах единого входа для приложения Salesforce](common/sp-identifier.png)

    a. В текстовом поле **URL-адрес для входа** введите значение в следующем формате:

    Учетная запись предприятия: `https://<subdomain>.my.salesforce.com`

    Учетная запись разработчика: `https://<subdomain>-dev-ed.my.salesforce.com`

    b. В текстовое поле **Идентификатор** введите значение в следующем формате:

    Учетная запись предприятия: `https://<subdomain>.my.salesforce.com`

    Учетная запись разработчика: `https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE]
    > Эти значения приведены для примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Чтобы получить их, обратитесь в [службу поддержки клиентов Salesforce](https://help.salesforce.com/support).

5. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать нужный вам **XML метаданных федерации**, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

6. Скопируйте требуемый URL-адрес из раздела **Настройка Salesforce**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    б) Идентификатор Azure AD.

    в) URL-адрес выхода.

### <a name="configure-salesforce-single-sign-on"></a>Настройка Salesforce единого входа

1. Откройте новую вкладку в браузере и войдите в учетную запись администратора Salesforce.

2. Щелкните **Setup** (Настройка) под **значком параметров** в правом верхнем углу страницы.

    ![Настройка единого входа](./media/salesforce-tutorial/configure1.png)

3. В области навигации прокрутите вниз до элемента **Settings** (Параметры) и щелкните **Identity** (Удостоверение), чтобы развернуть соответствующий раздел. Затем щелкните **Параметры единого входа**.

    ![Настройка единого входа](./media/salesforce-tutorial/sf-admin-sso.png)

4. На странице **Single Sign-On Settings** (Параметры единого входа) нажмите кнопку **Edit** (Изменить).

    ![Настройка единого входа](./media/salesforce-tutorial/sf-admin-sso-edit.png)

    > [!NOTE]
    > Если не удается включить параметры единого входа для своей учетной записи Salesforce, возможно, вам придется обратиться за помощью в [службу поддержки Salesforce](https://help.salesforce.com/support).

5. Выберите **SAML Enabled** (SAML включен), а затем щелкните **Save** (Сохранить).

      ![Настройка единого входа](./media/salesforce-tutorial/sf-enable-saml.png)

6. Чтобы настроить параметры единого входа SAML, щелкните **New from Metadata File** (Создать из файла метаданных).

    ![Настройка единого входа](./media/salesforce-tutorial/sf-admin-sso-new.png)

7. Щелкните **Выбрать файл** для отправки XML-файла метаданных, загруженного с портала Azure, и щелкните **Создать**.

    ![Настройка единого входа](./media/salesforce-tutorial/xmlchoose.png)

8. На странице **SAML Single Sign-On Settings** (Параметры единого входа SAML) поля заполняются автоматически. Щелкните "Сохранить".

    ![Настройка единого входа](./media/salesforce-tutorial/salesforcexml.png)

9. В области навигации слева в Salesforce щелкните **Company Settings** (Параметры компании), чтобы развернуть соответствующий раздел, и выберите пункт **My Domain** (Мой домен).

    ![Настройка единого входа](./media/salesforce-tutorial/sf-my-domain.png)

10. Прокрутите страницу вниз до раздела **Authentication Configuration** (Конфигурация аутентификации) и нажмите кнопку **Edit** (Изменить).

    ![Настройка единого входа](./media/salesforce-tutorial/sf-edit-auth-config.png)

11. В разделе **Authentication Configuration** (Конфигурация аутентификации), установите флажок **AzureSSO** (Единый вход Azure) для параметра **Authentication Service** (Служба проверки подлинности) конфигурации единого входа SAML и нажмите кнопку **Save** (Сохранить).

    ![Настройка единого входа](./media/salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > Если выбрано несколько служб проверки подлинности, то при попытке инициировать единый вход в среду Salesforce пользователям будет предложено уточнить, с помощью какой службы проверки подлинности нужно выполнить вход. Чтобы этого избежать, **снимите флажки для всех других служб проверки подлинности**.

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

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Salesforce.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем **Salesforce**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Salesforce**.

    ![Ссылка на Salesforce в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-salesforce-test-user"></a>Создание тестового пользователя Salesforce

В этом разделе вы создадите в Salesforce пользователя с именем Britta Simon. Приложение Salesforce поддерживает JIT-подготовку. Эта функция включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Если пользователь в Salesforce еще не существует, он создается при попытке доступа к приложению Salesforce. Salesforce также поддерживает автоматическую подготовку пользователей. Дополнительные сведения о настройке автоматической подготовки пользователей можно найти [здесь](salesforce-provisioning-tutorial.md).

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

При нажатии на плитку Salesforce на панели доступа вы автоматически войдете в Salesforce, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Руководство по настройке Google Apps для автоматической подготовки пользователей](salesforce-provisioning-tutorial.md)
