---
title: Руководство по интеграции Azure Active Directory с SAP Fiori | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в SAP Fiori.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 77ad13bf-e56b-4063-97d0-c82a19da9d56
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: jeedes
ms.openlocfilehash: 897685eb967e03cfd30182eec6b237e27386496c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67092149"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-fiori"></a>Руководство по интеграции Azure Active Directory с SAP Fiori

В этом руководстве описано, как интегрировать SAP Fiori с Azure Active Directory (Azure AD).

Интеграция SAP Fiori с Azure AD обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к SAP Fiori.
* Пользователи могут автоматически входить в SAP Fiori с учетными записями Azure AD (единый вход).
* Вы можете управлять учетными записями централизованно, через портал Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье о [едином входе в приложения в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с SAP Fiori, вам потребуется:

* подписка Azure AD Если у вас еще нет подписки Azure AD, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем приступить к работе.
* подписка SAP Fiori с поддержкой единого входа.
* Требуется SAP Fiori 7.20 или более поздней версии.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде и интеграцию SAP Fiori с Azure AD.

SAP Fiori поддерживает следующие функции:

* **единый вход, инициируемый поставщиком услуг**.

## <a name="add-sap-fiori-in-the-azure-portal"></a>Добавление SAP Fiori на портале Azure

Чтобы интегрировать SAP Fiori с Azure AD, необходимо добавить SAP Fiori в список управляемых приложений SaaS.

1. Войдите на [портале Azure](https://portal.azure.com).

1. В меню слева выберите **Azure Active Directory**.

    ![Пункт Azure Active Directory](common/select-azuread.png)

1. Щелкните **Корпоративные приложения** > **Все приложения**.

    ![Область "Корпоративные приложения"](common/enterprise-applications.png)

1. Чтобы добавить приложение, выберите **Новое приложение**.

    ![Элемент "Новое приложение"](common/add-new-app.png)

1. В поле поиска введите **SAP Fiori**. В результатах поиска выберите **SAP Fiori**, а затем щелкните **Добавить**.

    ![SAP Fiori в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в приложение SAP Fiori с использованием тестового пользователя **Britta Simon**. Для обеспечения работы единого входа необходимо установить связь между пользователем Azure Active Directory и соответствующим пользователем в SAP Fiori.

Чтобы настроить и проверить единый вход Azure AD в SAP Fiori, потребуется выполнить действия в следующих стандартных блоках.

| Задача | ОПИСАНИЕ |
| --- | --- |
| **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** | Позволяет пользователям использовать эту возможность. |
| **[Настройка единого входа в SAP Fiori](#configure-sap-fiori-single-sign-on)** | Настройка параметров единого входа в приложении. |
| **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** | Проверка работы единого входа Azure AD на примере пользователя Britta Simon. |
| **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** | Предоставление пользователю Britta Simon возможности использовать единый вход Azure AD. |
| **[Создание тестового пользователя SAP Fiori](#create-an-sap-fiori-test-user)** . | Требуется, чтобы в SAP Fiori существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD. |
| **[Проверка единого входа](#test-single-sign-on)** | необходима, чтобы убедиться, что конфигурация работает правильно. |

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описывается настройка единого входа Azure AD в SAP Fiori на портале Azure.

1. Откройте новое окно веб-браузера и войдите на свой корпоративный сайт SAP Fiori как администратор.

1. Убедитесь, что службы **http** и **https** активны, а в коде транзакции **SMICM** назначены соответствующие порты.

1. Войдите в бизнес-клиент SAP для системы SAP **T01**, где требуется единый вход. После этого активируйте управление сеансами безопасности HTTP.

    1. Перейдите к коду транзакции **SICF_SESSIONS**. В нем отображаются все параметры соответствующего профиля с текущими значениями. Они должны выглядеть, как показано ниже.

        ```
        login/create_sso2_ticket = 2
        login/accept_sso2_ticket = 1
        login/ticketcache_entries_max = 1000
        login/ticketcache_off = 0  login/ticket_only_by_https = 0 
        icf/set_HTTPonly_flag_on_cookies = 3
        icf/user_recheck = 0  http/security_session_timeout = 1800
        http/security_context_cache_size = 2500
        rdisp/plugin_auto_logout = 1800
        rdisp/autothtime = 60
        ```

        >[!NOTE]
        > Настройте параметры в соответствии с требованиями организации. Описанные выше параметры приведены только в качестве примера.

    1. При необходимости настройте параметры в профиле экземпляра (используемого по умолчанию) системы SAP и перезапустите систему SAP.

    1. Дважды щелкните соответствующий клиент, чтобы включить сеанс безопасности HTTP.

        ![Страница "Current Values of Relevant Profile Parameters" (Текущие значения соответствующих параметров профиля) в SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-profileparameter.png)

    1. Активируйте следующие службы SICF.

        ```
        /sap/public/bc/sec/saml2
        /sap/public/bc/sec/cdc_ext_service
        /sap/bc/webdynpro/sap/saml2
        /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
        ```

1. Перейдите к коду транзакции **SAML2** в бизнес-клиенте для системы SAP [**T01/122**]. В новом окне браузера откроется пользовательский интерфейс настройки. В этом примере мы используем бизнес-клиент для системы SAP 122.

    ![Страница входа в бизнес-клиент SAP Fiori](./media/sapfiori-tutorial/tutorial-sapnetweaver-sapbusinessclient.png)

1. Введите имя пользователя и пароль, а затем щелкните **Вход**.

    ![Страница "SAML 2.0 Configuration of ABAP System T01/122" (Настройка SAML 2.0 системы ABAP T01/122) в SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-userpwd.png)

1. В поле **Имя поставщика** замените **T01122** на **http:\//T01122**, а затем выберите **Сохранить**.

    > [!NOTE]
    > По умолчанию имя поставщика имеет формат \<sid>\<клиент>. Azure AD ожидает имя в формате \<протокол>://\<имя>. Мы рекомендуем использовать имя поставщика в формате https\://\<sid>\<клиент>, чтобы в Azure AD можно было настроить несколько ядер ABAP SAP Fiori.

    ![Обновленное имя поставщика на странице "SAML 2.0 Configuration of ABAP System T01/122" (Настройка SAML 2.0 системы ABAP T01/122) в SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-providername.png)

1. Выберите вкладку **Local Provider** (Локальный поставщик)  > **Metadata** (Метаданные).

1. В диалоговом окне **SAML 2.0 Metadata** (Метаданные SAML 2.0) скачайте созданный XML-файл метаданных и сохраните его на компьютере.

    ![Ссылка "Download Metadata" (Скачать метаданные) в диалоговом окне "SAP SAML 2.0 Metadata" (Метаданные SAP SAML 2.0)](./media/sapfiori-tutorial/tutorial-sapnetweaver-generatesp.png)

1. На [портале Azure](https://portal.azure.com/) в области интеграции с приложением **SAP Fiori** выберите **Единый вход**.

    ![Параметр "Единый вход"](common/select-sso.png)

1. В области **Выбрать метод единого входа** выберите режим **SAML** или **SAML/WS-FED**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

1. На панели **Настройка единого входа с помощью SAML** щелкните **Изменить** (значок карандаша), чтобы открыть панель **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

1. В области **Базовая конфигурация SAML** выполните описанные ниже действия.

    1. Щелкните **Отправить файл метаданных**.

        ![Параметр "Отправить файл метаданных"](common/upload-metadata.png)

   1. Чтобы выбрать файл метаданных, выберите значок папки, а затем выберите **Отправить**.

       ![Выберите файл метаданных, а затем нажмите кнопку "Отправить"](common/browse-upload-metadata.png)

1. После успешной передачи файла метаданных значения параметров **Идентификатор** и **URL-адрес ответа** в области **Базовая конфигурация SAML** будут заполнены автоматически. В поле **URL-адрес для входа** введите URL-адрес в следующем формате: https:\//\<ваш корпоративный экземпляр SAP Fiori\>.

    ![Сведения о домене и URL-адресах единого входа для SAP Fiori](common/sp-identifier-reply.png)

    > [!NOTE]
    > Несколько клиентов сообщают об ошибках, связанных с неправильной настройкой значений **URL-адрес ответа**. Если вы видите сообщение об этой ошибке, с помощью следующего сценария PowerShell укажите правильный URL-адрес ответа для своего экземпляра.
    >
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > 
    > Можно самостоятельно задать идентификатор объекта `ServicePrincipal` перед выполнением сценария либо передать его сюда.

1. Приложение SAP Fiori ожидает утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Чтобы изменить значения этих атрибутов, в области **Настройка единого входа с помощью SAML** выберите **Изменить**.

    ![Область "Атрибуты пользователя"](common/edit-attribute.png)

1. В области **Утверждения и атрибуты пользователя** настройте атрибуты токена SAML, как показано на предыдущем рисунке. Затем сделайте следующее:

    1. Выберите **Изменить**, чтобы открыть область **Управление утверждениями пользователя**.

    1. Из списка **Преобразование** выберите **ExtractMailPrefix()** .

    1. Из списка **Параметр 1** выберите **user.userprinicipalname**.

    1. Щелкните **Сохранить**.

       ![Область "Управление утверждениями пользователя"](./media/sapfiori-tutorial/nameidattribute.png)

       ![Раздел "Преобразование" в области "Управление утверждениями пользователя"](./media/sapfiori-tutorial/nameidattribute1.png)


1. В области **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** нажмите кнопку **Скачать**, расположенную рядом с полем **XML метаданных федерации**. Выберите параметр для скачивания в соответствии с вашими требованиями. Сохраните сертификат на своем компьютере.

    ![Параметр "Скачать сертификат"](common/metadataxml.png)

1. Требуемые URL-адреса можно скопировать из раздела **Настройка SAP Fiori**.

    * URL-адрес входа.
    * Идентификатор Azure AD
    * URL-адрес выхода.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

### <a name="configure-sap-fiori-single-sign-on"></a>Настройка единого входа в SAP Fiori

1. Войдите в систему SAP и перейдите к коду транзакции **SAML2**. Откроется новое окно браузера со страницей настройки SAML.

1. Чтобы настроить конечные точки для доверенного поставщика удостоверений (Azure AD), перейдите на вкладку **Trusted Providers** (Надежные поставщики).

    ![Вкладка "Trusted Providers" (Надежные поставщики) в SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-samlconfig.png)

1. Щелкните **Add** (Добавить) и выберите **Upload Metadata File** (Отправить файл метаданных) в контекстном меню.

    ![Параметры "Add" (Добавить) и "Upload Metadata File" (Отправить файл метаданных) в SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-uploadmetadata.png)

1. Передайте файл метаданных, скачанный с портала Azure. Щелкните **Далее**.

    ![Выбор файла метаданных для передачи в SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-metadatafile.png)

1. На следующей странице в поле **Alias** (Псевдоним) введите псевдоним. Например, **aadsts**. Щелкните **Далее**.

    ![Поле "Alias" (Псевдоним) в SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-aliasname.png)

1. Убедитесь, что поле **Digest Algorithm** (Алгоритм выборки сообщений) содержит значение **SHA-256**. Щелкните **Далее**.

    ![Проверка значения поля "Digest Algorithm" (Алгоритм выборки сообщений) в SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-identityprovider.png)

1. В разделе **Single Sign-On Endpoints** (Конечные точки единого входа) выберите **HTTP POST**, а затем щелкните **Next** (Далее).

    ![Параметры в разделе "Single Sign-On Endpoints" (Конечные точки единого входа) в SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect.png)

1. В разделе **Single Logout Endpoints** (Конечные точки единого выхода) выберите **HTTP Redirect** (Перенаправление протокола HTTP), а затем щелкните **Next** (Далее).

    ![Параметры в разделе "Single Logout Endpoints" (Конечные точки единого выхода) в SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect1.png)

1. В разделе **Artifact Endpoints** (Конечные точки артефактов) щелкните **Next** (Далее), чтобы продолжить.

    ![Параметры в разделе "Artifact Endpoints" (Конечные точки артефактов) в SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-artifactendpoint.png)

1. В разделе **Authentication Requirements** (Требования аутентификации) щелкните **Finish** (Готово).

    ![Параметры в разделе "Authentication Requirements" (Требования аутентификации) и параметр "Finish" (Готово) в SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-authentication.png)

1. Выберите **Trusted Provider** (Надежный поставщик)  > **Identity Federation** (Федерация удостоверений) (внизу страницы). Выберите **Изменить**

    ![Вкладки "Trusted Provider" (Надежный поставщик) и "Identity Federation" (Федерация удостоверений) в SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-trustedprovider.png)

1. Выберите **Добавить**.

    ![Параметр "Add" (Добавить) на вкладке "Identity Federation" (Федерация удостоверений)](./media/sapfiori-tutorial/tutorial-sapnetweaver-addidentityprovider.png)

1. В диалоговом окне **Supported NameID Formats** (Поддерживаемые форматы NameID) выберите **Unspecified** (Не указано). Нажмите кнопку **ОК**.

    ![Диалоговое окно "Supported NameID Formats" (Поддерживаемые форматы NameID) и параметры в SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameid.png)

    Значения **User ID Source** (Источник идентификатора пользователя) и **User ID Mapping Mode** (Режим сопоставления идентификатора пользователя) определяют связь между пользователем SAP и утверждением Azure AD.  

    **Сценарий 1**. Сопоставление пользователя SAP и пользователя Azure AD

    1. В SAP в разделе **Details of NameID Format "Unspecified"** (Сведения о формате NameID "На указано") запишите следующие сведения.

        ![Диалоговое окно "Details of NameID Format "Unspecified" (Сведения о формате NameID "На указано") в SAP](./media/sapfiori-tutorial/nameiddetails.png)

    1. На портале Azure в разделе **Утверждения и атрибуты пользователя** запишите обязательные утверждения из Azure AD.

        ![Диалоговое окно "Утверждения и атрибуты пользователя" на портале Azure](./media/sapfiori-tutorial/claimsaad1.png)

    **Сценарий 2**. Выбор идентификатора пользователя SAP с учетом указанного адреса электронной почты в SU01 В этом случае идентификатор электронной почты должен быть настроен в SU01 для каждого пользователя, которому требуется единый вход.

    1.  В SAP в разделе **Details of NameID Format "Unspecified"** (Сведения о формате NameID "На указано") запишите следующие сведения.

        ![Диалоговое окно "Details of NameID Format "Unspecified" (Сведения о формате NameID "На указано") в SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameiddetails1.png)

    1. На портале Azure в разделе **Утверждения и атрибуты пользователя** запишите обязательные утверждения из Azure AD.

       ![Диалоговое окно "Утверждения и атрибуты пользователя" на портале Azure](./media/sapfiori-tutorial/claimsaad2.png)

1. Выберите **Save** (Сохранить), затем выберите **Enable** (Включить), чтобы включить поставщик удостоверений.

    ![Параметры "Save" (Сохранить) и "Enable" (Включить) в SAP](./media/sapfiori-tutorial/configuration1.png)

1. При появлении запроса нажмите кнопку **ОК**.

    ![Кнопка "ОК" в диалоговом окне "SAML 2.0 Configuration" (Конфигурация SAML 2.0) в SAP](./media/sapfiori-tutorial/configuration2.png)

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

В этом разделе описано, как создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure выберите **Azure Active Directory** > **Пользователи** > **Все пользователи**.

    ![Пункты меню "Пользователи" и "Все пользователи"](common/users.png)

1. Выберите **Новый пользователь**.

    ![Элемент "Новый пользователь"](common/new-user.png)

1. В области **Пользователь** выполните приведенные ниже действия.

    1. В поле **Имя** введите **BrittaSimon**.
  
    1. В поле **Имя пользователя** введите **brittasimon\@\<домен_вашей_компании>.\<доменная_зона>** . Например, **brittasimon\@contoso.com**.

    1. Установите флажок **Показать пароль**. Запишите значение, которое отображается в поле **Пароль**.

    1. Нажмите кнопку **Создать**.

    ![Панель "Пользователь"](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе вы предоставите Britta Simon доступ к SAP Fiori, что позволит этому пользователю использовать единый вход Azure.

1. На портале Azure выберите **Корпоративные приложения** > **Все приложения** > **SAP Fiori**.

    ![Область "Корпоративные приложения"](common/enterprise-applications.png)

1. В списке приложений выберите **SAP Fiori**.

    ![Ссылка на SAP Fiori в списке "Приложения"](common/all-applications.png)

1. В меню выберите **Пользователи и группы**.

    ![Пункт меню "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**. Затем в области **Добавление назначения** щелкните **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

1. На панели **Пользователи и группы** в списке пользователей выберите **Britta Simon**. Щелкните **Выбрать**.

1. Если вы планируете получать значение роли в утверждении SAML, выберите на панели **Выбор роли** подходящую роль для пользователя из предложенного списка. Щелкните **Выбрать**.

1. В области **Добавление назначения** выберите **Назначить**.

### <a name="create-an-sap-fiori-test-user"></a>Создание тестового пользователя SAP Fiori

В этом разделе описано, как создать пользователя Britta Simon в приложении SAP Fiori. Обратитесь к штатной команде экспертов по SAP или с партнеру SAP своей организации, чтобы добавить пользователей на платформу SAP Fiori.

### <a name="test-single-sign-on"></a>Проверка единого входа

1. После активации поставщика удостоверений Azure AD в SAP Fiori попробуйте перейти по одному из следующих URL-адресов, чтобы проверить единый вход (запрос имени пользователя и пароля не должен отображаться):

    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm
    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm

    > [!NOTE]
    > Замените *sapurl* фактическим именем узла SAP.

1. Тестовый URL-адрес должен открывать приведенную ниже тестовую страницу приложения в SAP. Если эта страница открылась, то единый вход Azure AD успешно настроен.

    ![Стандартная тестовая страница приложения в SAP](./media/sapfiori-tutorial/testingsso.png)

1. При появлении запроса имени пользователя и пароля включите трассировку, чтобы диагностировать проблему. Используйте следующий URL-адрес для трассировки: https:\//\<sapurl\>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения см. в статьях ниже:

- [Список руководств по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Что такое условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
