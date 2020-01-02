---
title: Руководство по интеграции единого входа Azure Active Directory с Akamai | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Akamai.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1b7e0d7a-e78f-43a5-af93-b626186e2376
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 042dd242285081001ca48c9f17e4d42c2294c0ff
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74979140"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-akamai"></a>Руководство по интеграции единого входа Azure Active Directory с Akamai

В этом руководстве описано, как интегрировать Akamai с Azure Active Directory (Azure AD). Интеграция Akamai с Azure AD обеспечивает следующие возможности:

* Контроль доступа к Akamai с помощью Azure AD.
* Автоматический вход пользователей в Akamai с учетными записями Azure AD.
* Централизованное управление учетными записями через портал Azure.

Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее:

* подписка Azure AD Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* Подписка Akamai с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

- Akamai поддерживает единый вход, инициированный поставщиком удостоверений.

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
    * **[Создание тестового пользователя в Akamai](#create-akamai-test-user)** нужно для того, чтобы в Akamai существовал пользователь B.Simon, связанный с представлением этого же пользователя в Azure AD.
1. **[Проверка единого входа](#test-sso)** необходима, чтобы убедиться в корректной работе конфигурации.

## <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Akamai** найдите раздел **Управление** и выберите **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

   ![Правка базовой конфигурации SAML](common/edit-urls.png)

1. Если вы хотите настроить приложение в режиме, инициируемом **поставщиком удостоверений**, в разделе **Базовая конфигурация SAML** введите значения следующих полей.

    a. В текстовом поле **Идентификатор** введите URL-адрес в формате `https://<Yourapp>.login.go.akamai-access.com/sp/response`.

    б) В текстовом поле **URL-адрес ответа** введите URL-адрес в формате `https:// <Yourapp>.login.go.akamai-access.com/sp/response`.

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

1. На портале Azure выберите **Корпоративные приложения**, а затем —**Все приложения**.
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

1. Войдите в консоль **доступа к корпоративному приложению Akamai**.
1. В **консоли Akamai ЕАА** выберите **Identity** > **Identity Providers** (Удостоверение > Поставщики удостоверений).

    ![Настройка Akamai](./media/header-akamai-tutorial/configure01.png)

1. Щелкните **Add Identity Provider** (Добавить поставщика удостоверений).

    ![Настройка Akamai](./media/header-akamai-tutorial/configure02.png)

    a. Укажите уникальное имя в **Name** (Имя).
    
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

### <a name="header-based-authentication"></a>Проверка подлинности на основе заголовков

Проверка подлинности Akamai на основе заголовков

1. Выберите вариант **Custom HTTP** (Настраиваемый HTTP) в мастере добавления приложений.

    ![Настройка Akamai](./media/header-akamai-tutorial/configure05.png)

    ![Настройка Akamai](./media/header-akamai-tutorial/configure06.png)

    ![Настройка Akamai](./media/header-akamai-tutorial/configure07.png)

    ![Настройка Akamai](./media/header-akamai-tutorial/configure08.png)

#### <a name="authentication"></a>Аутентификация

![Настройка Akamai](./media/header-akamai-tutorial/configure09.png)

![Настройка Akamai](./media/header-akamai-tutorial/configure10.png)

#### <a name="services"></a>Службы

1. Щелкните "Save and Go to Authentication" (Сохранить и перейти к проверке подлинности).

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

### <a name="kerberos-authentication"></a>Проверка подлинности Kerberos

#### <a name="remote-desktop"></a>Удаленный рабочий стол

1. Выберите вариант **RDP** в мастере добавления приложений.

    ![Настройка Akamai](./media/header-akamai-tutorial/configure16.png)

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

Щелкните **Save and go to Deployment** (Завершить и перейти к развертыванию).

![Настройка Akamai](./media/header-akamai-tutorial/configure22.png)

![Настройка Akamai](./media/header-akamai-tutorial/configure23.png)

![Настройка Akamai](./media/header-akamai-tutorial/configure24.png)

### <a name="deployment"></a>Развертывание

#### <a name="ssh"></a>SSH

1. Перейдите к разделу "Add Applications" (Добавление приложений) и выберите **SSH**.

    ![Настройка Akamai](./media/header-akamai-tutorial/configure25.png)

    ![Настройка Akamai](./media/header-akamai-tutorial/configure26.png)

1. Настройте удостоверение приложения.

    ![Настройка Akamai](./media/header-akamai-tutorial/configure27.png)

    a. Укажите имя и описание.

    b. Укажите IP-адрес или полное доменное имя сервера приложений и порт для подключения SSH.

    c. Укажите имя пользователя и парольную фразу "Проверка Akamai ЕАА" для SSH.

    d. Укажите значение в поле "External host Name" (Имя внешнего узла).

    д. Укажите расположение соединителя и выберите нужный соединитель.

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

Щелкните **Deploy Application** (Развернуть приложение).

![Настройка Akamai](./media/header-akamai-tutorial/configure32.png)

### <a name="kerberos-applications"></a>Приложения Kerberos

#### <a name="adding-directory"></a>Добавление каталога

![Настройка Akamai](./media/header-akamai-tutorial/configure33.png)

![Настройка Akamai](./media/header-akamai-tutorial/configure34.png)

![Настройка Akamai](./media/header-akamai-tutorial/configure35.png)

![Настройка Akamai](./media/header-akamai-tutorial/configure36.png)

### <a name="create-akamai-test-user"></a>Создание тестового пользователя Akamai

Из этого разделе вы узнаете, как создать пользователя B.Simon в приложении Akamai. Обратитесь в  [службу поддержки клиентов Akamai](https://www.akamai.com/us/en/contact-us/), чтобы добавить пользователей на платформу Akamai. Перед использованием единого входа необходимо создать и активировать пользователей. 

## <a name="test-sso"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Akamai на панели доступа, вы автоматически войдете в приложение Akamai, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Попробуйте использовать Akamai с Azure AD](https://aad.portal.azure.com/)
