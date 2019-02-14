---
title: Руководство по Интеграция Azure Active Directory с SAP NetWeaver | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в SAP NetWeaver.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 1b9e59e3-e7ae-4e74-b16c-8c1a7ccfdef3
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66ad6ea26535190d2fc5798e1223c2196d3d5a5d
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56211144"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-netweaver"></a>Руководство. Интеграция Azure Active Directory с SAP NetWeaver

В этом руководстве описано, как интегрировать SAP NetWeaver с Azure Active Directory.
Интеграция SAP NetWeaver с Azure AD обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к приложению SAP NetWeaver.
* Вы можете включить автоматический вход пользователей в SAP NetWeaver (единый вход) с помощью учетных записей Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с SAP NetWeaver, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* подписка на SAP NetWeaver с поддержкой единого входа;
* по меньшей мере SAP NetWeaver 7.20.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* SAP NetWeaver поддерживает инициированный единый вход **пакета обновления**.

## <a name="adding-sap-netweaver-from-the-gallery"></a>Добавление SAP NetWeaver из коллекции

Чтобы настроить интеграцию SAP NetWeaver с Azure AD, необходимо добавить SAP NetWeaver из коллекции в список управляемых приложений SaaS.

**Чтобы добавить SAP NetWeaver из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **SAP NetWeaver**, выберите **SAP NetWeaver** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![SAP NetWeaver в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в SAP NetWeaver с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в SAP NetWeaver.

Чтобы настроить и проверить единый вход Azure AD в SAP NetWeaver, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в SAP NetWeaver](#configure-sap-netweaver-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя SAP NetWeaver](#create-sap-netweaver-test-user)** требуется для создания пользователя Britta Simon в SAP NetWeaver, связанного с соответствующим пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в SAP NetWeaver, сделайте следующее.

1. Откройте окно веб-браузера и войдите на сайт своей компании SAP NetWeaver как администратор

2. Убедитесь, что службы **http** и **https** активны, а в коде транзакции **SMICM** назначены соответствующие порты.

3. Выполните вход в бизнес-клиент системы SAP (T01), где требуется единый вход, и активируйте управление сеансом безопасности HTTP.

    a. Перейдите к коду транзакции **SICF_SESSIONS**. В нем отображаются все параметры соответствующего профиля с текущими значениями. Они выглядят следующим образом:
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
    > Настройте эти параметры в соответствии с требованиями вашей организации. Упомянутые выше параметры приведены здесь только для иллюстрации.

    б) При необходимости настройте параметры в экземпляре / профиле по умолчанию системы SAP и перезапустите систему SAP.

    c. Дважды щелкните соответствующего клиента, чтобы включить сеанс безопасности HTTP.

    ![Ссылка для скачивания сертификата](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_profileparameter.png)

    4.3. Активируйте ниже службы SICF:
    ```
    /sap/public/bc/sec/saml2
    /sap/public/bc/sec/cdc_ext_service
    /sap/bc/webdynpro/sap/saml2
    /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
    ```
4. Перейдите к коду транзакции **SAML2** в бизнес-клиенте системы SAP [T01/122]. В браузере откроется пользовательский интерфейс. В этом примере предполагается, что 122 является бизнес-клиентом SAP.

    ![Ссылка для скачивания сертификата](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_sapbusinessclient.png)

5. Укажите имя пользователя и пароль для входа в пользовательский интерфейс, затем нажмите кнопку **Изменить**.

    ![Ссылка для скачивания сертификата](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_userpwd.png)

6. Замените **Имя поставщика** T01122, указав `http://T01122`, и щелкните **Сохранить**.

    > [!NOTE]
    > Имя поставщика по умолчанию указывается в формате <sid><client>, но Azure AD ожидает имя в формате <protocol>://<name>, рекомендуя поддерживать имя поставщика как https://<sid><client>, чтобы разрешить многим обработчикам SAP NetWeaver ABAP выполнять настройку в Azure AD.

    ![Ссылка для скачивания сертификата](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_providername.png)

7. **Создание метаданных поставщика служб.** После настройки **локального поставщика** и **надежных поставщиков** в пользовательском интерфейсе SAML 2.0 следующим шагом является создание файла метаданных поставщика служб (который будет содержать все параметры, контексты проверки подлинности и другие конфигурации в SAP). После создания этого файла необходимо отправить его в Azure AD.

    ![Ссылка для скачивания сертификата](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_generatesp.png)

    a. Перейдите на вкладку **Локальный поставщик**.

    б) Щелкните **Метаданные**.

    c. Сохраните созданный **файл XML метаданных** на компьютере и передайте его в раздел **Базовая конфигурация SAML**, чтобы автоматически заполнить значения **Идентификатор** и **URL-адрес ответа** на портале Azure.

8. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **SAP NetWeaver** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

9. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

10. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

11. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    a. Щелкните **Передать файл метаданных** для отправки **файла метаданных поставщика служб**, полученного ранее.

    ![Передача файла метаданных](common/upload-metadata.png)

    б) Щелкните **значок папки**, выберите файл метаданных и нажмите кнопку **Отправить**.

    ![Выбор файла метаданных](common/browse-upload-metadata.png)

    c. После успешной передачи файла метаданных значения **Идентификатор** и **URL-адрес ответа** в разделе **Базовая конфигурация SAML** автоматически заполняются значениями, как показано ниже.

    ![Сведение о домене и URL-адресах единого входа для приложения SAP NetWeaver](common/sp-identifier-reply.png)

    В текстовое поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<your company instance of SAP NetWeaver>`.

12. Приложение SAP NetWeaver ожидает утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно в разделе **Атрибуты пользователя** на странице интеграции приложения. На странице **Настройка единого входа с помощью SAML** нажмите кнопку **Изменить**, чтобы открыть диалоговое окно **Атрибуты пользователя**.

    ![изображение](common/edit-attribute.png)

13. В разделе **Утверждения пользователя** диалогового окна **Атрибуты пользователя** настройте атрибут токена SAML, как показано на рисунке выше, и выполните следующие действия.

    a. Щелкните **значок редактирования**, чтобы открыть диалоговое окно **Управление утверждениями пользователя**.

    ![изображение](./media/sapnetweaver-tutorial/nameidattribute.png)

    ![изображение](./media/sapnetweaver-tutorial/nameidattribute1.png)

    б) Из списка **Преобразование** выберите **ExtractMailPrefix()**.

    c. Из списка **Параметр 1** выберите **user.userprinicipalname**.

    4.3. Выберите команду **Сохранить**.

14. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать нужный вам **XML метаданных федерации**, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

15. Из раздела **Настройка SAP NetWeaver** скопируйте нужные URL-адреса согласно вашим требованиям.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    б) Идентификатор Azure AD.

    в) URL-адрес выхода.

### <a name="configure-sap-netweaver-single-sign-on"></a>Настройка единого входа SAP NetWeaver

1. Войдите в систему SAP и перейдите к коду транзакции SAML2. Откроется новое окно браузера с экраном настройки SAML.

2. Чтобы настроить конечные точки для доверенного поставщика удостоверений (Azure AD) перейдите на вкладку **Доверенные поставщики**.

    ![Настройка единого входа](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_samlconfig.png)

3. Нажмите **Добавить** и выберите **Отправить файл метаданных** в контекстном меню.

    ![Настройка единого входа](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_uploadmetadata.png)

4. Отправьте файл метаданных, который вы скачали на портале Azure.

    ![Настройка единого входа](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_metadatafile.png)

5. На следующем экране введите псевдоним, например aadsts, и нажмите **Далее** для продолжения.

    ![Настройка единого входа](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_aliasname.png)

6. **Алгоритм хэш-кода** должен быть **SHA-256**, он не требует внесения изменений. Затем нажмите **Далее**.

    ![Настройка единого входа](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_identityprovider.png)

7. В разделе **Конечные точки единого входа** укажите **HTTP POST** и нажмите **Далее** для продолжения.

    ![Настройка единого входа](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect.png)

8. В разделе **Конечные точки единого выхода** выберите **HTTPRedirect** и нажмите **Далее** для продолжения.

    ![Настройка единого входа](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect1.png)

9. В разделе **Конечные точки артефакта** нажмите **Далее** для продолжения.

    ![Настройка единого входа](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_artifactendpoint.png)

10. В разделе **Требования к проверке подлинности** нажмите **Готово**.

    ![Настройка единого входа](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_authentication.png)

11. Перейдите на вкладку **Надежный поставщик** > **Федерация удостоверений** (из нижней части экрана). Нажмите кнопку **Изменить**.

    ![Настройка единого входа](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_trustedprovider.png)

12. Нажмите **Добавить** на вкладке **Федерация удостоверений** (нижнее окно).

    ![Настройка единого входа](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_addidentityprovider.png)

13. Во всплывающем окне выберите **Не указано** из списка **Поддерживаемые форматы NameID** и нажмите "ОК".

    ![Настройка единого входа](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameid.png)

14. Обратите внимание, что значения **Источник идентификатора пользователя** и **Режим сопоставления идентификатора пользователя** определяют связь между пользователем SAP и утверждением Azure AD.  

    #### <a name="scenario-sap-user-to-azure-ad-user-mapping"></a>Сценарий. Сопоставление пользователя SAP и пользователя Azure AD.

    a. Снимок экрана со сведениями о NameID в SAP.

    ![Настройка единого входа](./media/sapnetweaver-tutorial/nameiddetails.png)

    б) Снимок экрана, на котором упоминаются требуемые утверждения из Azure AD.

    ![Настройка единого входа](./media/sapnetweaver-tutorial/claimsaad1.png)

    #### <a name="scenario-select-sap-user-id-based-on-configured-email-address-in-su01-in-this-case-email-id-should-be-configured-in-su01-for-each-user-who-requires-sso"></a>Сценарий. Выбор идентификатора пользователя SAP с учетом указанного электронного адреса в SU01. В этом случае идентификатор электронной почты должен быть настроен в su01 для каждого пользователя, которому требуется единый вход.

    a.  Снимок экрана со сведениями о NameID в SAP.

    ![Настройка единого входа](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameiddetails1.png)

    б) Снимок экрана, на котором упоминаются требуемые утверждения из Azure AD.

    ![Настройка единого входа](./media/sapnetweaver-tutorial/claimsaad2.png)

15. Последовательно выберите **Сохранить** и **Включить** для включения поставщика удостоверений.

    ![Настройка единого входа](./media/sapnetweaver-tutorial/configuration1.png)

16. Нажмите **ОК** при появлении запроса.

    ![Настройка единого входа](./media/sapnetweaver-tutorial/configuration2.png)

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    а) В поле **Имя** введите **BrittaSimon**.
  
    б) В поле **Имя пользователя** введите **brittasimon@yourcompanydomain.extension**.  
    Например BrittaSimon@contoso.com.

    в) Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    г) Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к SAP NetWeaver.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **SAP NetWeaver**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **SAP NetWeaver**.

    ![Ссылка на SAP NetWeaver в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-sap-netweaver-test-user"></a>Создание тестового пользователя SAP NetWeaver

В этом разделе описано, как создать пользователя Britta Simon в приложении SAP NetWeaver. Совместно со штатной командой экспертов SAP или с партнером SAP организации добавьте пользователей на платформу SAP NetWeaver.

### <a name="test-single-sign-on"></a>Проверка единого входа 

1. После активизации Azure AD для поставщика удостоверений попробуйте получить доступ по указанному ниже URL-адресу, чтобы проверить единый вход (не должен отображаться запрос на ввод имени пользователя и пароля).

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    Можно также указать такой URL-адрес:

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    > [!NOTE]
    > Замените sapurl фактическим именем узла SAP.

2. Указанный выше URL-адрес должен переадресовать вас на показанный ниже экран. Если вы можете открыть показанную ниже страницу, настройка единого входа Azure AD выполнена успешно.

    ![Настройка единого входа](./media/sapnetweaver-tutorial/testingsso.png)

3. Если отобразится запрос на ввод имени пользователя и пароля, диагностируйте проблему путем включения трассировки, используя приведенный ниже URL-адрес.

    `https://<sapurl>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#`

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

