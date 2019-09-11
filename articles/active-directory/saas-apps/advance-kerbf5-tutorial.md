---
title: Руководство по Интеграция единого входа Azure Active Directory с F5 | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и F5.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9c5fb47a-1c5d-437a-b4c1-dbf739eaf5e3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e8e6a9eb50d15e6e4eb788fefe13d7e5983a834
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70165477"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>Руководство по Интеграция единого входа Azure Active Directory с F5

В этом руководстве описано, как интегрировать F5 с Azure Active Directory (Azure AD). Интеграция F5 с Azure AD обеспечивает следующие возможности:

* Контроль доступа к F5 с помощью Azure AD.
* Автоматический вход пользователей в F5 с помощью учетных записей Azure AD.
* Централизованное управление учетными записями через портал Azure.

Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее:

* подписка Azure AD Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* Подписка F5 с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* F5 поддерживает вход, инициированный **поставщиком услуг или поставщиком удостоверений**.
* Единый вход в F5 можно настроить тремя способами.

- [Настройка единого входа в F5 для расширенного приложения Kerberos](#configure-f5-single-sign-on-for-advanced-kerberos-application)

- [Настройка единого входа в F5 для приложения на основе заголовка](headerf5-tutorial.md)

- [Настройка единого входа в F5 для приложения Kerberos](kerbf5-tutorial.md)

## <a name="adding-f5-from-the-gallery"></a>Добавление F5 из коллекции

Чтобы настроить интеграцию F5 с Azure AD, необходимо добавить F5 из коллекции в список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в поле поиска введите **F5**.
1. Выберите **F5** на панели результатов и добавьте это приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>Настройка и проверка единого входа Azure AD для F5

Настройте и проверьте единый вход с Azure AD в F5, используя тестового пользователя **B.Simon**. Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в F5.

Чтобы настроить и проверить единый вход Azure AD в F5, выполните действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
    1. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD с помощью пользователя B.Simon.
    1. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить пользователю B.Simon использовать единый вход Azure AD.
1. **[Настройка единого входа в F5-SSO](#configure-f5-sso)** необходима, чтобы настроить параметры единого входа на стороне приложения.
    1. **[Создание тестового пользователя приложения F5](#create-f5-test-user)** требуется для того, чтобы в F5 существовал пользователь B.Simon, связанный с одноименным пользователем в Azure AD.
1. **[Проверка единого входа](#test-sso)** необходима, чтобы убедиться в корректной работе конфигурации.

## <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **F5** найдите раздел **Управление** и выберите **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

   ![Правка базовой конфигурации SAML](common/edit-urls.png)

1. Если вы хотите настроить приложение в режиме, инициируемом **поставщиком удостоверений**, в разделе **Базовая конфигурация SAML** введите значения следующих полей.

    a. В текстовом поле **Идентификатор** введите URL-адрес в формате `https://<YourCustomFQDN>.f5.com/`.

    б) В текстовом поле **URL-адрес ответа** введите URL-адрес в формате `https://<YourCustomFQDN>.f5.com/`.

1. Чтобы настроить приложение для работы в режиме, инициируемом **поставщиком услуг**, щелкните **Задать дополнительные URL-адреса** и выполните следующие действия.

    В текстовом поле **URL-адрес входа** введите URL-адрес в формате `https://<YourCustomFQDN>.f5.com/`.

    > [!NOTE]
    > Эти значения приведены для примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов F5](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

1. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** найдите элемент **XML метаданных федерации** и выберите **Скачать**, чтобы скачать сертификат и сохранить его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

1. Требуемые URL-адреса можно скопировать из раздела **Настройка F5**.

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

В этом разделе описано, как включить единый вход в Azure для пользователя B.Simon, предоставив этому пользователю доступ к F5.

1. На портале Azure выберите **Корпоративные приложения**, а затем —**Все приложения**.
1. В списке приложений выберите **F5**.
1. На странице "Обзор" приложения найдите раздел **Управление** и выберите **Пользователи и группы**.

   ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Ссылка "Добавить пользователя"](common/add-assign-user.png)

1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
1. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.
1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

## <a name="configure-f5-sso"></a>Настройка единого входа в F5

- [Настройка единого входа в F5 для приложения на основе заголовка](headerf5-tutorial.md)

- [Настройка единого входа в F5 для приложения Kerberos](kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-advanced-kerberos-application"></a>Настройка единого входа в F5 для расширенного приложения Kerberos

1. Откройте новое окно веб-браузера, зайдите на сайт F5 (расширенный Kerberos) с правами администратора и сделайте следующее:

1. Необходимо импортировать в F5 (расширенный Kerberos) сертификат метаданных, который будет использоваться позже в процессе настройки. Выберите **System > Certificate Management > Traffic Certificate Management >> SSL Certificate List** (Система > Управление сертификатами > Управление сертификатами трафика > Список SSL-сертификатов). Щелкните **"Import"** ("Импортировать") в правом верхнем углу

    ![Настройка F5 (расширенный Kerberos)](./media/advance-kerbf5-tutorial/configure01.png)
 
1. Чтобы настроить SAML IDP, перейдите на страницу **Доступ > Федерация > Поставщик службы SAML > Создать > Из метаданных.**

    ![Настройка F5 (расширенный Kerberos)](./media/advance-kerbf5-tutorial/configure02.png)

    ![Настройка F5 (расширенный Kerberos)](./media/advance-kerbf5-tutorial/configure03.png)
 
    ![Настройка F5 (расширенный Kerberos)](./media/advance-kerbf5-tutorial/configure04.png)

    ![Настройка F5 (расширенный Kerberos)](./media/advance-kerbf5-tutorial/configure05.png)
 
1. Укажите сертификат, отправленный на этапе 3

    ![Настройка F5 (расширенный Kerberos)](./media/advance-kerbf5-tutorial/configure06.png)

    ![Настройка F5 (расширенный Kerberos)](./media/advance-kerbf5-tutorial/configure07.png)

 1. Чтобы настроить SAML IDP, перейдите на страницу **Доступ > Федерация > Поставщик службы SAML > Локальные службы SP > Создать**.

    ![Настройка F5 (расширенный Kerberos)](./media/advance-kerbf5-tutorial/configure08.png)
 
1. Последовательно выберите **ОК**.

1. Выберите конфигурацию SP и щелкните **Bind/UnBind IdP Connectors** (Привязать/отменить привязку соединителей IDP).

     ![Настройка F5 (расширенный Kerberos)](./media/advance-kerbf5-tutorial/configure09.png)
 
 
1. Щелкните **Добавить новую строку** и выберите **Внешний соединитель IDP**, созданный на предыдущем шаге.

    ![Настройка F5 (расширенный Kerberos)](./media/advance-kerbf5-tutorial/configure10.png)
 
1. Для настройки единого входа Kerberos откройте **Доступ > Единый вход > Kerberos**.

    >[!Note]
    >Вам потребуется создать и указать учетную запись делегирования Kerberos. См. раздел KCD (см. приложение для ссылок на переменные).

    •   Источник имени пользователя `session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    •   Источник области пользователя `session.logon.last.domain`

    ![Настройка F5 (расширенный Kerberos)](./media/advance-kerbf5-tutorial/configure11.png)

1. Чтобы настроить профиль доступа, откройте **Доступ > Профиль или политики > Профиль доступа (для политик сеанса)** .

    ![Настройка F5 (расширенный Kerberos)](./media/advance-kerbf5-tutorial/configure12.png)

    ![Настройка F5 (расширенный Kerberos)](./media/advance-kerbf5-tutorial/configure13.png)

    ![Настройка F5 (расширенный Kerberos)](./media/advance-kerbf5-tutorial/configure14.png)

    ![Настройка F5 (расширенный Kerberos)](./media/advance-kerbf5-tutorial/configure15.png)

    ![Настройка F5 (расширенный Kerberos)](./media/advance-kerbf5-tutorial/configure16.png)
 
    * session.logon.last.usernameUPN   expr {[mcget {session.saml.last.identity}]}

    * session.ad.lastactualdomain  TEXT superdemo.live

    ![Настройка F5 (расширенный Kerberos)](./media/advance-kerbf5-tutorial/configure17.png)

    * (userPrincipalName=%{session.logon.last.usernameUPN})

    ![Настройка F5 (расширенный Kerberos)](./media/advance-kerbf5-tutorial/configure18.png)

    ![Настройка F5 (расширенный Kerberos)](./media/advance-kerbf5-tutorial/configure19.png)

    * session.logon.last.username  expr { "[mcget {session.ad.last.attr.sAMAccountName}]" }

    ![Настройка F5 (расширенный Kerberos)](./media/advance-kerbf5-tutorial/configure20.png)

    * mcget {session.logon.last.username}
    * mcget {session.logon.last.password}

1. Чтобы добавить новый узел, перейдите в раздел **Локальный трафик > Узлы > Список узлов > +** .

    ![Настройка F5 (расширенный Kerberos)](./media/advance-kerbf5-tutorial/configure21.png)
 
1. Чтобы создать новый пул, перейдите в раздел **Локальный трафик > Пулы > Список пулов > Создать**.

     ![Настройка F5 (расширенный Kerberos)](./media/advance-kerbf5-tutorial/configure22.png)

 1. Чтобы создать новый виртуальный сервер, перейдите в раздел **Локальный трафик > виртуальные серверы > Список виртуальных серверов > +** .

    ![Настройка F5 (расширенный Kerberos)](./media/advance-kerbf5-tutorial/configure23.png)

1. Укажите профиль доступа, созданный на предыдущем шаге.

    ![Настройка F5 (расширенный Kerberos)](./media/advance-kerbf5-tutorial/configure24.png) 

### <a name="setting-up-kerberos-delegation"></a>Настройка делегирования Kerberos 

>[!Note]
>Дополнительные сведения см. [здесь](https://www.f5.com/pdf/deployment-guides/kerberos-constrained-delegation-dg.pdf).

* **Шаг 1. Создание учетной записи делегирования**

    * Пример доменного имени: superdemo.live Имя учетной записи SAM: big-ipuser

    * New-ADUser -Name "APM Delegation Account" -UserPrincipalName host/big-ipuser.superdemo.live@superdemo.live -SamAccountName "big-ipuser" -PasswordNeverExpires $true -Enabled $true -AccountPassword (Read-Host -AsSecureString "Password!1234")

* **Шаг 2. Задание имени участника-службы (для учетной записи делегирования APM)**

    *  Пример setspn –A host/big-ipuser.superdemo.live big-ipuser

* **Шаг 3. Делегирование имени участника-службы (для учетной записи Службы приложений)**

    * Настройте подходящее делегирование для учетной записи делегирования F5.
    * В приведенном ниже примере для учетной записи делегирования APM настраивается ограниченное делегирование Kerberos для приложения FRP-App1.superdemo.live.

        ![Настройка F5 (расширенный Kerberos)](./media/advance-kerbf5-tutorial/configure25.png)

1. Укажите сведения, указанные в приведенном выше справочном [документе](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html)

1. Приложение. Ниже представлено сопоставление переменных для SAML - F5 BIG-IP:

    ![Настройка F5 (расширенный Kerberos)](./media/advance-kerbf5-tutorial/configure26.png)

    ![Настройка F5 (расширенный Kerberos)](./media/advance-kerbf5-tutorial/configure27.png) 

1. Ниже приведен полный список атрибутов SAML по умолчанию. GivenName представляется с помощью следующей строки.
`session.saml.last.attr.name.http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

| | |
| -- | -- |
| eb46b6b6.session.saml.last.assertionID | _9a4e4ddd-148f-45c4-b959-f4d148172e00 |
| eb46b6b6.session.saml.last.assertionIssueInstant  | 2019-06-16T19:18:03.054Z |
| eb46b6b6.session.saml.last.assertionIssuer | `https://sts.windows.net/<TENANT ID>`/ |
| eb46b6b6.session.saml.last.attr.name. http://schemas.microsoft.com/claims/authnmethodsreferences | `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password` |
| eb46b6b6.session.saml.last.attr.name. http://schemas.microsoft.com/identity/claims/displayname | user0 |
| eb46b6b6.session.saml.last.attr.name. http://schemas.microsoft.com/identity/claims/identityprovider | `https://sts.windows.net/<TENANT ID>/` |
| eb46b6b6.session.saml.last.attr.name. http://schemas.microsoft.com/identity/claims/objectidentifier | 90d5f0e5-8f46-4bfd-b40f-ec973d00fcb7 |
| eb46b6b6.session.saml.last.attr.name. http://schemas.microsoft.com/identity/claims/tenantid | e6abffcf-4d23-4388-91c2-bfdfcbb1530c |
| eb46b6b6.session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress | user0@superdemo.live |
| eb46b6b6.session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname | user0 |
| eb46b6b6.session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name | user0@superdemo.live |
| eb46b6b6.session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname | 0 |
| eb46b6b6.session.saml.last.audience | `https://kerbapp.superdemo.live` |
| eb46b6b6.session.saml.last.authNContextClassRef | urn:oasis:names:tc:SAML:2.0:ac:classes:Password |
| eb46b6b6.session.saml.last.authNInstant | 2019-06-16T19:18:00.318Z |
| eb46b6b6.session.saml.last.identity | user0@superdemo.live |
| eb46b6b6.session.saml.last.inResponseTo | _b9c67faa63a224d7a63f4f3cbb09f78dc05fab |
| eb46b6b6.session.saml.last.nameIDValue | user0@superdemo.live |
| eb46b6b6.session.saml.last.nameIdFormat | urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress |
| eb46b6b6.session.saml.last.responseDestination | `https://kerbapp.superdemo.live/saml/sp/profile/post/acs` |
| eb46b6b6.session.saml.last.responseId | _a1eca95a-6c41-449e-bb53-1477ba106470 |
| eb46b6b6.session.saml.last.responseIssueInstant | 2019-06-16T19:18:03.070Z |
| eb46b6b6.session.saml.last.responseIssuer | `https://sts.windows.net/<TENANT ID>/` |
| eb46b6b6.session.saml.last.result | 1 |
| eb46b6b6.session.saml.last.samlVersion | 2,0 |
| eb46b6b6.session.saml.last.sessionIndex | _9a4e4ddd-148f-45c4-b959-f4d148172e00 |
| eb46b6b6.session.saml.last.statusValue | urn:oasis:names:tc:SAML:2.0:status:Success |
| eb46b6b6.session.saml.last.subjectConfirmDataNotOnOrAfter | 2019-06-16T19:23:03.054Z |
| eb46b6b6.session.saml.last.subjectConfirmDataRecipient | `https://kerbapp.superdemo.live/saml/sp/profile/post/acs` |
| eb46b6b6.session.saml.last.subjectConfirmMethod | urn:oasis:names:tc:SAML:2.0:cm:bearer |
| eb46b6b6.session.saml.last.validityNotBefore | 2019-06-16T19:13:03.054Z |
| eb46b6b6.session.saml.last.validityNotOnOrAfter | 2019-06-16T20:13:03.054Z |

### <a name="create-f5-test-user"></a>Создание тестового пользователя в F5

В этом разделе вы узнаете, как создать пользователя B.Simon в приложении F5. Обратитесь к  [группе поддержки клиентов F5](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45), чтобы добавить пользователей на платформу F5. Перед использованием единого входа необходимо создать и активировать пользователей. 

## <a name="test-sso"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку F5 на панели доступа, вы автоматически войдете в F5, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Попробуйте использовать F5 с Azure AD](https://aad.portal.azure.com/)

- [Настройка единого входа в F5 для приложения на основе заголовка](headerf5-tutorial.md)

- [Настройка единого входа в F5 для приложения Kerberos](kerbf5-tutorial.md)

