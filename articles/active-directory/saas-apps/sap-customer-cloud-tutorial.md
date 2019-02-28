---
title: Руководство. Интеграция Azure Active Directory с SAP Cloud for Customer | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в SAP Cloud for Customer.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 90154dab-eba2-4563-bcf0-f2acc797ea97
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 791e9a33a4a2618c5d25ee4e384c06fb6f6c140e
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56880076"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-for-customer"></a>Руководство. Интеграция Azure Active Directory с SAP Cloud for Customer

В этом руководстве описано, как интегрировать SAP Cloud for Customer с Azure Active Directory (Azure AD).
Интеграция SAP Cloud for Customer с Azure AD обеспечивает следующие преимущества:

* C помощью Azure AD вы можете контролировать доступ к приложению SAP Cloud for Customer.
* Вы можете включить автоматический вход пользователей в SAP Cloud for Customer (единый вход) с помощью учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с SAP Cloud for Customer, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* подписка SAP Cloud for Customer с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* SAP Cloud for Customer поддерживает единый вход, инициированный **поставщиком услуг**.

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>Добавление SAP Cloud for Customer из коллекции

Чтобы настроить интеграцию SAP Cloud for Customer с Azure AD, необходимо добавить SAP Cloud for Customer из коллекции в список управляемых приложений SaaS.

**Чтобы добавить SAP Cloud for Customer из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **SAP Cloud for Customer**, выберите **SAP Cloud for Customer** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![SAP Cloud for Customer в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в SAP Cloud for Customer с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в SAP Cloud for Customer.

Чтобы настроить и проверить единый вход Azure AD в SAP Cloud for Customer, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в SAP Cloud for Customer](#configure-sap-cloud-for-customer-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя SAP Cloud for Customer](#create-sap-cloud-for-customer-test-user)** требуется, чтобы в SAP Cloud for Customer существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в SAP Cloud for Customer, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **SAP Cloud for Customer** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    ![Сведения о домене и URL-адресах единого входа для приложения SAP Cloud for Customer](common/sp-identifier.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<server name>.crm.ondemand.com`.

    b. В текстовом поле **Идентификатор (сущности)** введите URL-адрес в следующем формате: `https://<server name>.crm.ondemand.com`.

    > [!NOTE]
    > Эти значения приведены для примера. Необходимо обновить эти значения действующим URL-адресом для входа и идентификатором. Чтобы получить эти значения, обратитесь к [группе поддержки клиентов SAP Cloud for Customer](https://www.sap.com/about/agreements.sap-cloud-services-customers.html). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

5. Приложение SAP Cloud for Customer ожидает утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно в разделе **Атрибуты пользователя** на странице интеграции приложения. На странице **Настройка единого входа с помощью SAML** нажмите кнопку **Изменить**, чтобы открыть диалоговое окно **Атрибуты пользователя**.

    ![изображение](common/edit-attribute.png)

6. В разделе **Атрибуты пользователя** в диалоговом окне **User Attributes & Claims** (Атрибуты пользователя и утверждения) выполните следующие действия.

    a. Щелкните **значок редактирования**, чтобы открыть диалоговое окно **Управление утверждениями пользователя**.

    ![изображение](./media/sap-customer-cloud-tutorial/tutorial_usermail.png)

    ![изображение](./media/sap-customer-cloud-tutorial/tutorial_usermailedit.png)

    b. Выберите **Преобразование** в качестве **источника**.

    c. Из списка **Преобразование** выберите **ExtractMailPrefix()**.

    4.3. В списке **Параметр 1** выберите атрибут пользователя, который вы хотите использовать в своей реализации.
    Например, если в качестве уникального идентификатора пользователя вы хотите использовать EmployeeID и сохранили значение атрибута в ExtensionAttribute2, выберите user.extensionattribute2.

    д. Выберите команду **Сохранить**.

7. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать нужный вам **XML метаданных федерации**, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

8. Скопируйте требуемый URL-адрес из раздела **Настройка SAP Cloud for Customer**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    б) Идентификатор Azure AD.

    в) URL-адрес выхода.

### <a name="configure-sap-cloud-for-customer-single-sign-on"></a>Настройка единого входа SAP Cloud for Customer
   
1. Войдите на портал SAP Cloud for Customer с правами администратора.
   
2. Перейдите к элементу **Application and User Management Common Task** (Общие задачи управления приложением и пользователем) и откройте вкладку **Поставщик удостоверений**.
   
3. Щелкните **New Identity Provider** (Новый поставщик удостоверений) и выберите XML-файл метаданных, скачанный с портала Azure. Импортируя метаданные, система автоматически отправляет требуемые сертификаты подписи и шифрования.
   
    ![Настройка единого входа](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_54.png)
   
4. В запросе SAML для Azure Active Directory требуется URL-адрес службы обработчика утверждений, поэтому установите флажок **Include Assertion Consumer Service URL** (Добавить URL-адрес службы обработчика утверждений).
   
5. Щелкните **Activate Single Sign-On**(Активировать единый вход).
   
6. Сохраните изменения.
   
7. Перейдите на вкладку **My System** (Моя система).
   
    ![Настройка единого входа](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_52.png)
   
8. В текстовое поле **Azure AD Sign On URL** (URL-адрес единого входа Azure AD) вставьте **URL-адрес входа**, скопированный на портале Azure.
   
    ![Настройка единого входа](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_53.png)
   
9. Укажите, может ли сотрудник вручную выбрать вход с помощью учетных данных (идентификатора пользователя и пароля) или единый вход, щелкнув **Manual Identity Provider Selection**(Выбор поставщика удостоверений вручную).
   
10. В разделе **SSO URL** (URL-адрес единого входа) укажите URL-адрес, который будет использоваться вашими сотрудниками для входа в систему. 
    В списке **URL Sent to Employee** (URL-адрес для отправки сотруднику) можно выбрать следующие параметры:
   
    **Non-SSO URL**
   
    Система отправит сотруднику обычный URL-адрес системы. Сотрудник не сможет входить в систему с помощью единого входа. Вместо этого ему необходимо использовать пароль или сертификат.
   
    **SSO URL** 
   
    Система отправит сотруднику URL-адрес единого входа. Сотрудник сможет входить в систему с помощью единого входа. Запрос на проверку подлинности перенаправляется через поставщик удостоверений.
   
    **Automatic Selection**
   
    Если функция единого входа неактивна, система отправит сотруднику обычный URL-адрес системы. Если функция единого входа активна, система проверит, есть ли у сотрудника пароль. При наличии пароля сотруднику будет отправлен URL-адрес единого входа и URL-адрес другого метода входа. Если пароля нет, сотруднику будет отправлен только URL-адрес единого входа.
   
11. Сохраните изменения.

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

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к SAP Cloud for Customer.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **SAP Cloud for Customer**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений введите и выберите **SAP Cloud for Customer**.

    ![Ссылка на SAP Cloud for Customer в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-sap-cloud-for-customer-test-user"></a>Создание тестового пользователя в SAP Cloud for Customer

В этом разделе описано, как создать пользователя Britta Simon в приложении SAP Cloud for Customer. Чтобы добавить пользователей на платформу SAP Cloud for Customer, обратитесь в  [службу поддержки SAP Cloud for Customer](https://www.sap.com/about/agreements.sap-cloud-services-customers.html). Перед использованием единого входа необходимо создать и активировать пользователей.

> [!NOTE]
> Значение идентификатора имени должно совпадать с полем имени пользователя на платформе SAP Cloud for Customer.

### <a name="test-single-sign-on"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент SAP Cloud for Customer на панели доступа, вы автоматически войдете в приложение SAP Cloud for Customer, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

