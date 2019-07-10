---
title: Руководство по Интеграция Azure Active Directory с Cisco Webex Meetings | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Cisco Webex Meetings.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec8c2fc2c2dbbfd2c98184215f696aaf18a87193
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67105547"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex-meetings"></a>Руководство по Интеграция Azure Active Directory с Cisco Webex Meetings

В этом учебнике вы узнаете, как интегрировать Cisco Webex Meetings с Azure Active Directory (Azure AD).
Интеграция Azure AD с Cisco Webex Meetings обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к Cisco Webex Meetings.
* Вы можете включить автоматический вход пользователей (единый вход) в Cisco Webex Meetings с помощью учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Cisco Webex Meetings, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* подписка Cisco Webex Meetings с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Cisco Webex Meetings поддерживает единый вход, инициированный **поставщиком услуг**.

* Cisco Webex Meetings поддерживает **JIT**-подготовку пользователей.

## <a name="adding-cisco-webex-meetings-from-the-gallery"></a>Добавление Cisco Webex Meetings из коллекции

Чтобы настроить интеграцию Cisco Webex Meetings с Azure AD, необходимо добавить Cisco Webex Meetings из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Cisco Webex Meetings из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Cisco Webex Meetings**, выберите **Cisco Webex Meetings** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![Cisco Webex Meetings в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Cisco Webex Meetings с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем Cisco Webex Meetings.

Чтобы настроить и проверить единый вход Azure AD в Cisco Webex Meetings, необходимо выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в Cisco Webex Meetings](#configure-cisco-webex-meetings-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя Cisco Webex Meetings](#create-cisco-webex-meetings-test-user)** нужно для того, чтобы в Cisco Webex Meetings существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в Cisco Webex Meetings, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции приложения **Cisco Webex Meetings** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На портале Azure на странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. В разделе **Базовая конфигурация SAML** отправьте скачанный **файл метаданных поставщика службы** и настройте приложение, выполнив следующие действия:

    >[!Note]
    >Вы получите файл метаданных поставщика услуг, описанный далее в разделе **Настройка единого входа в Cisco Webex Meetings**. 

    a. Щелкните **Отправить файл метаданных**.

    ![Передача файла метаданных](common/upload-metadata.png)

    b. Щелкните **значок папки**, выберите файл метаданных и нажмите кнопку **Отправить**.

    ![Выбор файла метаданных](common/browse-upload-metadata.png)

    c. После успешной отправки файла метаданных поставщика службы значения **идентификатора** и **URL-адреса ответа** автоматически добавятся в текстовое поле раздела **Базовая конфигурация SAML**.

    ![Сведения о домене и URL-адресах единого входа для приложения Cisco Webex Meetings](common/sp-identifier-reply.png)

    В текстовое поле **URL-адрес входа** вставьте значение **URL-адреса ответа**, которое автоматически заполняется при отправке файла метаданных поставщика услуг.

5. Для приложения Cisco Webex Meetings требуются проверочные утверждения SAML в определенном формате, поэтому следует добавить настраиваемые сопоставления атрибутов в конфигурацию атрибутов токена SAML. На следующем снимке экрана показан список атрибутов по умолчанию. Щелкните значок  **Изменить** , чтобы добавить атрибуты.

    ![image](common/edit-attribute.png)

6. Удалите атрибуты по умолчанию из раздела **Утверждения пользователя**. Для приложения Cisco Webex Meetings требуется передать еще несколько атрибутов в ответе SAML. В разделе **Утверждения пользователя** диалогового окна **Атрибуты пользователя** выполните следующие действия, чтобы добавить атрибут токена SAML, как показано в приведенной ниже таблице.
    
    | ИМЯ | Исходный атрибут|
    | ---------------|  --------- |
    |   firstname    | user.givenname |
    |   lastname    | user.surname |
    |   email       | user.mail |
    |   uid    | user.mail |

    a. Щелкните **Добавить новое утверждение**, чтобы открыть диалоговое окно **Управление утверждениями пользователя**.

    ![image](./media/cisco-webex-tutorial/tutorial-cisco-webex-addnewclaim.png)

    ![image](common/new-attribute-details.png)

    b. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.

    c. Оставьте пустым поле **Пространство имен**.

    d. В качестве источника выберите **Атрибут**.

    д. В списке **Атрибут источника** введите значение атрибута, отображаемое для этой строки.

    Е. Нажмите кнопку **ОК**.

    ж. Выберите команду **Сохранить**.

7. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать нужный вам **XML метаданных федерации**, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

8. Скопируйте требуемый URL-адрес из раздела **Настройка Cisco Webex Meetings**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    b. Идентификатор Azure AD

    c. URL-адрес выхода.

### <a name="configure-cisco-webex-meetings-single-sign-on"></a>Настройка единого входа в Cisco Webex Meetings

1. Войдите в [службу Cisco для управления совместной работой в облаке](https://www.webex.com/go/connectadmin), указав учетные данные администратора.

2. Выберите **Security Settings** (Параметры безопасности), а затем — **Federated Web SSO Configuration** (Настройка федеративного единого входа в Интернете).
 
    ![Настройка единого входа](./media/cisco-webex-tutorial/tutorial-cisco-webex-10.png)

3. В разделе **Federated Web SSO Configuration** (Настройка федеративного единого входа в Интернете) выполните следующие действия.

    ![Настройка единого входа](./media/cisco-webex-tutorial/tutorial-cisco-webex-11.png)

    a. В текстовом поле Federation Protocol (Протокол федерации) введите имя протокола.

    b. Щелкните ссылку **Import SAML Metadata** (Импортировать метаданные SAML), чтобы передать файл метаданных, скачанный с портала Azure.

    c. Нажмите кнопку **Export** (Экспорт), чтобы скачать файл метаданных поставщика услуг, и отправьте его в раздел **Базовая конфигурация SAML** на портале Azure.

    d. В текстовом поле **AuthContextClassRef** введите `urn:oasis:names:tc:SAML:2.0:ac:classes:unspecified` и, если вы хотите включить многофакторную проверку подлинности с помощью Azure AD, введите два значения, например `urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport;urn:oasis:names:tc:SAML:2.0:ac:classes:X509`.

    д. Щелкните **Auto Account Creation** (Автоматическое создание учетной записи).

    >[!NOTE]
    >Для включения **JIT**-подготовки пользователей нужно установить флажок **Auto Account Creation** (Автоматическое создание учетной записи). Кроме того, нужно передать атрибуты токена SAML в ответе SAML.

    Е. Выберите команду **Сохранить**. 

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

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив доступ к Cisco Webex Meetings.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **Cisco Webex Meetings**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Cisco Webex Meetings**.

    ![Ссылка на Cisco Webex Meetings в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-cisco-webex-meetings-test-user"></a>Создание тестового пользователя в Cisco Webex Meetings

Цель этого раздела — создать пользователя с именем Britta Simon в Cisco Webex Meetings. Приложение Cisco Webex Meetings поддерживает **JIT**-подготовку. Эта функция включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Если пользователь еще не существует в Cisco Webex Meetings, он создается при попытке доступа к Cisco Webex Meetings.

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Cisco Webex Meetings на Панели доступа, вы автоматически войдете в приложение Cisco Webex Meetings, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что такое условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

