---
title: Руководство. Интеграция Azure Active Directory с LinkedIn Learning | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и LinkedIn Learning.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d5857070-bf79-4bd3-9a2a-4c1919a74946
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/18/2018
ms.author: jeedes
ms.openlocfilehash: e8d829c4990aa798ce77ecc9caae51ace4c53023
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/28/2018
ms.locfileid: "53808049"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-learning"></a>Руководство. Интеграция Azure Active Directory с LinkedIn Learning

В этом руководстве описано, как интегрировать приложение LinkedIn Learning с Azure Active Directory (Azure AD).
Интеграция Azure AD с LinkedIn Learning обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к LinkedIn Learning.
* Вы можете включить автоматический вход пользователей в LinkedIn Learning (единый вход) с использованием учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с LinkedIn Learning, вам потребуется:

* подписка Azure AD; Если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).
* Подписка LinkedIn Learning с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства проводится настройка и проверка единого входа Azure AD в тестовой среде.

* LinkedIn Learning поддерживает единый вход, инициированный **поставщиком услуг и поставщиком удостоверений**.
* LinkedIn Learning поддерживает **JIT**-подготовку пользователей.

## <a name="adding-linkedin-learning-from-the-gallery"></a>Добавление LinkedIn Learning из коллекции

Чтобы настроить интеграцию LinkedIn Learning с Azure AD, необходимо добавить LinkedIn Learning из коллекции в список управляемых приложений SaaS.

**Чтобы добавить LinkedIn Learning из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"](common/add-new-app.png)

4. В поле поиска введите **LinkedIn Learning**, выберите **LinkedIn Learning** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![LinkedIn Learning в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в приложение LinkedIn Learning с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure Active Directory и соответствующим пользователем LinkedIn Learning.

Чтобы настроить и проверить единый вход Azure AD в LinkedIn Learning, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в LinkedIn Learning](#configure-linkedin-learning-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя LinkedIn Learning](#create-linkedin-learning-test-user)** требуется для того, чтобы в LinkedIn Learning существовал пользователь Britta Simon, связанный с представлением этого же пользователя в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в LinkedIn Learning, выполните следующие действия:

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **LinkedIn Learning** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. Если вы хотите настроить приложение в режиме, инициируемом **поставщиком удостоверений**, в разделе **Базовая конфигурация SAML** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения LinkedIn Learning](common/idp-intiated.png)

    a. В текстовом поле **Идентификатор** введите **идентификатор сущности**, скопированный с портала LinkedIn. 

    b. В текстовом поле **URL-адрес ответа** введите **URL-адрес службы обработчика утверждений (ACS)**, скопированный с портала LinkedIn.

    c. Если вы хотите настроить для приложения режим, **инициированный поставщиком услуг**, тогда выберите вариант **Задать дополнительные URL-адреса** в разделе **Базовая конфигурация SAML**, где вы будете указывать URL-адрес для входа. Чтобы создать URL-адреса входа, скопируйте **URL-адрес службы обработчика утверждений (ACS)** и замените /saml/ на /login/. После этого URL-адрес входа должен иметь следующий вид.

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=learning&applicationInstanceId=<InstanceId>`

    ![Сведения о домене и URL-адресах единого входа для приложения LinkedIn Learning](common/metadata-upload-additional-signon.png)

    > [!NOTE]
    > Эти значения приведены в качестве примера. Замените их фактическими значениями идентификатора и URL-адреса ответа, которые описываются далее в разделе **Настройка единого входа в LinkedIn Learning**.

5. Приложение LinkedIn Learning ожидает утверждения SAML в определенном формате, который требует добавить настраиваемые сопоставления атрибутов в конфигурацию атрибутов токена SAML. На следующем снимке экрана приведен пример. По умолчанию **уникальный идентификатор пользователя** имеет значение **user.userprincipalname**, но для LinkedIn Learning требуется сопоставить это значение с адресом электронной почты пользователя. Для этого можно использовать атрибут **user.mail** из списка или соответствующее значение атрибута, основанное на конфигурации организации. 

    ![изображение](common/edit-attribute.png)

6. В разделе **Утверждения пользователя** диалогового окна **Атрибуты пользователя** измените утверждения, щелкнув **значок изменения**, или добавьте утверждение, нажав кнопку **Добавить новое утверждение**, чтобы настроить атрибут токена SAML, как показано на рисунке выше, и выполните следующие действия.
    
    | ИМЯ | Исходный атрибут |
    | ---------------| --------------- |
    | email  | user.mail  |
    | department  | user.department  |
    | firstname  | user.givenname  |
    | lastname  | user.surname  |

    a. Щелкните **Добавить новое утверждение**, чтобы открыть диалоговое окно **Управление утверждениями пользователя**.

    ![изображение](./media/linkedinlearning-tutorial/tutorial_usermail.png)

    ![изображение](./media/linkedinlearning-tutorial/tutorial_usermailedit.png)

    b. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.

    c. Оставьте пустым поле **Пространство имен**.

    d. В качестве источника выберите **Атрибут**.

    д. В списке **Атрибут источника** введите значение атрибута, отображаемое для этой строки.

    Е. Нажмите кнопку **ОК**.

    ж. Выберите команду **Сохранить**.

7. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать нужный вам **XML метаданных федерации**, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

### <a name="configure-linkedin-learning-single-sign-on"></a>Настройка единого входа в LinkedIn Learning

1. В другом окне веб-браузера войдите в свой клиент LinkedIn Learning с правами администратора.

2. В **Account Center** (Центр учетных записей) в разделе **Settings** (Параметры) щелкните **Global Settings** (Глобальные параметры). Также выберите **Learning - Default** в раскрывающемся списке.

    ![Настройка единого входа](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_01.png)

3. Щелкните **OR Click Here to load and copy individual fields from the form** (Или щелкните здесь, чтобы загрузить и скопировать отдельные поля из формы) и скопируйте значения **идентификатора сущности** и **URL-адреса службы обработчика утверждений (ACS)**, и вставьте их в раздел **Базовая конфигурация SAML** на портале Azure.

    ![Настройка единого входа](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_03.png)

4. Перейдите в раздел **LinkedIn Admin Settings** (Параметры администратора LinkedIn). Передайте XML-файл, скачанный с портала Azure. Для этого нажмите кнопку **Upload XML file** (Передать XML-файл).

    ![Настройка единого входа](./media/linkedinlearning-tutorial/tutorial_linkedin_metadata_03.png)

5. Нажмите кнопку **On** (Включить), чтобы включить единый вход. Состояние единого входа изменяется с **Not Connected** (Не подключено) на **Connected** (Подключено.)

    ![Настройка единого входа](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_05.png)

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

В этом разделе описано, как предоставить пользователю Britta Simon доступ к LinkedIn Learning, чтобы он мог использовать единый вход Azure.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **LinkedIn Learning**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений введите и выберите **LinkedIn Learning**.

    ![Ссылка LinkedIn Learning в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-linkedin-learning-test-user"></a>Создание тестового пользователя LinkedIn Learning

Приложение LinkedIn Learning поддерживает JIT-подготовку пользователей, поэтому после аутентификации пользователи будут созданы в приложении автоматически. На странице параметров администратора на портале LinkedIn Learning включите параметр **Automatically assign licenses** (Автоматически назначать лицензии), чтобы активировать JIT-подготовку. При этом пользователю будет также назначена лицензия.

   ![Создание тестового пользователя Azure AD](./media/linkedinlearning-tutorial/LinkedinUserprovswitch.png)

### <a name="test-single-sign-on"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку LinkedIn Learning на панели доступа, вы автоматически войдете в приложение LinkedIn Learning, для которого настроили единый вход. Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

