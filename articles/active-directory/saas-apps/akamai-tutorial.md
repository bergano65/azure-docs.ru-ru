---
title: Руководство по интеграции единого входа Azure Active Directory с Akamai | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Akamai.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/03/2020
ms.author: jeedes
ms.openlocfilehash: fbec82e25424fd5220aa992cf2dd0e8449e6a0a4
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88523139"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-akamai"></a>Руководство по интеграции единого входа Azure Active Directory с Akamai

В этом руководстве описано, как интегрировать Akamai с Azure Active Directory (Azure AD). Интеграция Akamai с Azure AD обеспечивает следующие возможности:

* Контроль доступа к Akamai с помощью Azure AD.
* Автоматический вход пользователей в Akamai с учетными записями Azure AD.
* Централизованное управление учетными записями через портал Azure.

Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

Интеграция Azure Active Directory c Akamai Enterprise Application Access обеспечивает простой доступ к устаревшим приложениям, размещенным в облаке или локально. Для доступа к устаревшим приложениям без их изменения или установки агентов в интегрированном решении используются все преимущества современных возможностей Azure Active Directory, в том числе [условный доступ в Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal), [Защита идентификации Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) и [Azure AD Identity Governance](https://docs.microsoft.com/azure/active-directory/governance/identity-governance-overview).

На рисунке ниже показано место, которое Akamai ЕАА занимает в более обширном гибридном сценарии безопасного доступа.

![Место Akamai ЕАА в более обширном гибридном сценарии безопасного доступа](./media/header-akamai-tutorial/introduction01.png)

### <a name="key-authentication-scenarios"></a>Сценарии проверки подлинности с использованием ключа

Помимо встроенной поддержки интеграции с Azure Active Directory для современных протоколов проверки подлинности, таких как Open ID Connect, SAML и WS-Fed, Akamai EAA обеспечивает для приложений с устаревшей проверкой подлинности безопасный внутренний и внешний доступ с помощью Azure AD, что позволяет использовать для них современные сценарии (например, доступ без пароля). В том числе:

* приложения с проверкой подлинности на основе заголовков;
* Удаленный рабочий стол
* SSH (Secure Shell);
* приложения с проверкой подлинности Kerberos;
* VNC (Virtual Network Computing);
* приложения с анонимной проверкой подлинности или без встроенной проверки подлинности;
* приложения с проверкой подлинности NTLM (защита с двойным подтверждением для пользователя);
* приложение на основе форм (защита с двойным подтверждением для пользователя).

### <a name="integration-scenarios"></a>Сценарии интеграции

Партнерство между корпорацией Майкрософт и компанией Akamai в отношении решения ЕАА обеспечивает гибкие возможности для соблюдения бизнес-требований благодаря поддержке нескольких сценариев интеграции в зависимости от потребностей организации. С их помощью можно предоставить недостающие компоненты для всех приложений, а также постепенно классифицировать их и настроить соответствующие классификации политик.

#### <a name="integration-scenario-1"></a>Сценарий интеграции 1

Akamai ЕАА настраивается в Azure AD как единое приложение. Администратор может настроить Политику ЦС для приложения, а при выполнении соответствующих условий пользователи могут получить доступ к порталу Akamai ЕАА.

**Преимущества**.

* Достаточно один раз настроить поставщик удостоверений.

**Недостатки**.

* У пользователей в итоге оказывается два портала приложений.

* Для всех приложений применяется одна общая Политика ЦС.

![Сценарий интеграции 1](./media/header-akamai-tutorial/scenario1.png)

#### <a name="integration-scenario-2"></a>Сценарий интеграции 2

Приложение Akamai ЕАА настраивается отдельно на портале Azure AD. Администратор может настроить отдельные Политики ЦС для приложений, а при выполнении соответствующих условий пользователи могут напрямую перенаправляться к конкретному приложению.

**Преимущества**.

* Можно определить отдельные Политики ЦС.

* Все приложения представлены в меню запуска приложений O365 и на панели myApps.microsoft.com.


**Недостатки**.

* Необходимо настроить несколько поставщиков удостоверений.

![Сценарий интеграции 2](./media/header-akamai-tutorial/scenario2.png)

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее.

* Подписка Azure AD. Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* Подписка Akamai с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

- Akamai поддерживает единый вход, инициированный поставщиком удостоверений.

#### <a name="important"></a>Важно!

Все перечисленные ниже настройки одинаковы для сценариев интеграции **1** и **2**. Для **сценария интеграции 2** необходимо настроить отдельный поставщик удостоверений в Akamai ЕАА, а значение свойства "URL-адрес" необходимо изменить на URL-адрес приложения.

![Важно!](./media/header-akamai-tutorial/important.png)

## <a name="adding-akamai-from-the-gallery"></a>Добавление Akamai из коллекции

Чтобы настроить интеграцию Akamai с Azure AD, необходимо добавить Akamai из коллекции в список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в поле поиска введите **Akamai**.
1. Выберите **Akamai** в области результатов и добавьте это приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.

## <a name="configure-and-test-azure-ad-single-sign-on-for-akamai"></a>Настройка и проверка единого входа Azure AD для Akamai

Настройте и проверьте единый вход Azure AD в Akamai с помощью тестового пользователя **B.Simon**. Чтобы обеспечить работу единого входа, нужно установить связь между пользователем Azure AD и соответствующим пользователем в Akamai.

Чтобы настроить и проверить единый вход Azure AD в Akamai, выполните действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
    * **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD с помощью пользователя B.Simon.
    * **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить пользователю B.Simon использовать единый вход Azure AD.
1. **[Настройка единого входа в Akamai](#configure-akamai-sso)** необходима, чтобы настроить параметры единого входа на стороне приложения.
    * **[Настройка поставщика удостоверений](#setting-up-idp)**
    * **[Проверка подлинности на основе заголовков](#header-based-authentication)**
    * **[Удаленный рабочий стол](#remote-desktop)**
    * **[SSH](#ssh)**
    * **[Проверка подлинности Kerberos](#kerberos-authentication)**
    * **[Создание тестового пользователя в Akamai](#create-akamai-test-user)** нужно для того, чтобы в Akamai существовал пользователь B.Simon, связанный с представлением этого же пользователя в Azure AD.
1. **[Проверка единого входа](#test-sso)** позволяет убедиться в правильности конфигурации.

## <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Akamai** найдите раздел **Управление** и выберите **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

   ![Изменение базовой конфигурации SAML](common/edit-urls.png)

1. Если вы хотите настроить приложение в режиме, инициируемом **поставщиком удостоверений**, в разделе **Базовая конфигурация SAML** введите значения следующих полей.

    а. В текстовом поле **Идентификатор** введите URL-адрес в формате `https://<Yourapp>.login.go.akamai-access.com/saml/sp/response`.

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в формате `https:// <Yourapp>.login.go.akamai-access.com/saml/sp/response`.

    > [!NOTE]
    > Эти значения приведены для примера. Измените их на фактические значения идентификатора и URL-адреса ответа. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов Akamai](https://www.akamai.com/us/en/contact-us/). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

1. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** найдите элемент **XML метаданных федерации** и выберите **Скачать**, чтобы скачать сертификат и сохранить его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

1. Требуемые URL-адреса можно скопировать в разделе **Настройка Akamai**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

В этом разделе описано, как на портале Azure создать тестового пользователя с именем B.Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.
1. В верхней части экрана выберите **Новый пользователь**.
1. В разделе **Свойства пользователя** выполните следующие действия.
   1. В поле **Имя** введите `B.Simon`.  
   1. В поле **Имя пользователя** введите username@companydomain.extension. Например, `B.Simon@contoso.com`.
   1. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.
   1. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как включить единый вход в Azure для пользователя B.Simon, предоставив этому пользователю доступ к Akamai.

1. На портале Azure выберите **Корпоративные приложения**, а затем — **Все приложения**.
1. В списке приложений выберите **Akamai**.
1. На странице "Обзор" приложения найдите раздел **Управление** и выберите **Пользователи и группы**.

   ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Ссылка "Добавить пользователя"](common/add-assign-user.png)

1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
1. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.
1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

## <a name="configure-akamai-sso"></a>Настройка единого входа Akamai

### <a name="setting-up-idp"></a>Настройка IDP

**Конфигурация поставщика удостоверений Akamai ЕАА**

1. Войдите в консоль **Akamai Enterprise Application Access**.
1. В **консоли Akamai ЕАА** выберите **Identity** > **Identity Providers** (Удостоверение > Поставщики удостоверений) и щелкните **Add Identity Provider** (Добавить поставщик удостоверений).

    ![Настройка Akamai](./media/header-akamai-tutorial/configure01.png)

1. В разделе **Create New Identity Provider** (Создание поставщика удостоверений) выполните следующие действия.

    ![Настройка Akamai](./media/header-akamai-tutorial/configure02.png)

    а. Укажите уникальное имя в **Name** (Имя).

    b. Выберите **Third Party SAML** (SAML стороннего производителя) и щелкните **Create Identity Provider and Configure** (Создать и настроить поставщик удостоверений).

### <a name="general-settings"></a>Общие параметры

1. **Identity Intercept** (Перехват идентификаторов) — укажите имя (базовый URL-адрес поставщика служб, который будет использоваться в конфигурации Azure AD).

    > [!NOTE]
    > Вы можете выбрать собственный пользовательский домен (для этого потребуется запись DNS и сертификат). В этом примере мы будем использовать домен Akamai.

1. **Akamai Cloud Zone** (Облачная зона Akamai) — выберите соответствующую облачную зону.
1. **Certificate Validation** (Проверка сертификата) — описано в документации по Akamai (необязательно).

    ![Настройка Akamai](./media/header-akamai-tutorial/configure03.png)

### <a name="authentication-configuration"></a>Настройка проверки подлинности

1. URL-адрес — укажите URL-адрес, который вы задали для перехвата идентификаторов (именно сюда направляются пользователи после аутентификации).
2. Logout URL (URL-адрес выхода): укажите URL-адрес выхода.
3. Sign SAML Request (Подписать запрос SAML): по умолчанию этот флажок снят.
4. Для файла метаданных IDP добавьте приложение в консоли Azure AD.

    ![Настройка Akamai](./media/header-akamai-tutorial/configure04.png)

### <a name="session-settings"></a>Параметры сеанса

Оставьте параметры по умолчанию без изменений.

![Настройка Akamai](./media/header-akamai-tutorial/sessionsettings.png)

### <a name="directories"></a>Каталоги

Пропустите настройку каталога.

![Настройка Akamai](./media/header-akamai-tutorial/directories.png)

### <a name="customization-ui"></a>Пользовательский интерфейс настройки

Поставщик удостоверений можно настроить.

![Настройка Akamai](./media/header-akamai-tutorial/customizationui.png)

### <a name="advanced-settings"></a>Дополнительные параметры

Пропустите настройку дополнительных параметров. Дополнительные сведения см. в документации Akamai.

![Настройка Akamai](./media/header-akamai-tutorial/advancesettings.png)

### <a name="deployment"></a>Развертывание

1. Щелкните Deploy Identity Provider (Развернуть поставщик удостоверений).

    ![Настройка Akamai](./media/header-akamai-tutorial/deployment.png)

2. Проверьте успешность развертывания.

### <a name="header-based-authentication"></a>Проверка подлинности на основе заголовков

Проверка подлинности Akamai на основе заголовков

1. Выберите вариант **Custom HTTP** (Настраиваемый HTTP) в мастере добавления приложений.

    ![Настройка Akamai](./media/header-akamai-tutorial/configure05.png)

2. Заполните поля **Application Name** (Имя приложения) и **Description** (Описание).

    ![Настройка Akamai](./media/header-akamai-tutorial/configure06.png)

    ![Настройка Akamai](./media/header-akamai-tutorial/configure07.png)

    ![Настройка Akamai](./media/header-akamai-tutorial/configure08.png)

#### <a name="authentication"></a>Аутентификация

1. Перейдите на вкладку **Authentication** (Проверка подлинности).

    ![Настройка Akamai](./media/header-akamai-tutorial/configure09.png)

2. Назначьте **поставщик удостоверений**.

    ![Настройка Akamai](./media/header-akamai-tutorial/configure10.png)

#### <a name="services"></a>Службы

Щелкните "Save and Go to Authentication" (Сохранить и перейти к проверке подлинности).

![Настройка Akamai](./media/header-akamai-tutorial/configure11.png)

#### <a name="advanced-settings"></a>Дополнительные параметры

1. В разделе **Customer HTTP Headers** (Настраиваемые заголовки HTTP) укажите значения **CustomerHeader** и **SAML Attribute**.

    ![Настройка Akamai](./media/header-akamai-tutorial/configure12.png)

1. Нажмите кнопку **Save and go to Deployment** (Завершить и перейти к развертыванию).

    ![Настройка Akamai](./media/header-akamai-tutorial/configure13.png)

#### <a name="deploy-the-application"></a>Развертывание приложения

1. Нажмите кнопку **Deploy Application** (Развернуть приложение).

    ![Настройка Akamai](./media/header-akamai-tutorial/configure14.png)

1. Убедитесь, что приложение развернуто успешно.

    ![Настройка Akamai](./media/header-akamai-tutorial/configure15.png)

1. Проверьте взаимодействие с пользователем.

    ![Настройка Akamai](./media/header-akamai-tutorial/enduser01.png)

    ![Настройка Akamai](./media/header-akamai-tutorial/enduser02.png)

1. Проверьте условный доступ.

    ![Настройка Akamai](./media/header-akamai-tutorial/conditionalaccess01.png)

    ![Настройка Akamai](./media/header-akamai-tutorial/conditionalaccess02.png)

#### <a name="remote-desktop"></a>Удаленный рабочий стол

1. Выберите вариант **RDP** в мастере добавления приложений.

    ![Настройка Akamai](./media/header-akamai-tutorial/configure16.png)

1. Заполните поля **Application Name** (Имя приложения) и **Description** (Описание).

    ![Настройка Akamai](./media/header-akamai-tutorial/configure17.png)

    ![Настройка Akamai](./media/header-akamai-tutorial/configure18.png)

1. Укажите соединитель, который будет обслуживать это подключение.

    ![Настройка Akamai](./media/header-akamai-tutorial/configure19.png)

#### <a name="authentication"></a>Аутентификация

Щелкните **Save and Go to Services** (Сохранить и перейти к службам).

![Настройка Akamai](./media/header-akamai-tutorial/configure20.png)

#### <a name="services"></a>Службы

Щелкните **Save and go to Advanced Settings** (Сохранить и перейти к дополнительным параметрам).

![Настройка Akamai](./media/header-akamai-tutorial/configure21.png)

#### <a name="advanced-settings"></a>Дополнительные параметры

1. Щелкните **Save and go to Deployment** (Завершить и перейти к развертыванию).

    ![Настройка Akamai](./media/header-akamai-tutorial/configure22.png)

    ![Настройка Akamai](./media/header-akamai-tutorial/configure23.png)

    ![Настройка Akamai](./media/header-akamai-tutorial/configure24.png)

1. Проверьте взаимодействие с пользователем.

    ![Настройка Akamai](./media/header-akamai-tutorial/enduser03.png)

    ![Настройка Akamai](./media/header-akamai-tutorial/enduser02.png)

1. Условный доступ

    ![Настройка Akamai](./media/header-akamai-tutorial/conditionalaccess04.png)

    ![Настройка Akamai](./media/header-akamai-tutorial/conditionalaccess05.png)

    ![Настройка Akamai](./media/header-akamai-tutorial/conditionalaccess06.png)

1. Кроме того, можно непосредственно ввести URL-адрес приложения для работы по протоколу RDP.

#### <a name="ssh"></a>SSH

1. Перейдите к разделу Add Applications (Добавление приложений) и выберите **SSH**.

    ![Настройка Akamai](./media/header-akamai-tutorial/configure25.png)

1. Заполните поля **Application Name** (Имя приложения) и **Description** (Описание).

    ![Настройка Akamai](./media/header-akamai-tutorial/configure26.png)

1. Настройте удостоверение приложения.

    ![Настройка Akamai](./media/header-akamai-tutorial/configure27.png)

    а. Укажите имя и описание.

    b. Укажите IP-адрес или полное доменное имя сервера приложений и порт для подключения SSH.

    c. Укажите имя пользователя и парольную фразу "Проверка Akamai ЕАА" для SSH.

    d. Укажите значение в поле "External host Name" (Имя внешнего узла).

    д) Укажите расположение соединителя и выберите нужный соединитель.

#### <a name="authentication"></a>Аутентификация

Щелкните **Save and Go to Services** (Сохранить и перейти к службам).

![Настройка Akamai](./media/header-akamai-tutorial/configure28.png)

#### <a name="services"></a>Службы

Щелкните **Save and go to Advanced Settings** (Сохранить и перейти к дополнительным параметрам).

![Настройка Akamai](./media/header-akamai-tutorial/configure29.png)

#### <a name="advanced-settings"></a>Дополнительные параметры

Щелкните "Save and go to Deployment" (Завершить и перейти к развертыванию).

![Настройка Akamai](./media/header-akamai-tutorial/configure30.png)

![Настройка Akamai](./media/header-akamai-tutorial/configure31.png)

#### <a name="deployment"></a>Развертывание

1. Щелкните **Deploy Application** (Развернуть приложение).

    ![Настройка Akamai](./media/header-akamai-tutorial/configure32.png)

1. Проверьте взаимодействие с пользователем.

    ![Настройка Akamai](./media/header-akamai-tutorial/enduser03.png)

    ![Настройка Akamai](./media/header-akamai-tutorial/enduser04.png)

1. Условный доступ

    ![Настройка Akamai](./media/header-akamai-tutorial/conditionalaccess04.png)

    ![Настройка Akamai](./media/header-akamai-tutorial/conditionalaccess07.png)

    ![Настройка Akamai](./media/header-akamai-tutorial/conditionalaccess08.png)

    ![Настройка Akamai](./media/header-akamai-tutorial/conditionalaccess09.png)

### <a name="kerberos-authentication"></a>Проверка подлинности Kerberos

В приведенном ниже примере показано, как опубликовать внутренний веб-сервер <code>http://frp-app1.superdemo.live</code> и разрешать единый вход с применением ограниченного делегирования Kerberos.

#### <a name="general-tab"></a>Вкладка «Общие»

![Настройка Akamai](./media/header-akamai-tutorial/generaltab.png)

#### <a name="authentication-tab"></a>Вкладка "Authentication" (Аутентификация)

Назначьте поставщик удостоверений.

![Настройка Akamai](./media/header-akamai-tutorial/authenticationtab.png)

#### <a name="services-tab"></a>Вкладка Services (Службы)

![Настройка Akamai](./media/header-akamai-tutorial/servicestab.png)

#### <a name="advanced-settings"></a>Дополнительные параметры

![Настройка Akamai](./media/header-akamai-tutorial/advancesettings02.png)

> [!NOTE]
> Имя субъекта-службы для веб-сервера должно быть задано в формате SPN@Domain. Например, в данном случае это `HTTP/frp-app1.superdemo.live@SUPERDEMO.LIVE`. Оставьте для остальных параметров значение по умолчанию.

#### <a name="deployment-tab"></a>Вкладка Deployment (Развертывание)

![Настройка Akamai](./media/header-akamai-tutorial/deploymenttab.png)

#### <a name="adding-directory"></a>Добавление каталога

1. Выберите в раскрывающемся списке **AD**.

    ![Настройка Akamai](./media/header-akamai-tutorial/configure33.png)

1. Укажите необходимые данные.

    ![Настройка Akamai](./media/header-akamai-tutorial/configure34.png)

1. Проверьте создание каталога.

    ![Настройка Akamai](./media/header-akamai-tutorial/directorydomain.png)

1. Добавьте группы или подразделения, которым требуется доступ.

    ![Настройка Akamai](./media/header-akamai-tutorial/addgroup.png)

1. На изображении ниже группа называется EAAGroup и состоит из одного члена.

    ![Настройка Akamai](./media/header-akamai-tutorial/eaagroup.png)

1. Добавьте каталог для поставщика удостоверений. Для этого выберите **Identity** > **Identity Providers** (Удостоверение > Поставщики удостоверений), а затем перейдите на вкладку **Directories** (Каталоги) и щелкните **Assign directory** (Назначить каталог).

    ![Настройка Akamai](./media/header-akamai-tutorial/assigndirectory.png)

### <a name="configure-kcd-delegation-for-eaa-walkthrough"></a>Пошаговое руководство по настройке ограниченного делегирования Kerberos для ЕАА

#### <a name="step-1-create-an-account"></a>Шаг 1. Создание учетной записи 

1. В этом примере мы будем использовать учетную запись с именем **EAADelegation**. Это можно сделать с помощью оснастки **Пользователи и компьютеры Active Directory**.

    ![Настройка Akamai](./media/header-akamai-tutorial/assigndirectory.png)

    > [!NOTE]
    > Имя пользователя должно быть указано в определенном формате и составлено на основе имени, которое задано в поле **Identity Intercept** (Перехват идентификаторов). На рис. 1 показано **corpapps.login.go.akamai-access.com**.

1. Именем для входа пользователя будет `HTTP/corpapps.login.go.akamai-access.com`.

    ![Настройка Akamai](./media/header-akamai-tutorial/eaadelegation.png)

#### <a name="step-2-configure-the-spn-for-this-account"></a>Шаг 2. Настройка имени субъекта-службы для этой учетной записи

1. Для данного примера имя субъекта-службы будет следующим:

2. setspn -s **Http/corpapps.login.go.akamai-access.com eaadelegation**

    ![Настройка Akamai](./media/header-akamai-tutorial/spn.png)

#### <a name="step-3-configure-delegation"></a>Шаг 3. Настройка делегирования

1. Для учетной записи EAADelegation перейдите на вкладку Delegation (Делегирование).

    ![Настройка Akamai](./media/header-akamai-tutorial/spn.png)

    * Укажите, что можно использовать любой протокол проверки подлинности.
    * Нажмите Add (Добавить) и добавьте учетную запись пула приложений для веб-сайта Kerberos. Если настройки заданы правильно, она должна автоматически разрешаться в правильное имя субъекта-службы.

#### <a name="step-4-create-a-keytab-file-for-akamai-eaa"></a>Шаг 4. Создание файла keytab для Akamai ЕАА

1. Ниже приведен общий синтаксис.

1. ktpass /out ActiveDirectorydomain.keytab /princ `HTTP/yourloginportalurl@ADDomain.com` /mapuser serviceaccount@ADdomain.com /pass +rdnPass /crypto All /ptype KRB5_NT_PRINCIPAL

1. Пояснения к примеру

    | Фрагмент кода | Объяснение |
    | - | - |
    | Ktpass /out EAADemo.keytab | // Имя выходного файла keytab |
    | /princ HTTP/corpapps.login.go.akamai-access.com@superdemo.live | // HTTP/yourIDPName@YourdomainName |
    | /mapuser eaadelegation@superdemo.live | // Учетная запись делегирования EAA |
    | /pass RANDOMPASS | // Пароль учетной записи делегирования EAA |
    | /crypto All ptype KRB5_NT_PRINCIPAL | // См. в документации по Akamai ЕАА |
    | | |

1. Ktpass /out EAADemo.keytab /princ HTTP/corpapps.login.go.akamai-access.com@superdemo.live /mapuser eaadelegation@superdemo.live /pass RANDOMPASS /crypto All ptype KRB5_NT_PRINCIPAL

    ![Настройка Akamai](./media/header-akamai-tutorial/administrator.png)

#### <a name="step-5-import-keytab-in-the-akamai-eaa-console"></a>Шаг 5. Импорт keytab в консоли Akamai ЕАА

1. Щелкните **System** > **Keytabs** (Система > Файлы keytab).

    ![Настройка Akamai](./media/header-akamai-tutorial/keytabs.png)

1. В поле Keytab Type (Тип keytab) выберите **Kerberos Delegation** (Делегирование Kerberos).

    ![Настройка Akamai](./media/header-akamai-tutorial/keytabdelegation.png)

1. Убедитесь, что файл keytab отображается как развернутый и проверенный.

    ![Настройка Akamai](./media/header-akamai-tutorial/keytabs02.png)

1. Проверьте взаимодействие с пользователем.

    ![Настройка Akamai](./media/header-akamai-tutorial/enduser03.png)

    ![Настройка Akamai](./media/header-akamai-tutorial/enduser04.png)

1. Условный доступ

    ![Настройка Akamai](./media/header-akamai-tutorial/conditionalaccess04.png)

    ![Настройка Akamai](./media/header-akamai-tutorial/conditionalaccess10.png)

    ![Настройка Akamai](./media/header-akamai-tutorial/conditionalaccess11.png)

### <a name="create-akamai-test-user"></a>Создание тестового пользователя Akamai

Из этого разделе вы узнаете, как создать пользователя B.Simon в приложении Akamai. Обратитесь в [службу поддержки клиентов Akamai](https://www.akamai.com/us/en/contact-us/), чтобы добавить пользователей на платформу Akamai. Перед использованием единого входа необходимо создать и активировать пользователей. 

## <a name="test-sso"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Akamai на панели доступа, вы автоматически войдете в приложение Akamai, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Попробуйте использовать Akamai с Azure AD](https://aad.portal.azure.com/)
