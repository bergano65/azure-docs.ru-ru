---
title: Руководство. Интеграция Azure Active Directory с SAP NetWeaver | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в SAP NetWeaver.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1b9e59e3-e7ae-4e74-b16c-8c1a7ccfdef3
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: jeedes
ms.openlocfilehash: 607d05818966e62407795640d223f1aed2f59bbb
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54156754"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-netweaver"></a>Руководство. Интеграция Azure Active Directory с SAP NetWeaver

В этом руководстве описано, как интегрировать SAP NetWeaver с Azure Active Directory.

Интеграция SAP NetWeaver с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к приложению SAP NetWeaver.
- Вы можете включить автоматический вход пользователей в SAP NetWeaver (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с SAP NetWeaver, вам потребуется:

- подписка Azure AD;
- подписка на SAP NetWeaver с поддержкой единого входа;
- по меньшей мере SAP NetWeaver 7.20.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде.
Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление SAP NetWeaver из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-sap-netweaver-from-the-gallery"></a>Добавление SAP NetWeaver из коллекции

Чтобы настроить интеграцию SAP NetWeaver с Azure AD, необходимо добавить SAP NetWeaver из коллекции в список управляемых приложений SaaS.

**Чтобы добавить SAP NetWeaver из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **SAP NetWeaver**, выберите **SAP NetWeaver** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![SAP NetWeaver в списке результатов](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в SAP NetWeaver с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в SAP NetWeaver соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в SAP NetWeaver.

Чтобы настроить и проверить единый вход Azure AD в SAP NetWeaver, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя SAP NetWeaver](#creating-sapnetweaver-test-user)** требуется для создания пользователя Britta Simon в SAP NetWeaver, связанного с соответствующим пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении SAP NetWeaver.

**Чтобы настроить единый вход Azure AD в SAP NetWeaver, сделайте следующее:**

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

    b. При необходимости настройте параметры в экземпляре / профиле по умолчанию системы SAP и перезапустите систему SAP.

    c. Дважды щелкните соответствующего клиента, чтобы включить сеанс безопасности HTTP.

    ![Ссылка для скачивания сертификата](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_profileparameter.png)

    d. Активируйте ниже службы SICF:
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

    b. Щелкните **Метаданные**.

    c. Сохраните созданный **файл XML метаданных** на компьютере и передайте его в раздел **Базовая конфигурация SAML**, чтобы автоматически заполнить значения **Идентификатор** и **URL-адрес ответа** на портале Azure.

8. На портале Azure на странице интеграции с приложением **SAP NetWeaver** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

9. В диалоговом окне **Выбрать метод единого входа** щелкните **Выбрать** для режима **SAML**, чтобы включить единый вход.

    ![Настройка единого входа](common/tutorial_general_301.png)

10. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Настройка единого входа](common/editconfigure.png)

11. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    a. Щелкните **Передать файл метаданных** для отправки **файла метаданных поставщика служб**, полученного ранее.

    ![Передача файла метаданных](common/editmetadataupload.png)

    b. Щелкните **значок папки**, выберите файл метаданных и нажмите кнопку **Отправить**.

    ![Передача файла метаданных](common/uploadmetadata.png)

    c. После успешной передачи файла метаданных поля **Идентификатор** и **URL-адрес ответа** в разделе **Базовая конфигурация SAML** автоматически заполняются значениями, как показано ниже:

    ![Сведение о домене и URL-адресах единого входа для приложения SAP NetWeaver](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_url.png)

    d. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<your company instance of SAP NetWeaver>`

12. Приложение SAP NetWeaver ожидает утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно в разделе **Атрибуты пользователя** на странице интеграции приложения. На странице **Настройка единого входа с помощью SAML** нажмите кнопку **Изменить**, чтобы открыть диалоговое окно **Атрибуты пользователя**.

    ![Раздел "Атрибут"](./media/sapnetweaver-tutorial/edit_attribute.png)

13. В разделе **Утверждения пользователя** диалогового окна **Атрибуты пользователя** настройте атрибут токена SAML, как показано на рисунке выше, и выполните следующие действия.

    a. Щелкните **значок редактирования**, чтобы открыть диалоговое окно **Управление утверждениями пользователя**.
    
    ![Раздел "Атрибут"](./media/sapnetweaver-tutorial/nameidattribute.png)

    b. На вкладке **Управление утверждениями пользователя** выполните следующие шаги:

    ![Раздел "Атрибут"](./media/sapnetweaver-tutorial/nameidattribute1.png)

    * Выберите **Преобразование**.
  
    * Из списка **Преобразование** выберите `ExtractMailPrefix()`.
  
    * Из списка **Параметр 1** выберите `user.userprincipalname`.

    * Выберите команду **Сохранить**.

14. На странице **сертификата подписи SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать **XML метаданных федерации**, а затем сохраните метаданные на компьютере.

    ![Ссылка для скачивания сертификата](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_certificate.png)

15. Из раздела **Настройка SAP NetWeaver** скопируйте нужный URL-адрес согласно вашим требованиям.

    a. URL-адрес входа.

    b. Идентификатор Azure AD

    c. URL-адрес выхода

    ![Настройка SAP NetWeaver](common/configuresection.png)

16. Войдите в систему SAP и перейдите к коду транзакции SAML2. Откроется новое окно браузера с экраном настройки SAML.

17. Чтобы настроить конечные точки для доверенного поставщика удостоверений (Azure AD) перейдите на вкладку **Доверенные поставщики**.

    ![Настройка единого входа](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_samlconfig.png)

18. Нажмите **Добавить** и выберите **Отправить файл метаданных** в контекстном меню.

    ![Настройка единого входа](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_uploadmetadata.png)

19. Отправьте файл метаданных, который вы скачали на портале Azure.

    ![Настройка единого входа](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_metadatafile.png)

20. На следующем экране введите псевдоним, например aadsts, и нажмите **Далее** для продолжения.

    ![Настройка единого входа](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_aliasname.png)

21. **Алгоритм хэш-кода** должен быть **SHA-256**, он не требует внесения изменений. Затем нажмите **Далее**.

    ![Настройка единого входа](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_identityprovider.png)

22. В разделе **Конечные точки единого входа** укажите **HTTP POST** и нажмите **Далее** для продолжения.

    ![Настройка единого входа](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect.png)

23. В разделе **Конечные точки единого выхода** выберите **HTTPRedirect** и нажмите **Далее** для продолжения.

    ![Настройка единого входа](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect1.png)

24. В разделе **Конечные точки артефакта** нажмите **Далее** для продолжения.

    ![Настройка единого входа](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_artifactendpoint.png)

25. В разделе **Требования к проверке подлинности** нажмите **Готово**.

    ![Настройка единого входа](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_authentication.png)

26. Перейдите на вкладку **Надежный поставщик** > **Федерация удостоверений** (из нижней части экрана). Нажмите кнопку **Изменить**.

    ![Настройка единого входа](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_trustedprovider.png)

27. Нажмите **Добавить** на вкладке **Федерация удостоверений** (нижнее окно).

    ![Настройка единого входа](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_addidentityprovider.png)

28. Во всплывающем окне выберите **Не указано** из списка **Поддерживаемые форматы NameID** и нажмите "ОК".

    ![Настройка единого входа](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameid.png)

29. Обратите внимание, что значения **Источник идентификатора пользователя** и **Режим сопоставления идентификатора пользователя** определяют связь между пользователем SAP и утверждением Azure AD.  

    ####<a name="scenario-sap-user-to-azure-ad-user-mapping"></a>Сценарий. Сопоставление пользователя SAP и пользователя Azure AD.

    a. Снимок экрана сведений NameID в SAP.

    ![Настройка единого входа](./media/sapnetweaver-tutorial/nameiddetails.png)

    b. Снимок экрана, на котором упоминаются требуемые утверждения из Azure AD.

    ![Настройка единого входа](./media/sapnetweaver-tutorial/claimsaad1.png)

    ####<a name="scenario-select-sap-user-id-based-on-configured-email-address-in-su01-in-this-case-email-id-should-be-configured-in-su01-for-each-user-who-requires-sso"></a>Сценарий. Выбор идентификатора пользователя SAP с учетом указанного электронного адреса в SU01. В этом случае идентификатор электронной почты должен быть настроен в su01 для каждого пользователя, которому требуется единый вход.

    a.  Снимок экрана сведений NameID в SAP.

    ![Настройка единого входа](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameiddetails1.png)

    b. Снимок экрана, на котором упоминаются требуемые утверждения из Azure AD.

    ![Настройка единого входа](./media/sapnetweaver-tutorial/claimsaad2.png)

30. Последовательно выберите **Сохранить** и **Включить** для включения поставщика удостоверений.

    ![Настройка единого входа](./media/sapnetweaver-tutorial/configuration1.png)

31. Нажмите **ОК** при появлении запроса.

    ![Настройка единого входа](./media/sapnetweaver-tutorial/configuration2.png)

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Создание пользователя Azure AD][100]

2. В верхней части экрана выберите **Новый пользователь**.

    ![Создание тестового пользователя Azure AD](common/create_aaduser_01.png)

3. В разделе свойств пользователя сделайте следующее.

    ![Создание тестового пользователя Azure AD](common/create_aaduser_02.png)

    a. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon@yourcompanydomain.extension**.  
    Например, BrittaSimon@contoso.com

    c. Выберите **Свойства**, установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="creating-sap-netweaver-test-user"></a>Создание тестового пользователя SAP NetWeaver

В этом разделе описано, как создать пользователя Britta Simon в приложении SAP NetWeaver. Совместно со штатной командой экспертов SAP или с партнером SAP организации добавьте пользователей на платформу SAP NetWeaver.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к SAP NetWeaver.

1. На портале Azure перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Назначение пользователя][201]

2. В списке приложений выберите **SAP NetWeaver**.

    ![Настройка единого входа](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="testing-single-sign-on"></a>Проверка единого входа

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

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
