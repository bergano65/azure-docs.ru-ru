---
title: Руководство по Интеграция Azure Active Directory с Meta Networks Connector | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Meta Networks Connector.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4ae5f30d-113b-4261-b474-47ffbac08bf7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: ae0b8bb6dec4b129a4965426789819e119a25c53
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991504"
---
# <a name="tutorial-azure-active-directory-integration-with-meta-networks-connector"></a>Руководство по Интеграция Azure Active Directory с Meta Networks Connector

В этом руководстве описано, как интегрировать Meta Networks Connector с Azure Active Directory (Azure AD).
Интеграция Meta Networks Connector с Azure AD обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к Meta Networks Connector.
* Вы можете включить автоматический вход пользователей в Meta Networks Connector (единый вход) с учетными записями Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Meta Networks Connector, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* подписка Meta Networks Connector с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Meta Networks Connector поддерживает инициированный единый вход **пакета обновлений** и **выдающей точки распространения**.
 
* Meta Networks Connector поддерживает **JIT**-подготовку пользователей.

## <a name="adding-meta-networks-connector-from-the-gallery"></a>Добавление Meta Networks Connector из коллекции

Чтобы настроить интеграцию Meta Networks Connector с Azure AD, нужно добавить Meta Networks Connector из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Meta Networks Connector из коллекции, сделайте следующее.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Meta Networks Connector**, выберите **Meta Networks Connector** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![Meta Networks Connector в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в приложение Meta Networks Connector с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Meta Networks Connector.

Чтобы настроить и проверить единый вход Azure AD в Meta Networks Connector, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в Meta Networks Connector](#configure-meta-networks-connector-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя Meta Networks Connector](#create-meta-networks-connector-test-user)** требуется для того, чтобы в Meta Networks Connector существовал пользователь Britta Simon, связанный с представлением этого же пользователя в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в Meta Networks Connector, выполните следующие действия:

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Meta Networks Connector** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. Если вы хотите настроить приложение в режиме, инициируемом **поставщиком удостоверений**, в разделе **Базовая конфигурация SAML** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения Meta Networks Connector](common/idp-intiated.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в формате `https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/saml/metadata`.

    б) В текстовом поле **URL-адрес ответа** введите URL-адрес в формате `https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/sso/saml`.

5. Чтобы настроить приложение для работы в режиме, инициируемом **поставщиком услуг**, щелкните **Задать дополнительные URL-адреса** и выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения Meta Networks Connector](common/both-advanced-urls.png)

    a. В текстовое поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/login`.

    b. В текстовом поле **Состояние ретранслятора** введите URL-адрес в формате `https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/#/`.

    > [!NOTE]
    > Эти значения приведены для примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа, которые описываются далее в этом руководстве.

6. Приложение Meta Networks Connector ожидает проверочные утверждения SAML в определенном формате, который требует добавить настраиваемые сопоставления атрибутов в вашу конфигурацию атрибутов токена SAML. На следующем снимке экрана показан список атрибутов по умолчанию. Щелкните значок **Изменить**, чтобы открыть диалоговое окно  **Атрибуты пользователя** .

    ![image](common/edit-attribute.png)
    
7. В дополнение к описанному выше приложение Meta Networks Connector ожидает несколько дополнительных атрибутов в ответе SAML. В разделе **Утверждения пользователя** диалогового окна **Атрибуты пользователя** выполните следующие действия, чтобы добавить атрибут токена SAML, как показано в приведенной ниже таблице.
    
    | ИМЯ | Атрибут источника | Пространство имен|
    | ---------------| --------------- | -------- |
    | firstname | user.givenname | |
    | lastname | user.surname | |
    | emailaddress| user.mail| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | name | user.userprincipalname| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | phone | user.telephonenumber | |

    a. Щелкните **Добавить новое утверждение**, чтобы открыть диалоговое окно **Управление утверждениями пользователя**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.

    c. Оставьте пустым поле **Пространство имен**.

    d. В качестве источника выберите **Атрибут**.

    д. В списке **Атрибут источника** введите значение атрибута, отображаемое для этой строки.

    Е. Нажмите кнопку **ОК**.

    ж. Выберите команду **Сохранить**.

8. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Загрузить**, чтобы загрузить требуемый **сертификат (Base64)** из предложенных вариантов, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

9. Скопируйте требуемый URL-адрес из раздела **Настройка Meta Networks Connector**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    b. Идентификатор Azure AD

    c. URL-адрес выхода.

### <a name="configure-meta-networks-connector-single-sign-on"></a>Настройка единого входа в Meta Networks Connector

1. Откройте новую вкладку в браузере и войдите в учетную запись администратора Meta Networks Connector.
    
    > [!NOTE]
    > Meta Networks Connector является безопасной системой. Поэтому перед переходом на портал этого приложения необходимо, чтобы ваш общедоступный IP-адрес был добавлен в список разрешений на его стороне. Чтобы получить свой общедоступный IP-адрес, перейдите по ссылке, указанной [здесь](https://whatismyipaddress.com/). Отправьте свой IP-адрес [группе поддержки клиентов Meta Networks Connector](mailto:support@metanetworks.com), чтобы он был добавлен в список разрешений.
    
2. Выберите **Administrator** (Администратор) и щелкните **Settings** (Параметры).
    
    ![Настройка единого входа](./media/metanetworksconnector-tutorial/configure3.png)
    
3. Убедитесь, что параметры **Log Internet Traffic** (Ведение журнала трафика Интернета) и **Force VPN MFA** (Принудительная многофакторная проверка подлинности через VPN) отключены.
    
    ![Настройка единого входа](./media/metanetworksconnector-tutorial/configure1.png)
    
4. Выберите **Administrator** (Администратор) и щелкните **SAML**.
    
    ![Настройка единого входа](./media/metanetworksconnector-tutorial/configure4.png)
    
5. Выполните следующие действия на странице **DETAILS** (Сведения).
    
    ![Настройка единого входа](./media/metanetworksconnector-tutorial/configure2.png)
    
    a. Скопируйте значение **SSO URL** (URL-адрес единого входа) и вставьте его в текстовое поле **URL-адрес входа** в разделе **Домены и URL-адреса приложения Meta Networks Connector**.
    
    b. Скопируйте значение **Recipient URL** (URL-адрес получателя) и вставьте его в текстовое поле **URL-адрес ответа** в разделе **Домены и URL-адреса приложения Meta Networks Connector**.
    
    c. Скопируйте значение **Audience URI (SP Entity ID)** (URI аудитории (Идентификатор сущности поставщика услуг)) и вставьте его в текстовое поле **Идентификатор (сущности)** в разделе **Домены и URL-адреса приложения Meta Networks Connector**.
    
    d. Включение SAML
    
6. На вкладке **GENERAL** (Общие) выполните следующее.

    ![Настройка единого входа](./media/metanetworksconnector-tutorial/configure5.png)

    a. В поле **URL-адрес единого входа для поставщика удостоверений** вставьте значение **URL-адреса входа**, скопированное на портале Azure.

    b. В поле **Издатель поставщика удостоверений** вставьте значение **идентификатора Azure AD**, скопированное на портале Azure.

    c. Откройте в Блокноте сертификат, скачанный с портала Azure, и вставьте его в текстовое поле **Сертификат X.509**.

    d. Включите **JIT-подготовку**.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD 

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    a. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon\@домен_вашей_компании.доменная_зона**.  
    Например BrittaSimon@contoso.com.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Meta Networks Connector.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **Meta Networks Connector**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. Из списка приложений выберите **Meta Networks Connector**.

    ![Ссылка на Meta Networks Connector в списке "Приложения"](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-meta-networks-connector-test-user"></a>Создание тестового пользователя Meta Networks Connector

В этом разделе вы создадите в Meta Networks Connector пользователя Britta Simon. Приложение Meta Networks Connector поддерживает JIT-подготовку. Эта функция включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Если пользователь еще не существует в Meta Networks Connector, он создается при попытке доступа к приложению Meta Networks Connector.

>[!Note]
>Чтобы создать пользователя вручную, обратитесь к [группе поддержки клиентов Meta Networks Connector](mailto:support@metanetworks.com).

### <a name="test-single-sign-on"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку "Meta Networks Connector" на панели доступа, вы автоматически войдете в приложение Meta Networks Connector, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

