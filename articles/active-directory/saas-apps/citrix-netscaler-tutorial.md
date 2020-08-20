---
title: Учебник. Интеграция единого входа Azure Active Directory с Citrix NetScaler (проверка подлинности Kerberos) | Документация Майкрософт
description: Узнайте, как настроить единый вход (SSO) между Azure Active Directory и Citrix NetScaler с помощью проверки подлинности Kerberos.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2020
ms.author: jeedes
ms.openlocfilehash: 4ff6154e17408b9e2daaf3c81321ae31693de3aa
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88544610"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-citrix-netscaler-kerberos-based-authentication"></a>Учебник. Интеграция единого входа Azure Active Directory с Citrix NetScaler (проверка подлинности Kerberos)

В этом учебнике описано, как интегрировать Citrix NetScaler с Azure Active Directory (Azure AD). Интеграция платформы Citrix NetScaler с Azure AD обеспечивает следующие возможности:

* Контроль доступа к Citrix NetScaler с помощью Azure AD.
* Автоматический вход пользователей на платформу Citrix NetScaler с учетными записями Azure AD.
* Централизованное управление учетными записями через портал Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье о [едином входе в приложения с помощью Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее.

* Подписка Azure AD. Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* Подписка Citrix NetScaler с поддержкой единого входа (SSO).

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде. Этот учебник включает в себя следующие сценарии:

* Единый вход для Citrix NetScaler, **инициированный поставщиком услуг**.

* **JIT**-подготовка пользователей для Citrix NetScaler.

* [Проверка подлинности на основе Kerberos для Citrix NetScaler](#publish-the-web-server).

* [Проверка подлинности на основе заголовка для Citrix NetScaler](header-citrix-netscaler-tutorial.md#publish-the-web-server).

* После настройки Citrix NetScaler вы можете применить функцию управления сеансом, которая защищает от хищения конфиденциальных данных вашей организации и несанкционированного доступа к ним в реальном времени. Управление сеансом является расширением функции условного доступа. [Узнайте, как применять управление сеансами с помощью Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-citrix-netscaler-from-the-gallery"></a>Добавление Citrix NetScaler из коллекции

Чтобы интегрировать Citrix NetScaler с Azure AD, сначала добавьте Citrix NetScaler в список управляемых приложений SaaS из коллекции:

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.

1. В меню слева выберите **Azure Active Directory**.

1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

1. Чтобы добавить новое приложение, выберите **Новое приложение**.

1. В разделе **Добавление из коллекции** в поле поиска введите **Citrix NetScaler**.

1. Выберите **Citrix NetScaler** в области результатов и добавьте это приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.

## <a name="configure-and-test-azure-ad-single-sign-on-for-citrix-netscaler"></a>Настройка и проверка единого входа Azure AD для Citrix NetScaler

Настройте и проверьте единый вход Azure AD в Citrix NetScaler с помощью тестового пользователя **B.Simon**. Чтобы обеспечить работу единого входа, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Citrix NetScaler.

Чтобы настроить и проверить единый вход Azure AD в Citrix NetScaler, выполните действия в следующих стандартных блоках:

1. [Настройка единого входа Azure AD](#configure-azure-ad-sso) необходима, чтобы пользователи могли использовать эту функцию.

    1. [Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user) позволяет проверить работу единого входа Azure AD для пользователя B.Simon.

    1. [Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user) необходимо, чтобы пользователь B.Simon мог использовать единый вход Azure AD.

1. [Настройка единого входа в Citrix NetScaler](#configure-citrix-netscaler-sso) необходима, чтобы настроить параметры единого входа на стороне приложения.

    * [Создание тестового пользователя Citrix NetScaler](#create-a-citrix-netscaler-test-user) требуется для создания в Citrix NetScaler пользователя B.Simon, связанного с одноименным пользователем в Azure AD.

1. [Проверка единого входа](#test-sso) позволяет убедиться в правильности конфигурации.

## <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Чтобы включить единый вход Azure AD на портале Azure, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com/) в области интеграции с приложением **Citrix NetScaler** в разделе **Управление** выберите **Единый вход**.

1. В области **Выбрать метод единого входа** выберите **SAML**.

1. В области **Настройка единого входа с помощью SAML** щелкните значок пера **Правка**, чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить эти параметры.

   ![Изменение базовой конфигурации SAML](common/edit-urls.png)

1. Если вы хотите настроить приложение в режиме, **инициируемом поставщиком удостоверений**, в разделе **Базовая конфигурация SAML** выполните следующие действия.

    1. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<Your FQDN>`

    1. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `http(s)://<Your FQDN>.of.vserver/cgi/samlauth`

1. Если вы хотите настроить приложение для работы в режиме, **инициируемом поставщиком услуг**, выберите **Задать дополнительные URL-адреса** и выполните следующие действия:

    * В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<Your FQDN>/CitrixAuthService/AuthService.asmx`

    > [!NOTE]
    > * URL-адреса, используемые в этом разделе, не являются реальными значениями. Замените их фактическими значениями для идентификатора, URL-адреса ответа и URL-адреса входа. Чтобы получить эти значения, обратитесь в [группу поддержки клиентов Citrix NetScaler](https://www.citrix.com/contact/technical-support.html). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.
    > * Чтобы настроить единый вход, URL-адреса должны быть доступны с общедоступных веб-сайтов. Необходимо настроить параметры брандмауэра или безопасности на стороне Citrix NetScaler, чтобы разрешить Azure AD отправлять маркер на указанный URL-адрес.

1. На панели **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** найдите **URL-адрес метаданных федерации приложений**, скопируйте его и сохраните в Блокноте.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

1. Скопируйте требуемые URL-адреса в разделе **Настройка Citrix NetScaler**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

В этом разделе описано, как на портале Azure создать тестового пользователя с именем B.Simon.

1. На портале Azure в меню слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

1. В верхней части панели выберите **Новый пользователь**.

1. В свойствах **пользователя** выполните следующие действия:

   1. Для параметра **Имя** введите `B.Simon`.  

   1. В поле **Имя пользователя** введите значение _username@companydomain.extension_ . Например, `B.Simon@contoso.com`.

   1. Установите флажок **Показать пароль** и запишите или скопируйте значение, которое отображается в поле **Пароль**.

   1. Нажмите кнопку **создания**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю B.Simon использовать единый вход Azure, предоставив этому пользователю доступ к Citrix NetScaler.

1. На портале Azure выберите **Корпоративные приложения**, а затем — **Все приложения**.

1. В списке приложений выберите **Citrix NetScaler**.

1. В обзоре приложения в разделе **Управление** выберите **Пользователи и группы**.

   ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Ссылка "Добавить пользователя"](common/add-assign-user.png)

1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** из списка **Пользователи**. Щелкните **Выбрать**.

1. Если вы хотите получить значение роли в утверждении SAML, выберите в диалоговом окне **Выбор роли** подходящую роль для пользователя в предложенном списке, а затем щелкните **Выбрать**.

1. В диалоговом окне **Добавление назначения** выберите **Назначить**.

## <a name="configure-citrix-netscaler-sso"></a>Настройка единого входа в Citrix NetScaler

Выберите ссылку для необходимого типа проверки подлинности:

- [Настройка единого входа с помощью Citrix NetScaler для проверки подлинности на основе Kerberos](#publish-the-web-server)

- [Настройка единого входа с помощью Citrix NetScaler для проверки подлинности на основе заголовков](header-citrix-netscaler-tutorial.md#publish-the-web-server)

### <a name="publish-the-web-server"></a>Публикация веб-сервера 

Чтобы создать виртуальный сервер, сделайте следующее:

1. Выберите **Traffic Management** (Управление трафиком) > **Балансировка нагрузки** > **Службы**.
    
1. Выберите **Добавить**.

    ![Настройка Citrix NetScaler — панель служб](./media/citrix-netscaler-tutorial/web01.png)

1. Задайте следующие значения для веб-сервера, на котором выполняются приложения:

   * **имя службы**;
   * **IP-адрес сервера и существующий сервер**;
   * **протокол**;
   * **порт**.

### <a name="configure-the-load-balancer"></a>Настройка подсистемы балансировки нагрузки

Чтобы настроить подсистему балансировки нагрузки, сделайте следующее.

1. Выберите **Traffic Management** (Управление трафиком) > **Балансировка нагрузки** > **Виртуальные серверы**.

1. Выберите **Добавить**.

1. Задайте следующие значения, как показано на следующем снимке экрана:

    * **имя**;
    * **протокол**;
    * **IP-адрес**;
    * **порт**.

1. Щелкните **ОК**.

    ![Настройка Citrix NetScaler — панель "Основные параметры"](./media/citrix-netscaler-tutorial/load01.png)

### <a name="bind-the-virtual-server"></a>Привязка виртуального сервера

Чтобы привязать подсистему балансировки нагрузки к виртуальному серверу, сделайте следующее:

1. В области **Services and Service Groups** (Службы и группы служб) выберите пункт **No Load Balancing Virtual Server Service Binding** (Нет привязки к службе виртуальных серверов для балансировки нагрузки).

   ![Настройка Citrix NetScaler — панель привязки к службе виртуального сервера балансировки нагрузки](./media/citrix-netscaler-tutorial/bind01.png)

1. Проверьте, чтобы параметры были установлены, как показано на следующем снимке экрана, а затем выберите **Close** (Закрыть).

   ![Настройка Citrix NetScaler — проверка привязки к службе виртуального сервера](./media/citrix-netscaler-tutorial/bind02.png)

### <a name="bind-the-certificate"></a>Привязка сертификата

Чтобы опубликовать эту службу как службу TLS, привяжите сертификат сервера, а затем протестируйте приложение.

1. В разделе **Certificate** (Сертификат) выберите **No Server Certificate** (Без сертификата сервера).

   ![Настройка Citrix NetScaler — панель "Сертификат сервера"](./media/citrix-netscaler-tutorial/bind03.png)

1. Проверьте, чтобы параметры были установлены, как показано на следующем снимке экрана, а затем выберите **Close** (Закрыть).

   ![Настройка Citrix NetScaler — проверка сертификата](./media/citrix-netscaler-tutorial/bind04.png)

## <a name="citrix-adc-saml-profile"></a>Профиль SAML для Citrix ADC

Чтобы настроить профиль SAML для Citrix ADC, выполните действия в следующих разделах.

### <a name="create-an-authentication-policy"></a>Создание политики проверки подлинности

Чтобы создать политику проверки подлинности, сделайте следующее:

1. Выберите **Security** (Безопасность) > **AAA – Application Traffic** (AAA — трафик приложений) > **Policies** (Политики) > **Authentication** (Проверка подлинности) > **Authentication Policies** (Политики проверки подлинности).

1. Выберите **Добавить**.

1. На панели **Create Authentication Policy** (Создание политики проверки подлинности) введите или выберите следующие значения:

    * **Name** (Имя). Введите имя политики проверки подлинности.
    * **Action** (Действие). Введите **SAML**, а затем выберите **Add** (Добавить).
    * **Expression** (Выражение).  Введите **true**.     
    
    ![Настройка Citrix NetScaler — область создания политики проверки подлинности](./media/citrix-netscaler-tutorial/policy01.png)

1. Нажмите кнопку **создания**.

### <a name="create-an-authentication-saml-server"></a>Создание сервера SAML для проверки подлинности

Чтобы создать сервер SAML для проверки подлинности, перейдите на панель **Create Authentication SAML Server** (Создание сервера SAML для проверки подлинности), а затем выполните следующие действия:

1. В поле **Name** (Имя) введите имя сервера SAML для проверки подлинности.

1. В разделе **Export SAML Metadata** (Экспорт метаданных SAML):

   1. Установите флажок **Import Metadata** (Импортировать метаданные).

   1. Введите URL-адрес метаданных федерации из пользовательского интерфейса SAML Azure, скопированного ранее.
    
1. В поле **Issuer Name** (Имя поставщика) введите соответствующий URL-адрес.

1. Нажмите кнопку **создания**.

![Настройка Citrix NetScaler — панель создания сервера SAML для проверки подлинности](./media/citrix-netscaler-tutorial/server01.png)

### <a name="create-an-authentication-virtual-server"></a>Создание виртуального сервера проверки подлинности

Чтобы создать виртуальный сервер проверки подлинности, сделайте следующее:

1.  Выберите **Security** (Безопасность) > **AAA - Application Traffic** (AAA — трафик приложений) > **Policies** (Политики) > **Authentication** (Проверка подлинности) > **Authentication Virtual Servers** (Виртуальные серверы проверки подлинности).

1.  Выберите **Add** (Добавить) и выполните следующие действия:

    1. В поле **Name** (Имя) введите имя для виртуального сервера проверки подлинности.

    1. Установите флажок **Non-Addressable** (Без адресации).

    1. Для параметра **Protocol** (Протокол) выберите значение **SSL**.

    1. Нажмите кнопку **ОК**.
    
1. Выберите **Continue** (Продолжить).

### <a name="configure-the-authentication-virtual-server-to-use-azure-ad"></a>Настройка виртуального сервера проверки подлинности для использования Azure AD

Измените два раздела для виртуального сервера проверки подлинности.

1.  На панели **Advanced Authentication Policies** (Расширенные политики проверки подлинности) выберите **No Authentication Policy** (Без политики проверки подлинности).

    ![Настройка Citrix NetScaler — панель расширенных политик проверки подлинности](./media/citrix-netscaler-tutorial/virtual01.png)

1. На панели **Policy Binding** (Привязка политики) выберите политику проверки подлинности, а затем нажмите кнопку **Bind** (Привязать).

    ![Настройка Citrix NetScaler — панель привязки политики](./media/citrix-netscaler-tutorial/virtual02.png)

1. На панели **Form Based Virtual Servers** (Виртуальные серверы на основе формы) выберите **No Load Balancing Virtual Server** (Виртуальный сервер без балансировки нагрузки).

    ![Настройка Citrix NetScaler — панель виртуальных серверов на основе формы](./media/citrix-netscaler-tutorial/virtual03.png)

1. В поле **Authentication FQDN** (Полное доменное имя для проверки подлинности) введите полное доменное имя (обязательно).

1. Выберите виртуальный сервер балансировки нагрузки, который требуется защитить с помощью проверки подлинности Azure AD.

1. Нажмите кнопку **Bind** (Привязать).

    ![Настройка Citrix NetScaler — панель привязки виртуального сервера для балансировки нагрузки](./media/citrix-netscaler-tutorial/virtual04.png)

    > [!NOTE]
    > Обязательно нажмите кнопу **Done** (Готово) на панели **Authentication Virtual Server Configuration** (Конфигурация виртуального сервера проверки подлинности).

1. Чтобы проверить изменения, в браузере перейдите по URL-адресу приложения. Должна отобразиться страница входа клиента вместо страницы без запроса проверки подлинности, которая открывалась ранее.

    ![Настройка Citrix NetScaler — страница входа в веб-браузере](./media/citrix-netscaler-tutorial/virtual05.png)

## <a name="configure-citrix-netscaler-sso-for-kerberos-based-authentication"></a>Настройка единого входа с помощью Citrix NetScaler для проверки подлинности на основе Kerberos

### <a name="create-a-kerberos-delegation-account-for-citrix-adc"></a>Создание учетной записи делегирования Kerberos для Citrix ADC

1. Создайте учетную запись пользователя (_AppDelegation_ в нашем примере).

    ![Настройка Citrix NetScaler — панель свойств](./media/citrix-netscaler-tutorial/kerberos01.png)

1. Настройте имя субъекта-службы узла для этой учетной записи. 

    Например, `setspn -S HOST/AppDelegation.IDENTT.WORK identt\appdelegation`.
    
    В данном примере:

    * `IDENTT.WORK`: полное доменное имя.
    * `identt`: NetBIOS-имя домена.
    * `appdelegation`: имя учетной записи пользователя для делегирования.

1. Настройте делегирование для веб-сервера, как показано на следующем снимке экрана:
 
    ![Настройка Citrix NetScaler — вкладка делегирования на панели свойств](./media/citrix-netscaler-tutorial/kerberos02.png)

    > [!NOTE]
    > В примере снимка экрана внутреннее имя веб-сервера, где выполняется сайт встроенной проверки подлинности Windows (WIA), — _CWEB2_.

### <a name="citrix-netscaler-aaa-kcd-kerberos-delegation-accounts"></a>Citrix NetScaler AAA KCD (учетные записи делегирования Kerberos)

Чтобы настроить учетную запись Citrix NetScaler AAA KCD, сделайте следующее:

1.  Выберите **Citrix Gateway** (Шлюз Citrix) > **AAA KCD (Kerberos Constrained Delegation) Accounts** (Учетные записи AAA KCD (ограниченное делегирование Kerberos)).

1.  Выберите **Add** (Добавить), а затем введите или выберите следующие значения:

    * **Name** (Имя). Введите имя для учетной записи KCD.

    * **Realm** (Область). Введите домен и расширение прописными буквами.

    * **Service SPN** (Имя субъекта-службы): `http/<host/fqdn>@<DOMAIN.COM>`.
    
        > [!NOTE]
        > `@DOMAIN.COM` нужно обязательно добавлять в формате с прописными буквами. Например, `http/cweb2@IDENTT.WORK`.

    * **Delegated User** (Делегированный пользователь). Введите имя делегированного пользователя.

    * Установите флажок **Password for Delegated User** (Пароль для делегированного пользователя), а затем введите и подтвердите пароль.

1. Нажмите кнопку **ОК**.
 
    ![Настройка Citrix NetScaler — область конфигурации учетных записей KCD](./media/citrix-netscaler-tutorial/kerberos03.png)

### <a name="citrix-traffic-policy-and-traffic-profile"></a>Политика и профиль трафика Citrix

Чтобы настроить политику трафика Citrix и профиль трафика, сделайте следующее:

1.  Go to **Security** (Безопасность) > **AAA - Application Traffic** (AAA — трафик приложений) > **Policies** (Политики) > **Traffic Policies, Profiles and Form SSO ProfilesTraffic Policies** (Политики трафика, профили и создание политик трафика профилей для единого входа).

1.  Выберите **Traffic Profiles** (Профили трафика).

1.  Выберите **Добавить**.

1.  Чтобы настроить профиль трафика, введите или выберите следующие значения.

    * **Name** (Имя). Введите имя для профиля трафика.

    * **Single Sign-on** (Единый вход). Выберите **ON** (Вкл.).

    * **KCD Account** (Учетная запись KCD). Выберите учетную запись KCD, созданную в предыдущем разделе.

1. Нажмите кнопку **ОК**.

    ![Настройка Citrix NetScaler — область настройки профиля трафика](./media/citrix-netscaler-tutorial/kerberos04.png)
 
1.  Выберите **Traffic Policy** (Политика трафика).

1.  Выберите **Добавить**.

1.  Чтобы настроить политику трафика, введите или выберите следующие значения:

    * **Name** (Имя). Введите имя для политики трафика.

    * **Profile** (Профиль). Выберите профиль трафика, созданный в предыдущем разделе.

    * **Expression** (Выражение). Введите **true**.

1. Нажмите кнопку **ОК**.

    ![Настройка Citrix NetScaler — область настройки политики трафика](./media/citrix-netscaler-tutorial/kerberos05.png)

### <a name="bind-a-traffic-policy-to-a-virtual-server-in-citrix"></a>Привязка политики трафика к виртуальному серверу в Citrix

Чтобы привязать политику трафика к виртуальному серверу с помощью графического пользовательского интерфейса, сделайте следующее:

1. Выберите **Traffic Management** (Управление трафиком) > **Балансировка нагрузки** > **Виртуальные серверы**.

1. В списке виртуальных серверов выберите виртуальный сервер, к которому требуется привязать политику перезаписи, а затем выберите **Open** (Открыть).

1. В области **Load Balancing Virtual Server** (Виртуальный сервер балансировки нагрузки) в разделе **Advanced Settings** (Расширенные параметры) выберите **Policies** (Политики). Все политики, настроенные для экземпляра NetScaler, отображаются в списке.
 
    ![Настройка Citrix NetScaler — панель виртуального сервера балансировки нагрузки](./media/citrix-netscaler-tutorial/kerberos06.png)

    ![Настройка Citrix NetScaler — диалоговое окно политик](./media/citrix-netscaler-tutorial/kerberos07.png)

1.  Установите флажок рядом с именем политики, которую вы хотите связать с этим виртуальным сервером.
 
    ![Настройка Citrix NetScaler — панель привязки политики трафика виртуального сервера для балансировки нагрузки](./media/citrix-netscaler-tutorial/kerberos09.png)

1. В диалоговом окне **Choose Type** (Выбор типа) выполните следующее:

    1. Для параметра **Choose Policy** (Выберите политику) выберите **Traffic** (Трафик).

    1. Для параметра **Choose Type** (Выберите тип) выберите **Request** (Запрос).

    ![Настройка Citrix NetScaler — область выбора типа](./media/citrix-netscaler-tutorial/kerberos08.png)

1. Когда политика будет привязана, нажмите кнопку **Done** (Готово).
 
    ![Настройка Citrix NetScaler — панель "Политики"](./media/citrix-netscaler-tutorial/kerberos10.png)

1. Протестируйте привязку с помощью веб-сайта WIA.

    ![Настройка Citrix NetScaler — тестовая страница в веб-браузере](./media/citrix-netscaler-tutorial/kerberos11.png)    

### <a name="create-a-citrix-netscaler-test-user"></a>Создание тестового пользователя Citrix NetScaler

В этом разделе описано, как создать в Citrix NetScaler пользователя с именем B.Simon. Приложение Citrix NetScaler поддерживает JIT-подготовку пользователей, которая включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Если пользователь еще не существует в Citrix NetScaler, он создается после аутентификации.

> [!NOTE]
> Если вам нужно создать пользователя вручную, обратитесь в [группу поддержки клиентов Citrix NetScaler](https://www.citrix.com/contact/technical-support.html).

## <a name="test-sso"></a>Проверка единого входа 

В этом разделе вы с помощью панели доступа выполните проверку конфигурации единого входа Azure AD.

Щелкнув плитку Citrix NetScaler на панели доступа, вы автоматически войдете в приложение Citrix NetScaler, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Тестирование работы Citrix NetScaler с Azure AD](https://aad.portal.azure.com/)

- [Настройка единого входа Citrix NetScaler для проверки подлинности на основе заголовка](header-citrix-netscaler-tutorial.md)

- [Что такое управление сеансами в Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Защита приложения Citrix NetScaler с помощью функции управления настройками условного доступа](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)