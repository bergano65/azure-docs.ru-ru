---
title: Руководство по Интеграция Azure Active Directory с Pega Systems | Документация Майкрософт
description: Из этого руководства вы узнаете, как настроить единый вход между Azure Active Directory и Pega Systems.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 31acf80f-1f4b-41f1-956f-a9fbae77ee69
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: bd54466e054055ff84cd5bb2b28c5cc074ac0017
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72026801"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Руководство по Интеграция Azure Active Directory с Pega Systems

В этом руководстве объясняется, как интегрировать приложение Pega Systems с Azure Active Directory (Azure AD).

Такая интеграция обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к Pega Systems.
* Вы можете включить автоматический вход для пользователей (единый вход) в Pega Systems с помощью учетных записей Azure AD.
* Вы можете управлять учетными записями централизованно — через портал Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Pega Systems, вам потребуется:

* подписка Azure AD Если у вас нет среды Azure AD, вы можете получить [пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).
* подписка Pega Systems с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure Active Directory в тестовой среде.

* Pega Systems поддерживает единый вход, инициируемый поставщиком услуг и поставщиком удостоверений.

## <a name="add-pega-systems-from-the-gallery"></a>Добавление Pega Systems из коллекции

Чтобы настроить интеграцию Pega Systems с Azure AD, необходимо добавить Pega Systems из коллекции в список управляемых приложений SaaS.

1. На [портале Azure](https://portal.azure.com) в области слева щелкните **Azure Active Directory**.

    ![Выберите Azure Active Directory.](common/select-azuread.png)

2. Выберите **Корпоративные приложения** > **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, выберите **Новое приложение** в верхней части окна.

    ![Выбор элемента "Новое приложение"](common/add-new-app.png)

4. В поле поиска введите **Pega Systems**. В результатах поиска выберите **Pega Systems**, а затем щелкните **Добавить**.

     ![Результаты поиска](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Pega Systems с использованием тестового пользователя Britta Simon.
Для единого входа необходимо установить связь между пользователем в Azure AD и соответствующим пользователем в Pega Systems.

Чтобы настроить и проверить единый вход Azure AD в Pega Systems, вам потребуется выполнить следующие действия.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в Pega Systems](#configure-pega-systems-single-sign-on)** на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить ему использовать единый вход Azure AD.
5. **[Создание тестового пользователя Pega Systems](#create-a-pega-systems-test-user)** , связанного с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться, что конфигурация работает правильно.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure Active Directory в Pega Systems, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Pega Systems** выберите **Единый вход**.

    ![Выбор пункта "Единый вход"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Выбор метода единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Значок "Изменить"](common/edit-urls.png)

4. Если вы хотите настроить приложение в режиме, инициируемом поставщиком удостоверений, в диалоговом окне **Базовая конфигурация SAML** выполните следующие действия.

    ![Диалоговое окно "Базовая конфигурация SAML"](common/idp-intiated.png)

    1. В поле **Идентификатор** введите URL-адрес в следующем формате:

       `https://<customername>.pegacloud.io:443/prweb/sp/<instanceID>`

    1. В поле **URL-адрес ответа** введите URL-адрес в следующем формате:

       `https://<customername>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. Если вы хотите настроить приложение для работы в режиме, инициируемом поставщиком услуг, выберите **Задать дополнительные URL-адреса** и выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения Pega Systems](common/both-advanced-urls.png)

    1. В поле **URL-адрес для входа** введите URL-адрес для входа.

    1. В поле **Состояние ретранслятора** введите URL-адрес в следующем формате: `https://<customername>.pegacloud.io/prweb/sso`

    > [!NOTE]
    > Указанные здесь значения — это заполнители. Вместо них нужно указать фактический идентификатор, URL-адрес ответа, URL-адрес для входа и URL-адрес состояния ретранслятора. Далее в этом руководстве объясняется, как получить значения идентификатора и URL-адреса ответа в приложении Pega. Чтобы получить значение состояния ретранслятора, обратитесь к [группе поддержки Pega Systems](https://www.pega.com/contact-us). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

6. Приложению Pega Systems требуются утверждения SAML в определенном формате. Чтобы получать их в правильном формате, необходимо добавить сопоставления настраиваемых атрибутов в свою конфигурацию атрибутов токена SAML. На следующем снимке экрана показаны атрибуты по умолчанию. Щелкните значок **Изменить**, чтобы открыть диалоговое окно **Атрибуты пользователя**.

    ![Атрибуты пользователя](common/edit-attribute.png)

7. Помимо атрибутов, показанных на предыдущем снимке экрана, приложению Pega Systems требуется еще несколько атрибутов, возвращаемых в ответе SAML. В разделе **Утверждения пользователя** диалогового окна **Атрибуты пользователя** выполните приведенные ниже действия, чтобы добавить эти атрибуты токена SAML.

    
   - `uid`
   - `cn`
   - `mail`
   - `accessgroup`  
   - `organization`  
   - `orgdivision`
   - `orgunit`
   - `workgroup`  
   - `Phone`

    > [!NOTE]
    > Данные значения зависят от организации. Предоставьте соответствующие значения.

    1. Чтобы открыть диалоговое окно **Управление утверждениями пользователя**, выберите **Добавить новое утверждение**.

    ![Выбор "Добавить новое утверждение"](common/new-save-attribute.png)

    ![Диалоговое окно "Управление утверждениями пользователя"](common/new-attribute-details.png)

    1. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой записи.

    1. Оставьте пустым поле **Пространство имен**.

    1. В поле **Источник** выберите **Атрибут**.

    1. Из списка **Атрибут источника** выберите значение атрибута, отображаемое для этой строки.

    1. Нажмите кнопку **ОК**.

    1. Щелкните **Сохранить**.

8. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните ссылку **Скачать** рядом с пунктом **XML метаданных федерации** и сохраните сертификат на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

9. Требуемые URL-адреса можно скопировать из раздела **Настройка Pega Systems**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    1. **URL-адрес входа**.

    1. **Идентификатор Azure AD**

    1. **URL-адрес выхода**.

### <a name="configure-pega-systems-single-sign-on"></a>Настройка единого входа в Pega Systems

1. Чтобы настроить единый вход на стороне **Pega Systems**, откройте в отдельном окне браузера портал Pega и войдите с учетной записью администратора.

2. Выберите **Create** > **SysAdmin** > **Authentication Service** (Создать > Системный администратор > Служба аутентификации).

    ![Выбор "Authentication Service" (Служба аутентификации)](./media/pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
3. Выполните следующие действия на экране **Create Authentication Service** (Создание службы аутентификации).

    ![Экран "Create Authentication Service" (Создание службы аутентификации)](./media/pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    1. Из раскрывающегося списка **Type** (Тип) выберите **SAML 2.0**.

    1. В поле **Name** (Имя) введите любое имя, например **Azure AD SSO**.

    1. В поле **Short description** (Краткое описание) введите описание.  

    1. Выберите **Create and open** (Создать и открыть).
    
4. В разделе **Identity Provider (IdP) information** (Сведения о поставщике удостоверений) выберите **Import IdP metadata** (Импортировать метаданные поставщика удостоверений) и найдите файл метаданных, который вы скачали с портала Azure. Нажмите кнопку **Submit** (Отправить), чтобы загрузить метаданные.

    ![Раздел "Identity Provider (IdP) information" (Сведения о поставщике удостоверений)](./media/pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
    Данные поставщика удостоверений будут импортированы, как показано ниже.

    ![Импортированные данные поставщика удостоверений](./media/pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
6. В разделе **Service Provider (SP) settings** (Параметры поставщика услуг) сделайте следующее.

    ![Раздел "Service Provider (SP) settings" (Параметры поставщика услуг)](./media/pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    1. Скопируйте значение **Entity Identification** (Идентификатор сущности) и вставьте его в поле **Идентификатор** в разделе **Базовая конфигурация SAML** на портале Azure.

    1. Скопируйте значение **Assertion Consumer Service (ACS) location** (Расположение службы обработчика утверждений (ACS)) и вставьте его в поле **URL-адрес для ответа** в разделе **Базовая конфигурация SAML** на портале Azure.

    1. Выберите **Disable request signing** (Отключить подписывание запросов).

7. Щелкните **Сохранить**.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

В этом разделе описано, как создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Выбор "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Выбор "Новый пользователь"](common/new-user.png)

3. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    a. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon @\<ваш_домен.\<доменная_зона>** . (Например, BrittaSimon@contoso.com).

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе в, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Pega Systems.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **Pega Systems**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. Из списка приложений выберите **Pega Systems**.

    ![Список приложений](common/all-applications.png)

3. В области слева выберите **Пользователи и группы**.

    ![Выбор параметра "Пользователи и группы"](common/users-groups-blade.png)

4. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** — **Пользователи и группы**.

    ![Выбор элемента "Добавить пользователя"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** выберите **Britta Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка. В нижней части экрана нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** выберите **Назначить**.

### <a name="create-a-pega-systems-test-user"></a>Создание тестового пользователя Pega Systems

Далее необходимо создать пользователя с именем Britta Simon в Pega Systems. Обратитесь к [группе поддержки Pega Systems](https://www.pega.com/contact-us) для создания пользователей.

### <a name="test-single-sign-on"></a>Проверка единого входа

Теперь необходимо проверить конфигурацию единого входа Azure AD с помощью Панели доступа.

Щелкнув элемент "Pega Systems" на Панели доступа, вы автоматически войдете в приложение Pega Systems, для которого настроили единый вход. Дополнительные сведения см. в разделе [Доступ и использование приложений на портале "Мои приложения"](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)