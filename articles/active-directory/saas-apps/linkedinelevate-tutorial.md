---
title: Руководство по Интеграция Azure Active Directory с LinkedIn Elevate | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и LinkedIn Elevate.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2ad9941b-c574-42c3-bd0f-5d6ec68537ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 49e5b6f52b92889ccc7c46f091ea1b90d43b0307
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64704799"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-elevate"></a>Руководство по Интеграция Azure Active Directory с LinkedIn Elevate

В этом руководстве описано, как интегрировать приложение LinkedIn Elevate с Azure Active Directory (Azure AD).
Интеграция Azure AD с LinkedIn Elevate обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать, у кого есть доступ к приложению LinkedIn Elevate.
* Вы можете включить автоматический вход пользователей в LinkedIn Elevate (единый вход) с использованием учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с LinkedIn Elevate, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/));
* подписка LinkedIn Elevate с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* LinkedIn Elevate поддерживает единый вход, инициированный **поставщиком услуг и поставщиком удостоверений**.

* LinkedIn Elevate поддерживает **JIT**-подготовку пользователей.

* LinkedIn Elevate поддерживает [**автоматическую** подготовку пользователей](linkedinelevate-provisioning-tutorial.md).

## <a name="adding-linkedin-elevate-from-the-gallery"></a>Добавление LinkedIn Elevate из коллекции

Чтобы настроить интеграцию приложения LinkedIn Elevate с Azure AD, вам нужно добавить это приложение из коллекции в свой список управляемых приложений SaaS.

**Добавление приложения LinkedIn Elevate из коллекции**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **LinkedIn Elevate**, выберите **LinkedIn Elevate** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![LinkedIn Elevate в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в приложение LinkedIn Elevate с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем LinkedIn Elevate.

Чтобы настроить и проверить единый вход Azure AD в LinkedIn Elevate, выполните следующие действия:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в LinkedIn Elevate](#configure-linkedin-elevate-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя LinkedIn Elevate](#create-linkedin-elevate-test-user)** требуется для того, чтобы в LinkedIn Elevate существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в LinkedIn Elevate, сделайте следующее:

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **LinkedIn Elevate** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. Если вы хотите настроить приложение в режиме, инициируемом **поставщиком удостоверений**, в разделе **Базовая конфигурация SAML** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения LinkedIn Elevate](common/idp-intiated.png)

    a. В текстовом поле **Идентификатор** введите значение **идентификатора сущности**. Далее в этом учебнике описано, как скопировать его на портале LinkedIn.

    b. В текстовом поле **URL-адрес ответа** введите значение **URL-адреса службы обработчика утверждений (ACS)** . Далее в этом учебнике описано, как скопировать это значение URL-адреса на портале LinkedIn.

5. Чтобы настроить приложение для работы в режиме, инициируемом **поставщиком услуг**, щелкните **Задать дополнительные URL-адреса** и выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения LinkedIn Elevate](common/metadata-upload-additional-signon.png)

    В текстовом поле **URL-адрес входа** введите URL-адрес в формате `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=elevate&applicationInstanceId=<InstanceId>`.

6. Приложение LinkedIn Elevate ожидает утверждения SAML в определенном формате, а для этого вам нужно добавить настраиваемые сопоставления атрибутов в конфигурацию атрибутов токена SAML. На следующем снимке экрана показан список атрибутов по умолчанию, когда  **nameidentifier**  сопоставляется с  **user.userprincipalname**. Приложение LinkedIn Elevate ожидает сопоставления nameidentifier с  **user.mail**, поэтому необходимо изменить сопоставление атрибутов, щелкнув значок "Изменить" и изменив сопоставление атрибутов.

    ![image](common/edit-attribute.png)

7. В дополнение к описанному выше приложение LinkedIn Elevate ожидает несколько дополнительных атрибутов в ответе SAML. В разделе "Утверждения пользователя" диалогового окна **Атрибуты пользователя** выполните следующие действия, чтобы добавить атрибут токена SAML, как показано в приведенной ниже таблице.

    | ИМЯ | Исходный атрибут|
    | -------| -------------|
    | department | user.department |

    a. Щелкните **Добавить новое утверждение**, чтобы открыть диалоговое окно **Управление утверждениями пользователя**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.

    c. Оставьте пустым поле **Пространство имен**.

    d. В качестве источника выберите **Атрибут**.

    д. В списке **Атрибут источника** введите значение атрибута, отображаемое для этой строки.

    Е. Нажмите кнопку **ОК**.

    ж. Выберите команду **Сохранить**.

8. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать нужный вам **XML метаданных федерации**, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

9. Требуемый URL-адрес можно скопировать из раздела **Настройка LinkedIn Elevate**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    b. Идентификатор Azure AD

    c. URL-адрес выхода.

### <a name="configure-linkedin-elevate-single-sign-on"></a>Настройка единого входа в LinkedIn Elevate

1. В другом окне веб-браузера войдите в свой клиент LinkedIn Elevate с правами администратора.

1. В **Account Center** (Центр учетных записей) в разделе **Settings** (Параметры) щелкните **Global Settings** (Глобальные параметры). Кроме того, выберите **Elevate — Elevate AAD Test** (Проверка Azure AD для Elevate) из раскрывающегося списка.

    ![Настройка единого входа](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_01.png)

1. Щелкните **OR Click Here to load and copy individual fields from the form** (Или щелкните здесь, чтобы загрузить и скопировать отдельные поля из формы) и сделайте следующее:

    ![Настройка единого входа](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_03.png)

    a. Скопируйте **идентификатор сущности** и вставьте его в текстовое поле **Идентификатор** в разделе **Базовая конфигурация SAML** на портале Azure.

    b. Скопируйте **URL-адрес службы обработчика утверждений (ACS)** и вставьте его в текстовое поле **URL-адрес ответа** в разделе **Базовая конфигурация SAML** на портале Azure.

1. Перейдите в раздел **LinkedIn Admin Settings** (Параметры администратора LinkedIn). Передайте XML-файл, скачанный с портала Azure. Для этого нажмите кнопку Upload XML file (Передать XML-файл).

    ![Настройка единого входа](./media/linkedinelevate-tutorial/tutorial_linkedin_metadata_03.png)

1. Нажмите кнопку **On** (Включить), чтобы включить единый вход. Состояние единого входа изменится с **Not Connected** (Не подключено) на **Connected** (Подключено.)

    ![Настройка единого входа](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    а. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите `brittasimon@yourcompanydomain.extension`. Например BrittaSimon@contoso.com.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как предоставить пользователю Britta Simon доступ к LinkedIn Elevate, чтобы он мог использовать единый вход Azure.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **LinkedIn Elevate**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **LinkedIn Elevate**.

    ![Ссылка LinkedIn Elevate в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-linkedin-elevate-test-user"></a>Создание тестового пользователя LinkedIn Elevate

Приложение LinkedIn Elevate поддерживает JIT-подготовку пользователей, поэтому после аутентификации пользователи будут созданы в приложении автоматически. На странице параметров администратора на портале LinkedIn Elevate включите параметр **Automatically assign licenses** (Автоматически назначать лицензии), чтобы активировать JIT-подготовку, одновременно с которой пользователю будет сразу назначена лицензия. LinkedIn Elevate также поддерживает автоматическую подготовку пользователей. Дополнительные сведения о настройке автоматической подготовки пользователей можно найти [здесь](linkedinelevate-provisioning-tutorial.md).

   ![Создание тестового пользователя Azure AD](./media/linkedinelevate-tutorial/LinkedinUserprovswitch.png)

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку LinkedIn Elevate на Панели доступа, вы автоматически войдете в приложение LinkedIn Elevate, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)