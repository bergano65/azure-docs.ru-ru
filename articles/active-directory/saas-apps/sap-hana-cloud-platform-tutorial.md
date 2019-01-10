---
title: Руководство. Интеграция Azure Active Directory с SAP Cloud Platform | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в SAP Cloud Platform.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bd398225-8bd8-4697-9a44-af6e6679113a
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/17/2018
ms.author: jeedes
ms.openlocfilehash: 5ea4c3928c7bb3ee8250fd7b69ff9646a698fa3b
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/28/2018
ms.locfileid: "53809086"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform"></a>Руководство. Интеграция Azure Active Directory с SAP Cloud Platform

В этом руководстве описано, как интегрировать SAP Cloud Platform с Azure Active Directory (Azure AD).
Интеграция Azure AD с SAP Cloud Platform обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к SAP Cloud Platform.
* Вы можете включить автоматический вход пользователей в SAP Cloud Platform (единый вход) с использованием учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с SAP Cloud Platform, вам потребуется:

* подписка Azure AD; Если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).
* Подписка SAP Cloud Platform с поддержкой единого входа

По завершении работы с этим руководством пользователи Azure AD, назначенные SAP Cloud Platform, смогут выполнять единый вход в приложение следуя указаниям в статье [Что такое панель доступа?](../user-help/active-directory-saas-access-panel-introduction.md).

>[!IMPORTANT]
>Для тестирования единого входа необходимо развернуть собственное приложение или оформить подписку на приложение в учетной записи SAP Cloud Platform. В этом учебнике используется развертывание приложения в учетной записи.
> 

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства проводится настройка и проверка единого входа Azure AD в тестовой среде.

* SAP Cloud Platform поддерживает единый вход, инициированный **поставщиком услуг**.

## <a name="adding-sap-cloud-platform-from-the-gallery"></a>Добавление SAP Cloud Platform из коллекции

Чтобы настроить интеграцию SAP Cloud Platform с Azure AD, необходимо добавить SAP Cloud Platform из коллекции в список управляемых приложений SaaS.

**Чтобы добавить SAP Cloud Platform из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"](common/add-new-app.png)

4. В поле поиска введите **SAP Cloud Platform**, выберите **SAP Cloud Platform** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![SAP Cloud Platform в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в SAP Cloud Platform с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в SAP Cloud Platform.

Чтобы настроить и проверить единый вход Azure AD в SAP Cloud Platform, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в SAP Cloud Platform](#configure-sap-cloud-platform-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя SAP Cloud Platform](#create-sap-cloud-platform-test-user)** нужно для того, чтобы в SAP Cloud Platform также существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в SAP Cloud Platform, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **SAP Cloud Platform** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    ![Сведения о домене и URL-адресах единого входа для приложения SAP Cloud Platform](common/sp-identifier-reply.png)

    a. В текстовом поле **URL-адрес входа** введите URL-адрес, который пользователи используют для входа в приложение **Sign On URL**. Это URL-адрес защищенного ресурса для конкретной учетной записи в приложении SAP Cloud Platform. URL-адрес имеет следующий формат: `https://<applicationName><accountName>.<landscape host>.ondemand.com/<path_to_protected_resource>`.
      
     >[!NOTE]
     >Это URL-адрес в приложении SAP Cloud Platform, запрашивающий аутентификацию пользователя.
     > 

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |

    b. В текстовом поле **Идентификатор** укажите URL-адрес SAP Cloud Platform в одном из следующих форматов: 

    | |
    |--|
    | `https://hanatrial.ondemand.com/<instancename>` |
    | `https://hana.ondemand.com/<instancename>` |
    | `https://us1.hana.ondemand.com/<instancename>` |
    | `https://ap1.hana.ondemand.com/<instancename>` |

    c. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате:

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.hana.ondemand.com/<instancename>` |

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Укажите вместо них фактические значения URL-адреса для входа, идентификатора и URL-адреса ответа. Чтобы получить URL-адрес входа и идентификатор, обратитесь к [службе поддержки клиентов SAP Cloud Platform](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/5dd739823b824b539eee47b7860a00be.html). URL-адрес ответа можно получить в разделе управления доверием, который рассматривается далее в этом руководстве.
    > 
4. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать нужный вам **XML метаданных федерации**, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

### <a name="configure-sap-cloud-platform-single-sign-on"></a>Настройка единого входа SAP Cloud Platform

1. В другом окне браузера войдите на панель SAP Cloud Platform по адресу `https://account.<landscape host>.ondemand.com/cockpit` (например, https://account.hanatrial.ondemand.com/cockpit)).

2. Щелкните вкладку **Доверие** .
   
    ![Доверие](./media/sap-hana-cloud-platform-tutorial/ic790800.png "Доверие")

3. В разделе управления доверием на вкладке **Local Service Provider** (Поставщик локальных служб) выполните следующие действия:

    ![Управление доверием](./media/sap-hana-cloud-platform-tutorial/ic793931.png "Управление доверием")
   
    a. Нажмите кнопку **Изменить**.

    b. Для параметра **Configuration Type** (Тип конфигурации) выберите значение **Custom** (Настраиваемая).

    c. В поле **Имя поставщика локальных служб**оставьте значение по умолчанию. Скопируйте это значение и вставьте его в поле **Идентификатор** в конфигурации Azure AD для SAP Cloud Platform.

    d. Чтобы создать пару ключей **Signing Key** (Ключ подписывания) и **Signing Certificate** (Сертификат подписывания), щелкните **Generate Key Pair** (Создать пару ключей).

    д. Для параметра **Principal Propagation** (Распространение субъектов) выберите значение **Disabled** (Отключено).

    Е. Для параметра **Force Authentication** (Принудительная аутентификация) выберите значение **Disabled** (Отключено).

    ж. Выберите команду **Сохранить**.

4. После сохранения параметров на вкладке **Local Service Provider** (Поставщик локальных служб) выполните следующие действия, чтобы получить URL-адрес ответа:
   
    ![Получение метаданных](./media/sap-hana-cloud-platform-tutorial/ic793930.png "Получение метаданных")

    a. Скачайте файл метаданных SAP Cloud Platform, щелкнув ссылку **Get Metadata** (Получить метаданные).

    b. Откройте скачанный XML-файл метаданных SAP Cloud Platform, а затем найдите тег **ns3:AssertionConsumerService**.
 
    c. Скопируйте значение атрибута **Location** и вставьте его в текстовое поле **URL-адрес ответа** в конфигурации Azure AD для SAP Cloud Platform.

5. Откройте вкладку **Trusted Identity Provider** (Доверенный поставщик удостоверений) и щелкните **Add Trusted Identity Provider** (Добавить доверенный поставщик удостоверений).
   
    ![Управление доверием](./media/sap-hana-cloud-platform-tutorial/ic790802.png "Управление доверием")
   
    >[!NOTE]
    >Для управления списком доверенных поставщиков удостоверений необходимо выбрать тип конфигурации «Настраиваемая» в разделе «Поставщик локальных служб». В качестве типа конфигурации по умолчанию используется неизменяемое неявное доверие к службе SAP ID Service. Для значения «Нет» параметры доверия отсутствуют.
    > 
    > 

6. Откройте вкладку **General** (Общие) и нажмите кнопку **Browse** (Обзор), чтобы передать скачанный файл метаданных.
    
    ![Управление доверием](./media/sap-hana-cloud-platform-tutorial/ic793932.png "Управление доверием")
    
    >[!NOTE]
    >После передачи файла метаданных поля **Single Sign-on URL** (URL-адрес единого входа), **Single Logout URL** (URL-адрес единого выхода) и **Signing Certificate** (Сертификат для подписи) заполняются автоматически.
    > 
     
7. Перейдите на вкладку **Атрибуты** .

8. На вкладке **Атрибуты** сделайте следующее:
    
    ![Атрибуты](./media/sap-hana-cloud-platform-tutorial/ic790804.png "Атрибуты") 

    a. Щелкните **Add Assertion-Based Attribute** (Добавить атрибут на основе утверждений) и добавьте следующие атрибуты на основе утверждений:
       
    | Атрибут утверждения | Атрибут субъекта |
    | --- | --- |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |firstname |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |lastname |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |email |
   
     >[!NOTE]
     >Конфигурация атрибутов зависит от способа разработки приложений в SCP, т. е. какие атрибуты предполагается использовать в ответе SAML и какое имя (атрибут субъекта) используется для доступа к этому атрибуту в коде.
     > 
    
    b. **Атрибут по умолчанию** на снимке экрана предоставляется только для примера. Он не требуется для того, чтобы сценарий работал.  
 
    c. Имена и значения для **атрибута субъекта** , показанные на снимке экрана, зависят от способа разработки приложения. Возможно, приложению требуются другие сопоставления.

### <a name="assertion-based-groups"></a>Группы на основе утверждений

При необходимости для поставщика удостоверений Azure Active Directory можно настроить группы на основе утверждений.

Использование групп на SAP Cloud Platform позволяет динамически назначать одного или нескольких пользователей одной или нескольким ролям в приложениях SAP Cloud Platform, которые определяются по значениям атрибутов в утверждении SAML 2.0. 

Например, если утверждение содержит атрибут *contract=temporary*, возможно, вам потребуется добавить всех затронутых пользователей в группу *TEMPORARY*. Группа *TEMPORARY* может содержать одну или несколько ролей из одного или нескольких приложений, развернутых в учетной записи SAP Cloud Platform.
 
Используйте группы на основе утверждений, чтобы одновременно назначить большое число пользователей для одной или нескольких ролей приложений в учетной записи SAP Cloud Platform. Если требуется назначить одного или небольшое число пользователей конкретным ролям, мы советуем назначить их непосредственно на вкладке **Authorizations** (Разрешения) панели SAP Cloud Platform.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD 

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее.

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    a. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon@yourcompanydomain.extension**.  
    Например, BrittaSimon@contoso.com

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к SAP Cloud Platform.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **SAP Cloud Platform**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений введите и выберите **SAP Cloud Platform**.

    ![Ссылка на SAP Cloud Platform в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-sap-cloud-platform-test-user"></a>Создание тестового пользователя SAP Cloud Platform

Чтобы разрешить пользователям Azure AD входить в SAP Cloud Platform, им необходимо назначить роли в SAP Cloud Platform.

**Чтобы назначить роль пользователю, сделайте следующее:**

1. Войдите на панель **SAP Cloud Platform**.

2. Выполните следующие действия.
   
    ![Авторизации](./media/sap-hana-cloud-platform-tutorial/ic790805.png "Авторизации")
   
    a. Щелкните **Авторизация**.

    b. Откройте вкладку **Пользователи** .

    c. В тестовом поле **Пользователь** введите электронный адрес пользователя.

    d. Щелкните **Назначить** , чтобы назначить роль пользователю.

    д. Выберите команду **Сохранить**.

### <a name="test-single-sign-on"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент SAP Cloud Platform на панели доступа, вы автоматически войдете в приложение SAP Cloud Platform, для которого настроили единый вход. Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

