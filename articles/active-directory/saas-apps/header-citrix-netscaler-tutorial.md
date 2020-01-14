---
title: Руководство. Интеграция единого входа Azure Active Directory с Citrix NetScaler (проверка подлинности на основе заголовков) | Документация Майкрософт
description: Из этой статьи вы узнаете, как настроить единый вход между Azure Active Directory и Citrix NetScaler.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b0adc7bf-696d-44c9-a57a-f9e9471b8710
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9c442ca731ecb10f977c19b86cb32caded36659
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75470539"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-citrix-netscaler-header-based-authentication"></a>Руководство. Интеграция единого входа Azure Active Directory с Citrix NetScaler (проверка подлинности на основе заголовков)

В этом руководстве описано, как интегрировать Citrix NetScaler с Azure Active Directory (Azure AD). Интеграция платформы Citrix NetScaler с Azure AD обеспечивает следующие возможности:

* Контроль доступа к Citrix NetScaler с помощью Azure AD.
* Возможность включить автоматический вход пользователей на платформу Citrix NetScaler с учетными записями Azure AD.
* Централизованное управление учетными записями через портал Azure.

Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>предварительные требования

Чтобы приступить к работе, потребуется следующее:

* подписка Azure AD; Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* Подписка Citrix NetScaler с поддержкой единого входа (SSO).

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Citrix NetScaler поддерживает единый вход, инициированный **поставщиком услуг**.

* Citrix NetScaler поддерживает **JIT-подготовку** пользователей.

- [Настройка проверки подлинности на основе заголовка для единого входа в Citrix NetScaler](#configure-citrix-netscaler-single-sign-on-for-header-based-authentication)

- [Настройка проверки подлинности Kerberos для единого входа в Citrix NetScaler](citrix-netscaler-tutorial.md)


## <a name="adding-citrix-netscaler-from-the-gallery"></a>Добавление Citrix NetScaler из коллекции

Чтобы настроить интеграцию Citrix NetScaler с Azure AD, вам нужно добавить Citrix NetScaler из коллекции в список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в поле поиска введите **Citrix NetScaler**.
1. Выберите **Citrix NetScaler** в области результатов и добавьте это приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.


## <a name="configure-and-test-azure-ad-single-sign-on-for-citrix-netscaler"></a>Настройка и проверка единого входа Azure AD для Citrix NetScaler

Настройте и проверьте единый вход Azure AD в Citrix NetScaler с помощью тестового пользователя **B.Simon**. Чтобы обеспечить работу единого входа, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Citrix NetScaler.

Чтобы настроить и проверить единый вход Azure AD в Citrix NetScaler, выполните действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
    1. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD с помощью пользователя B.Simon.
    1. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить пользователю B.Simon использовать единый вход Azure AD.
1. **[Настройка единого входа в Citrix NetScaler](#configure-citrix-netscaler-sso)** необходима, чтобы настроить параметры единого входа на стороне приложения.
    1. **[Создание тестового пользователя Citrix NetScaler](#create-citrix-netscaler-test-user)** требуется для создания в Citrix NetScaler пользователя B.Simon, связанного с одноименным пользователем в Azure AD.
1. **[Проверка единого входа](#test-sso)** необходима, чтобы убедиться в корректной работе конфигурации.

## <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Citrix NetScaler** найдите раздел **Управление** и выберите **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

   ![Правка базовой конфигурации SAML](common/edit-urls.png)

1. Если вы хотите настроить приложение в режиме, инициируемом **поставщиком удостоверений**, в разделе **Базовая конфигурация SAML** введите значения следующих полей.

    а. В текстовом поле **Идентификатор** введите URL-адрес в формате `https://<<Your FQDN>>`.

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в формате `https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`.

1. Чтобы настроить приложение для работы в режиме, инициируемом **поставщиком услуг**, щелкните **Задать дополнительные URL-адреса** и выполните следующие действия.

    В текстовом поле **URL-адрес входа** введите URL-адрес в формате `https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`.

    > [!NOTE]
    > Эти значения приведены для примера. Укажите вместо них фактические значения URL-адреса для входа, идентификатора и URL-адреса ответа. Чтобы получить эти значения, обратитесь в [группу поддержки клиентов Citrix NetScaler](https://www.citrix.com/contact/technical-support.html). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

    > [!NOTE]
    > Чтобы единый вход работал правильно, эти URL-адреса должны быть доступны с общедоступных сайтов. Необходимо настроить параметры брандмауэра или безопасности на стороне Netscaler, чтобы разрешить Azure AD отправлять маркер на указанный выше URL-адрес ACS.

1. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** найдите **URL-адрес метаданных федерации приложений**, скопируйте его и сохраните в Блокноте.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

1. Приложение Citrix NetScaler ожидает проверочные утверждения SAML в определенном формате, который требует добавить настраиваемые сопоставления атрибутов в вашу конфигурацию атрибутов токена SAML. На следующем снимке экрана показан список атрибутов по умолчанию. Щелкните **Изменить** и измените сопоставление атрибутов.

    ![image](common/edit-attribute.png)

1. В дополнение к описанному выше приложение Citrix NetScaler ожидает несколько дополнительных атрибутов в ответе SAML. В разделе "Утверждения пользователя" диалогового окна "Атрибуты пользователя" выполните следующие действия, чтобы добавить атрибут токена SAML, как показано в приведенной ниже таблице.

    | Имя | Исходный атрибут|
    | ---------------| --------------- |
    | mySecretID  | user.userprincipalname |
    
    1. Щелкните **Добавить новое утверждение**, чтобы открыть диалоговое окно **Управление утверждениями пользователя**.

    1. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.

    1. Оставьте пустым поле **Пространство имен**.

    1. В качестве источника выберите **Атрибут**.

    1. В списке **Атрибут источника** введите значение атрибута, отображаемое для этой строки.

    1. Нажмите кнопку **ОК**.

    1. Выберите команду **Сохранить**.


1. Скопируйте требуемые URL-адреса в разделе **Настройка Citrix NetScaler**.

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

В этом разделе описано, как разрешить пользователю B.Simon использовать единый вход Azure, предоставив этому пользователю доступ к Citrix NetScaler.

1. На портале Azure выберите **Корпоративные приложения**, а затем —**Все приложения**.
1. В списке приложений выберите **Citrix NetScaler**.
1. На странице "Обзор" приложения найдите раздел **Управление** и выберите **Пользователи и группы**.

   ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Ссылка "Добавить пользователя"](common/add-assign-user.png)

1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
1. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.
1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

## <a name="configure-citrix-netscaler-sso"></a>Настройка единого входа в Citrix NetScaler

- [Настройка проверки подлинности на основе заголовка для единого входа в Citrix NetScaler](#configure-citrix-netscaler-single-sign-on-for-header-based-authentication)

- [Настройка проверки подлинности Kerberos для единого входа в Citrix NetScaler](citrix-netscaler-tutorial.md)

### <a name="publishing-web-server"></a>Публикация веб-сервера 

1. Создайте **виртуальный сервер**.

    а. Выберите **Управление трафиком > Балансировка нагрузки > Службы**.
    
    b. Нажмите кнопку **Добавить**.

    ![Конфигурация Citrix NetScaler](./media/header-citrix-netscaler-tutorial/web01.png)

    c. Укажите следующие сведения о веб-сервере, на котором выполняются приложения:
    * **Имя службы**
    * **IP-адрес сервера и существующий сервер**
    * **Протокол**
    * **Порт**

     ![Конфигурация Citrix NetScaler](./media/header-citrix-netscaler-tutorial/web01.png)

### <a name="configuring-load-balancer"></a>Настройка Load Balancer

1. Чтобы настроить Load Balancer, сделайте следующее:

    ![Конфигурация Citrix NetScaler](./media/header-citrix-netscaler-tutorial/load01.png)

    а. Выберите **Traffic Management > Load Balancing > Virtual Servers** (Управление трафиком > Балансировка нагрузки > Виртуальные серверы).

    b. Нажмите кнопку **Добавить**.

    c. Укажите следующие дополнительные сведения:

    * **Название**
    * **Протокол**
    * **IP-адрес**
    * **Порт**
    * Щелкните **ОК**.

### <a name="bind-virtual-server"></a>Привязка виртуального сервера

Привяжите Load Balancer к созданному ранее виртуальному серверу.

![Конфигурация Citrix NetScaler](./media/header-citrix-netscaler-tutorial/bind01.png)

![Конфигурация Citrix NetScaler](./media/header-citrix-netscaler-tutorial/bind02.png)

### <a name="bind-certificate"></a>Привязка сертификата

Так как мы будем публиковать эту службу как SSL, привяжите сертификат сервера и протестируйте приложение.

![Конфигурация Citrix NetScaler](./media/header-citrix-netscaler-tutorial/bind03.png)

![Конфигурация Citrix NetScaler](./media/header-citrix-netscaler-tutorial/bind04.png)

## <a name="citrix-adc-saml-profile"></a>Профиль SAML для Citrix ADC

### <a name="create-authentication-policy"></a>Создание политики проверки подлинности

1. Выберите **Security > AAA – Application Traffic > Policies > Authentication > Authentication Policies** (Безопасность > AAA — трафик приложений > Политики > Аутентификация > Политики проверки подлинности).

2. Щелкните **Add** (Добавить) и укажите сведения.

    ![Конфигурация Citrix NetScaler](./media/header-citrix-netscaler-tutorial/policy01.png)

    а. Имя **политики проверки подлинности**.

    b. Выражение: **true**.

    c. Тип действия **SAML**.

    d. Действие — щелкните **Add** (Добавить) и следуйте указаниям мастера создания сервера SAML для проверки подлинности.
    
    д) Щелкните Create (Создать) в разделе **Authentication Policy** (Политика проверки подлинности).

### <a name="create-authentication-saml-server"></a>Создание сервера SAML для проверки подлинности

1. Выполните следующие действия:

    ![Конфигурация Citrix NetScaler](./media/header-citrix-netscaler-tutorial/server01.png)

    а. Укажите **имя**.

    b. Импорт метаданных (укажите URL-адрес метаданных федерации, скопированный ранее в пользовательском интерфейсе Azure SAML).
    
    c. Укажите **имя поставщика**.

    d. Щелкните **Create** (Создать).

### <a name="create-authentication-virtual-server"></a>Создание виртуального сервера проверки подлинности

1.  Выберите **Security > AAA - Application Traffic >> Authentication Virtual Servers** (Безопасность > AAA — трафик приложений > Политики > Аутентификация > Виртуальные серверы проверки подлинности).

2.  Щелкните **Add** (Добавить) и сделайте следующее:

    ![Конфигурация Citrix NetScaler](./media/header-citrix-netscaler-tutorial/server02.png)

    а.  Укажите **имя**.

    b.  Выберите **Non-Addressable** (Без адресации).

    c.  Протокол **SSL**.

    d.  Нажмите кнопку **ОК**.

    д)  Нажмите кнопку **Продолжить**.

### <a name="configure-the-authentication-virtual-server-to-use-azure-ad"></a>Настройка виртуального сервера проверки подлинности для использования Azure AD

Для этого потребуется внести изменения в два раздела виртуального сервера проверки подлинности.

1.  **Advanced Authentication Policies** (Расширенные политики проверки подлинности).

    ![Конфигурация Citrix NetScaler](./media/header-citrix-netscaler-tutorial/virtual01.png)

    а. Выберите ранее созданную **политику проверки подлинности**.

    b. Щелкните **Bind** (Привязать).

      ![Конфигурация Citrix NetScaler](./media/header-citrix-netscaler-tutorial/virtual02.png)

2. **Form Based Virtual Servers** (Виртуальные серверы на основе формы).

    ![Конфигурация Citrix NetScaler](./media/header-citrix-netscaler-tutorial/virtual03.png)

    а.  Вам нужно указать **полное доменное имя**, так как это обязательный параметр в пользовательском интерфейсе.

    b.  Выберите **Virtual Server Load Balancer** (Подсистема балансировки нагрузки виртуального сервера) для включения защиты с использованием аутентификации Azure AD.

    c.  Щелкните **Bind** (Привязать).

    ![Конфигурация Citrix NetScaler](./media/header-citrix-netscaler-tutorial/virtual04.png)

    >[!NOTE]
    >Не забудьте щелкнуть **Done** (Готово) на странице конфигурации виртуального сервера проверки подлинности.

3. Проверьте изменения. Перейдите по URL-адресу приложения. Вместо страницы, которая ранее отображалась без проверки подлинности, вы должны увидеть страницу входа в клиент.

    ![Конфигурация Citrix NetScaler](./media/header-citrix-netscaler-tutorial/virtual05.png)

## <a name="configure-citrix-netscaler-single-sign-on-for-header-based-authentication"></a>Настройка проверки подлинности на основе заголовка для единого входа в Citrix NetScaler

### <a name="citrix-adc-configuration"></a>Настройка Citrix ADC

### <a name="create-an-rewrite-action"></a>Создание действия перезаписи

1. Последовательно откройте **AppExpert > Rewrite > Rewrite Actions** (AppExpert > Перезапись > Действия перезаписи).
 
    ![Конфигурация Citrix NetScaler](./media/header-citrix-netscaler-tutorial/header01.png)

2.  Нажмите кнопку **Добавить**.

    а.  Укажите **имя**.

    b.  Тип = **INSERT_HTTP_HEADER**.

    c.  Укажите значение для **HEADER NAME** (Имя заголовка), в нашем примере это SecretID.

    d.  Укажите выражение **aaa.USER.ATTRIBUTE(`mySecretID`)** , где **mySecretID** обозначает утверждение SAML, отправляемое из Azure AD в Citrix ADC.

    д)  Нажмите кнопку **Создать**.

    ![Конфигурация Citrix NetScaler](./media/header-citrix-netscaler-tutorial/header02.png)
 
### <a name="create-a-rewrite-policy"></a>Создайте политику перезаписи.

1.  Последовательно выберите **AppExpert > Rewrite > Rewrite Policies** (AppExpert > Перезапись > Политики перезаписи).
 
    ![Конфигурация Citrix NetScaler](./media/header-citrix-netscaler-tutorial/header03.png)

2.  Нажмите кнопку **Добавить**.

    ![Конфигурация Citrix NetScaler](./media/header-citrix-netscaler-tutorial/header04.png)

    а.  Укажите **имя**.

    b.  Выберите созданное ранее **действие**.

    c. В поле Expression (Выражение) укажите **true**.

    d.  Щелкните **Create** (Создать).

### <a name="bind-rewrite-policy-to-virtual-servers"></a>Связь политики перезаписи с виртуальными серверами

1. Чтобы привязать политику перезаписи к определенному виртуальному серверу с помощью графического пользовательского интерфейса, сделайте следующее.

2. Выберите **Traffic Management > Load Balancing > Virtual Servers** (Управление трафиком > Балансировка нагрузки > Виртуальные серверы).

3. В области сведений в списке виртуальных серверов выберите тот **виртуальный сервер**, к которому вы хотите привязать политику повторного создания, а затем щелкните **Open** (Открыть).

4. В диалоговом окне Configure Virtual Server (Load Balancing) (Настройка виртуального сервера, балансировка нагрузки) перейдите на вкладку **Policies**  (Политики). В этом списке отображаются все политики, которые вы настроили в NetScaler.
 
    ![Конфигурация Citrix NetScaler](./media/header-citrix-netscaler-tutorial/header05.png)

    ![Конфигурация Citrix NetScaler](./media/header-citrix-netscaler-tutorial/header06.png)

5.  Установите **флажок** рядом с именем политики, которую вы хотите связать с этим виртуальным сервером.

    ![Конфигурация Citrix NetScaler](./media/header-citrix-netscaler-tutorial/header07.png)

    ![Конфигурация Citrix NetScaler](./media/header-citrix-netscaler-tutorial/header08.png)
 
6.  Нажмите кнопку **ОК**. В строке состояния появится сообщение о том, что политика успешно настроена.

### <a name="modify-saml-server-to-extract-attributes-from-claim"></a>Настройка сервера SAML для извлечения атрибутов из утверждения

1.  Выберите **Security > AAA – Application Traffic > Policies > Authentication > Advanced Policies > Actions > Servers** (Безопасность > AAA — трафик приложений > Политики > Аутентификация > Расширенные политики > Действия > Серверы).

2.  Выберите для приложения подходящий **сервер проверки подлинности SAML**.
 
    ![Конфигурация Citrix NetScaler](./media/header-citrix-netscaler-tutorial/header09.png)

3. В разделе "Attribute" (Атрибут) введите атрибуты SAML, которые вы намерены извлекать, через запятые (","). В нашем примере мы указали атрибут с именем **mySecretID**.
 
    ![Конфигурация Citrix NetScaler](./media/header-citrix-netscaler-tutorial/header10.png)

4. Проверьте доступ к приложениям.

    ![Конфигурация Citrix NetScaler](./media/header-citrix-netscaler-tutorial/header11.png)

### <a name="create-citrix-netscaler-test-user"></a>Создание тестового пользователя Citrix NetScaler

В этом разделе описано, как создать в Citrix NetScaler пользователя с именем B.Simon. Приложение Citrix NetScaler поддерживает JIT-подготовку пользователей, которая включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Если пользователь еще не существует в Citrix NetScaler, он создается после аутентификации.

> [!NOTE]
> Если вам нужно создать пользователя вручную, обратитесь в [группу поддержки клиентов Citrix NetScaler](https://www.citrix.com/contact/technical-support.html).

## <a name="test-sso"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Citrix NetScaler на панели доступа, вы автоматически войдете в приложение Citrix NetScaler, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Тестирование работы Citrix NetScaler с Azure AD](https://aad.portal.azure.com/)

- [Настройка проверки подлинности Kerberos для единого входа в Citrix NetScaler](citrix-netscaler-tutorial.md)
