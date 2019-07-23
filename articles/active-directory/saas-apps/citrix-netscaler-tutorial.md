---
title: Руководство по Интеграция Azure Active Directory с Citrix NetScaler | Документация Майкрософт
description: Из этой статьи вы узнаете, как настроить единый вход между Azure Active Directory и Citrix NetScaler.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: af501bd0-8ff5-468f-9b06-21e607ae25de
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 64dd67680626857db7f39fa7fd721b28a02d1561
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68276891"
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-netscaler"></a>Руководство по Интеграция Azure Active Directory с Citrix NetScaler

В этом руководстве описано, как интегрировать Citrix NetScaler с Azure Active Directory (Azure AD).
Интеграция Azure AD с Citrix NetScaler обеспечивает следующие преимущества:

* контроль доступа к Citrix Netscaler с помощью Azure AD;
* автоматический вход пользователей в Citrix NetScaler (единый вход) под учетной записью Azure AD;
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Citrix NetScaler, вам потребуется:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* подписка Citrix NetScaler с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Citrix NetScaler поддерживает единый вход, инициированный **поставщиком услуг**.

* Citrix NetScaler поддерживает **JIT-подготовку** пользователей.

## <a name="adding-citrix-netscaler-from-the-gallery"></a>Добавление Citrix NetScaler из коллекции

Чтобы настроить интеграцию Citrix NetScaler с Azure AD, вам нужно добавить Citrix NetScaler из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Citrix NetScaler из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Citrix NetScaler**, выберите **Citrix NetScaler** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

     ![Citrix NetScaler в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Citrix NetScaler с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Citrix NetScaler.

Чтобы настроить и проверить единый вход Azure AD в Citrix NetScaler, вам нужно выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в Citrix NetScaler](#configure-citrix-netscaler-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя Citrix NetScaler](#create-citrix-netscaler-test-user)** требуется для создания в Citrix NetScaler пользователя Britta Simon, связанного с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в Citrix NetScaler, выполните следующие действия:

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Citrix NetScaler** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    ![Сведения о домене и URL-адресах единого входа приложения Citrix NetScaler](common/sp-identifier-reply.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`.
    
    b. В текстовом поле **Идентификатор (сущности)** введите URL-адрес в следующем формате: `https://<<Your FQDN>>`.

    c. В текстовое поле **URL-адреса ответа или URL-адрес службы обработчика утверждений** введите URL-адрес в следующем формате: `https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`.
    
    > [!NOTE]
    > Эти значения приведены для примера. Необходимо обновить эти значения действующим URL-адресом для входа и идентификатором. Чтобы получить эти значения, обратитесь в [группу поддержки клиентов Citrix NetScaler](https://www.citrix.com/contact/technical-support.html). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

    > [!NOTE]
    > Чтобы единый вход работал правильно, эти URL-адреса должны быть доступны с общедоступных сайтов. Необходимо настроить параметры брандмауэра или безопасности на стороне Netscaler, чтобы разрешить Azure AD отправлять маркер на указанный выше URL-адрес ACS.

5. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать нужный вам **XML метаданных федерации**, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

6. Скопируйте требуемые URL-адреса из раздела **Настройка Citrix NetScaler**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    b. Идентификатор Azure AD

    c. URL-адрес выхода.

### <a name="configure-citrix-netscaler-single-sign-on"></a>Настройка единого входа в Citrix NetScaler

1. В другом окне веб-браузера войдите в свой клиент Citrix NetScaler с правами администратора.

2. Убедитесь, что здесь настроен параметр **NetScaler Firmware Version = NS12.1: Build 48.13.nc**.

    ![Настройка единого входа](./media/citrix-netscaler-tutorial/configure01.png)

3. На странице **VPN Virtual Server** (Виртуальный сервер VPN) сделайте следующее.

     ![Настройка единого входа](./media/citrix-netscaler-tutorial/configure02.png)

    a. Задайте для параметра шлюза **ICA Only** (Только ICA) значение **True**.
    
    b. Задайте для параметра **Enable Authentication** (Включить аутентификацию) значение **True**.
    
    c. **DTLS** настраивать не обязательно.
    
    d. Убедитесь, что **SSLv3** имеет значение **Disabled** (Отключено).

4. Создается настраиваемая группа **SSL Ciphers** для достижения уровня A+ для https://www.ssllabs.com, как показано ниже:

    ![Настройка единого входа](./media/citrix-netscaler-tutorial/configure03.png)

5. На странице **Configure Authentication SAML Server** (Настройка сервера аутентификации SAML) выполните следующие действия.

      ![Настройка единого входа](./media/citrix-netscaler-tutorial/configure04.png)

    a. В текстовое поле **Name** (Имя) введите имя сервера.

    b. В текстовое поле **Redirect URL** (URL-адрес переадресации) вставьте **URL-адрес входа**, скопированный на портале Azure.

    c. В текстовое поле **Single Logout URL** (URL-адрес удаленного выхода) вставьте значение **URL-адреса выхода**, скопированное на портале Azure.

    d. В поле **имени сертификата поставщика удостоверений** щелкните значок **+** , чтобы добавить сертификат, который вы скачали с портала Azure. После отправки сертификата выберите его из раскрывающегося списка.

    д. Ниже перечислены еще несколько полей, которые нужно настроить на этой странице.

      ![Настройка единого входа](./media/citrix-netscaler-tutorial/configure24.png)

    Е. Для параметра **Requested Authentication Context** (Запрашиваемый контекст аутентификации) выберите значение **Exact** (Точный).

    ж. Для параметра **Signature Algorithm** (Алгоритм подписи) выберите значение **RSA-SHA256**.

    h. Для параметра **Digest Method** (Метод выборки) выберите значение **SHA256**.

    i. Установите флажок **Enforce Username** (Требовать имя пользователя).

    j. Щелкните **ОК**

6. Чтобы настроить **профиль сеанса**, выполните следующие действия.

    ![Настройка единого входа](./media/citrix-netscaler-tutorial/configure06.png)

    a. В текстовом поле **Name** (Имя) введите имя профиля сеанса.

    b. На вкладке **Client Experience** (Взаимодействие с пользователем) внесите изменения, представленные на следующем снимке экрана.

    c. Внесите дополнительные изменения на вкладке **General** (Общие), как показано ниже, и щелкните **ОК**.

    ![Настройка единого входа](./media/citrix-netscaler-tutorial/configure07.png)

    d. На вкладке **Published Applications** (Опубликованные приложения) внесите изменения, которые представлены на следующем снимке экрана, и щелкните **ОК**.

    ![Настройка единого входа](./media/citrix-netscaler-tutorial/configure08.png)

    д. На вкладке **Security** (Безопасность) внесите изменения, которые представлены на следующем снимке экрана, и щелкните **ОК**.

    ![Настройка единого входа](./media/citrix-netscaler-tutorial/configure09.png)

7. Настройте подключения ICA через порт Session Reliability Port **2598**, как показано на следующем снимке экрана.

    ![Настройка единого входа](./media/citrix-netscaler-tutorial/configure10.png)

8. В разделе **SAML** добавьте **серверы**, как показано на следующем снимке экрана.

    ![Настройка единого входа](./media/citrix-netscaler-tutorial/configure11.png)

9. В разделе **SAML** добавьте **политики**, как показано на следующем снимке экрана.

     ![Настройка единого входа](./media/citrix-netscaler-tutorial/configure12.png)

10. На странице **Global Settings** (Глобальные параметры) перейдите к разделу **Clientless Access** (Доступ без клиента).

    ![Настройка единого входа](./media/citrix-netscaler-tutorial/configure13.png)

11. На вкладке **Configuration** (Конфигурация) сделайте следующее.

    ![Настройка единого входа](./media/citrix-netscaler-tutorial/configure14.png)

    a. Выберите **Allow Domains** (Разрешить домены).

    b. В текстом поле **Domain Name** (Доменное имя) выберите домен.

    c. Последовательно выберите **ОК**.

12. Настройте параметр **StoreFront** а разделе **Receiver for Web Sites** (Получатель для веб-сайтов), как показано на следующем снимке экрана.

    ![Настройка единого входа](./media/citrix-netscaler-tutorial/configure15.png)

13. Во всплывающем элементе **Manage Authentication Methods - Corp** (Управление методами проверки подлинности — корпоративные) выполните следующие действия.

    ![Настройка единого входа](./media/citrix-netscaler-tutorial/configure16.png)

    a. Выберите **Имя пользователя и пароль**.

    b. Выберите **Pass-through from NetScaler Gateway** (Сквозной режим для шлюза NetScaler).

    c. Последовательно выберите **ОК**.

14. Во всплывающем элементе **Configure Trusted Domains** (Настройка доверенных доменов) выполните следующие действия.

    ![Настройка единого входа](./media/citrix-netscaler-tutorial/configure17.png)

    a. Выберите **Trusted domains only** (Только доверенные домены).

    b. Щелкните **Добавить**, чтобы добавить нужный домен в текстовое поле **Trusted domains** (Доверенные домены).

    c. Выберите домен по умолчанию из списка **Default domain** (Домен по умолчанию).

    d. Выберите параметр **Show domains list in logon page** (Показать список доменов на странице входа).

    д. Последовательно выберите **ОК**.

15. На всплывающем элементе **Manage NetScaler Gateways** (Управление шлюзами NetScaler) выполните следующие действия.

    ![Настройка единого входа](./media/citrix-netscaler-tutorial/configure18.png)

    a. Щелкните **Добавить**, чтобы добавить шлюзы NetScaler в текстовое поле **NetScaler Gateways** (Шлюзы NetScaler).

    b. Нажмите кнопку **Закрыть**

16. На вкладке **StoreFront General Settings** (Общие параметры StoreFront) выполните следующие действия.

    ![Настройка единого входа](./media/citrix-netscaler-tutorial/configure19.png)

    a. В текстовое поле **Display name** (Отображаемое имя) введите имя нужного шлюза NetScaler.

    b. В текстовом поле **NetScaler Gateway URL** (URL-адрес шлюза NetScaler) введите URL-адрес нужного шлюза NetScaler.

    c. Для параметра **Usage or role** (Назначение или роль) выберите значение **Authentication and HDX routing** (Проверка подлинности и маршрутизация HDX).

    d. Последовательно выберите **ОК**.

17. На вкладке **StoreFront Secure Ticket Authority** (Центр защищенных билетов StoreFront) выполните следующие действия.

    ![Настройка единого входа](./media/citrix-netscaler-tutorial/configure20.png)

    a. Нажмите кнопку **Add** (Добавить), чтобы добавить в текстовое поле **URL-адреса центра защищенных билетов**.

    b. Выберите **Enable session reliability** (Включить надежность сеанса).

    c. Последовательно выберите **ОК**.

18. На вкладке **StoreFront Authentication Settings** (Параметры проверки подлинности StoreFront) выполните следующие действия.

    ![Настройка единого входа](./media/citrix-netscaler-tutorial/configure21.png)

    a. Выберите значение **Version** (Версия).

    b. Для параметра **Logon type** (Тип входа в систему) выберите **Domain** (Домен).

    c. Введите **Callback URL** (URL-адрес обратного вызова).

    d. Последовательно выберите **ОК**.

19. На вкладке **StoreFront Deploy Citrix Receiver** (Развертывание получателя Citrix для StoreFront) выполните следующие действия.

    ![Настройка единого входа](./media/citrix-netscaler-tutorial/configure22.png)

    a. Для параметра **Deployment option** (Вариант развертывания) как **Use Receiver for HTML5 if local Receiver is unavailable** (Использовать получатель для HTML5, если локальный получатель недоступен).

    b. Последовательно выберите **ОК**.

20. На всплывающем элементе **Manage Beacons** (Управление маяками) выполните следующие действия.

    ![Настройка единого входа](./media/citrix-netscaler-tutorial/configure23.png)

    a. Для параметра **Internal beacon** (Внутренний маяк) выберите значение **Use the service URL** (Использовать URL-адрес службы).

    b. Нажмите кнопку **Add** (Добавить), чтобы добавить URL-адрес в текстовое поле **External beacons** (Внешние маяки).

    c. Последовательно выберите **ОК**.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD 

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    а. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon@yourcompanydomain.extension** .  
    Например BrittaSimon@contoso.com.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Citrix NetScaler.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **Citrix NetScaler**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Citrix NetScaler**.

    ![Ссылка на Citrix NetScaler в списке "Приложения"](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-citrix-netscaler-test-user"></a>Создание тестового пользователя Citrix NetScaler

В этом разделе описано, как создать в Citrix NetScaler пользователя с именем Britta Simon. Приложение Citrix NetScaler поддерживает JIT-подготовку пользователей, которая включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Если пользователь еще не существует в Citrix NetScaler, он создается после аутентификации.

>[!NOTE]
>Если вам нужно создать пользователя вручную, обратитесь в [группу поддержки клиентов Citrix NetScaler](https://www.citrix.com/contact/technical-support.html).

### <a name="test-single-sign-on"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Citrix NetScaler на панели доступа, вы автоматически войдете в приложение Citrix NetScaler, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

