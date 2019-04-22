---
title: Руководство. Интеграция Azure Active Directory с локальной версией SharePoint | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и локальной версией SharePoint.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 85b8d4d0-3f6a-4913-b9d3-8cc327d8280d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ba9f4df36f753a1caf619ad90015fa073a00de3
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58883383"
---
# <a name="tutorial-azure-active-directory-integration-with-sharepoint-on-premises"></a>Руководство. Интеграция Azure Active Directory с локальной версией SharePoint

В этом руководстве описано, как интегрировать локальную версию SharePoint с Azure Active Directory (Azure AD).
Интеграция локальной версии SharePoint с Azure AD дает указанные ниже преимущества:

* Вы можете управлять доступом пользователей к локальной версии SharePoint в Azure AD.
* Вы можете включить автоматический вход пользователей в локальной версии SharePoint (единый вход) с использованием учетных записей Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с локальной версией SharePoint, вам потребуется следующее:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* подписка на локальную версию SharePoint с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Локальная версия SharePoint поддерживает единый вход, инициированный **поставщиком услуг**.

## <a name="adding-sharepoint-on-premises-from-the-gallery"></a>Добавление локальной версии SharePoint из коллекции

Чтобы настроить интеграцию локальной версии SharePoint с Azure AD, вам потребуется добавить локальную версию SharePoint из коллекции в ваш список управляемых приложений SaaS.

**Чтобы добавить локальную версию SharePoint из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **SharePoint (локальная версия)**, на панели результатов выберите **Локальная версия SharePoint** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Локальная версия SharePoint в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

Работая с этим разделом, вы настроите и протестируете единый вход Azure AD для локальной версии SharePoint с помощью тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в локальной версии SharePoint.

Чтобы настроить и протестировать единый вход Azure AD в локальной версии SharePoint, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в локальной версии SharePoint](#configure-sharepoint-on-premises-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Создание группы безопасности Azure AD на портале Azure](#create-an-azure-ad-security-group-in-the-azure-portal)** необходимо, чтобы создать группу безопасности в Azure AD и включить для нее единый вход.
5. **[Предоставление доступа к локальной группе безопасности SharePoint](#grant-access-to-sharepoint-on-premises-security-group)** необходимо, чтобы предоставить доступ определенной группе в Azure AD.
6. **[Назначение группы безопасности Azure AD на портале Azure](#assign-the-azure-ad-security-group-in-the-azure-portal)** необходимо, чтобы указать определенную группу Azure AD для аутентификации.
7. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD для локальной версии SharePoint, выполните следующие действия:

1. На [портале Azure](https://portal.azure.com/) откройте страницу интеграции с **локальным приложением SharePoint** и выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    ![Сведения о домене и URL-адресах единого входа для локальной версии SharePoint](common/sp-identifier-reply.png)

    a. В текстовое поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<YourSharePointServerURL>/_trust/default.aspx`.

    b. В поле **Идентификатор** введите URL-адрес в следующем формате: `urn:sharepoint:federation`.

    c. В текстовом поле **URL-адрес ответа** введите URL-адрес в формате `https://<YourSharePointServerURL>/_trust/default.aspx`.

    > [!NOTE]
    > Эти значения приведены для примера. Укажите вместо них фактические значения URL-адреса для входа, идентификатора и URL-адреса ответа. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов локальной версии SharePoint](https://support.office.com/). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

5. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Загрузить**, чтобы загрузить требуемый **сертификат (Base64)** из предложенных вариантов, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

    > [!Note]
    > Запишите путь к файлу, по которому вы загрузили файл сертификата, так как вам нужно будет использовать его позже в сценарии PowerShell для настройки.

6. Скопируйте требуемый URL-адрес из раздела **Настройка локальной версии SharePoint**. Для **URL-адреса службы единого входа** используйте значение из следующих форматов: `https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ — это идентификатор клиента подписки Azure AD.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    б) Идентификатор Azure AD.

    в) URL-адрес выхода.

    > [!NOTE]
    > В локальном приложении SharePoint используется токен SAML 1.1, поэтому Azure AD ожидает запроса WS Fed от сервера SharePoint и после аутентификации создает токен SAML 1.1.

### <a name="configure-sharepoint-on-premises-single-sign-on"></a>Настройка локальной версии SharePoint с поддержкой единого входа

1. В другом окне веб-браузера выполните вход на корпоративном сайте локальной версии SharePoint в качестве администратора.

2. **Настройка нового доверенного поставщика удостоверений в SharePoint Server 2016**

    Войдите в систему на сервере SharePoint Server 2016 и откройте командную консоль SharePoint 2016. Заполните значения $realm (значение идентификатора из локального домена и URL-адресов SharePoint на портале Azure), $wsfedurl (URL-адрес службы единого входа) и $filepath (путь к файлу, по которому вы загрузили файл сертификата) с портала Azure и выполните следующие команды для настройки нового доверенного поставщика удостоверений.

    > [!TIP]
    > Если вы не знаете, как использовать PowerShell, или хотите получить дополнительные сведения о том, как работает PowerShell, см. статью [SharePoint PowerShell](https://docs.microsoft.com/powershell/sharepoint/overview?view=sharepoint-ps).

    ```powershell
    $realm = "<Identifier value from the SharePoint on-premises Domain and URLs section in the Azure portal>"
    $wsfedurl="<SAML single sign-on service URL value which you have copied from the Azure portal>"
    $filepath="<Full path to SAML signing certificate file which you have downloaded from the Azure portal>"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname" -IncomingClaimTypeDisplayName "GivenName" -SameAsIncoming
    $map3 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" -IncomingClaimTypeDisplayName "SurName" -SameAsIncoming
    $map4 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress" -IncomingClaimTypeDisplayName "Email" -SameAsIncoming
    $map5 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.microsoft.com/ws/2008/06/identity/claims/role" -IncomingClaimTypeDisplayName "Role" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "SharePoint secured by Azure AD" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map,$map2,$map3,$map4 -SignInUrl $wsfedurl -IdentifierClaim "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"
    ```

    Далее включите доверенный поставщик удостоверений для вашего приложения, выполнив следующие действия:

    a. В центре администрирования перейдите в раздел **Управление веб-приложениями** и выберите веб-приложение, которое необходимо защитить с помощью Azure AD.

    b. На ленте щелкните **Поставщики проверки подлинности** и выберите зону, которую нужно использовать.

    c. Щелкните пункт **Доверенный поставщик удостоверений** и выберите поставщика, которого вы только что зарегистрировали под именем *AzureAD*.

    d. В разделе параметра URL-адреса страницы входа щелкните **Настраиваемая страница входа** и укажите значение /_trust/.

    д. Последовательно выберите **ОК**.

    ![Настройка поставщика проверки подлинности](./media/sharepoint-on-premises-tutorial/fig10-configauthprovider.png)

    > [!NOTE]
    > Некоторые внешние пользователи не смогут использовать эту единую интеграцию входа, так как их UPN будет искажать значение, подобное `MYEMAIL_outlook.com#ext#@TENANT.onmicrosoft.com`. Вскоре мы предоставим клиентам возможность конфигурации приложений для обработки UPN в зависимости от типа пользователя. После этого все ваши гостевые пользователи смогут использовать SSO без проблем в качестве сотрудников организации.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    а. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon\@домен_вашей_компании.доменная_зона**.  
    Например BrittaSimon@contoso.com.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Создание группы безопасности на портале Azure

1. Щелкните **Azure Active Directory > Все группы**.

    ![Создание группы безопасности Azure AD](./media/sharepoint-on-premises-tutorial/allgroups.png)

2. Щелкните **Новая группа**.

    ![Создание группы безопасности Azure AD](./media/sharepoint-on-premises-tutorial/newgroup.png)

3. Заполните поля **Тип группы**, **Имя группы**, **Описание группы** и **Тип членства**. Щелкните стрелку, чтобы выбрать членов группы. Найдите или щелкните члена, которого необходимо добавить в группу. Щелкните **Выбрать**, чтобы добавить выбранных членов, и нажмите кнопку **Создать**.

    ![Создание группы безопасности Azure AD](./media/sharepoint-on-premises-tutorial/addingmembers.png)

    > [!NOTE]
    > Чтобы назначить группы безопасности Azure Active Directory для локальной версии SharePoint, потребуется установить и настроить [AzureCP](https://yvand.github.io/AzureCP/) в локальной ферме SharePoint или разработать и настроить альтернативный пользовательский поставщик утверждений для SharePoint.  Обратитесь к разделу дополнительных сведений в конце документа, чтобы создать собственный пользовательский поставщик утверждений, если вы не используете AzureCP.

### <a name="grant-access-to-sharepoint-on-premises-security-group"></a>Предоставление доступа локальной группе безопасности SharePoint

**Настройка групп безопасности и разрешений при регистрации приложения**

1. На портале Azure выберите **Azure Active Directory**, а затем выберите **Регистрация приложений**.

    ![Колонка "Корпоративные приложения"](./media/sharepoint-on-premises-tutorial/appregistrations.png)

2. В поле поиска введите и выберите **Локальная версия SharePoint**.

    ![Локальная версия SharePoint в списке результатов](./media/sharepoint-on-premises-tutorial/appsearch.png)

3. Щелкните **Манифест**.

    ![Параметр "Манифест"](./media/sharepoint-on-premises-tutorial/manifest.png)

4. Измените `groupMembershipClaims`: `NULL` на `groupMembershipClaims`: `SecurityGroup`. Нажмите кнопку "Сохранить".

    ![Изменение манифеста](./media/sharepoint-on-premises-tutorial/manifestedit.png)

5. В меню **Параметры** щелкните **Необходимые разрешения**.

    ![Необходимые разрешения](./media/sharepoint-on-premises-tutorial/settings.png)

6. Выберите **Добавить** и щелкните **Выбор API**.

    ![Доступ к API](./media/sharepoint-on-premises-tutorial/required_permissions.png)

7. По очереди добавьте компоненты **Windows Azure Active Directory** и **API Microsoft Graph** (их невозможно добавить одновременно).

    ![Выбор API](./media/sharepoint-on-premises-tutorial/permissions.png)

8. Выберите "Windows Azure Active Directory", установите флажок "Чтение данных каталога" и щелкните "Выбрать". Вернитесь на предыдущий экран, добавьте компонент Microsoft Graph и также установите для него флажок "Чтение данных каталога".  Щелкните "Выбрать", а затем — "Готово".

    ![Разрешение доступа](./media/sharepoint-on-premises-tutorial/readpermission.png)

9. Теперь в разделе "Необходимые параметры" выберите на **Предоставить разрешения** и затем нажмите кнопку "Да", чтобы предоставить разрешения.

    ![Предоставление разрешений](./media/sharepoint-on-premises-tutorial/grantpermission.png)

    > [!NOTE]
    > Проверьте в области уведомлений, были успешно предоставлены разрешения.  Если это не так, AzureCP не будет работать правильно и будет невозможно настроить группы безопасности Active Directory Azure для локальной версии SharePoint.

10. Настройте AzureCP на локальной ферме SharePoint или настройте альтернативный пользовательский поставщик утверждений.  В этом примере мы используем AzureCP.

    > [!NOTE]
    > Обратите внимание на то, что AzureCP не является продуктом корпорации Майкрософт и не обслуживается службой технической поддержки Майкрософт. Скачайте приложение AzureCP на локальной ферме SharePoint по адресу https://yvand.github.io/AzureCP/. Установите и настройте программу. 

11. **Предоставьте доступ группе безопасности Azure Active Directory в локальной версии SharePoint**. Группам должен быть предоставлен доступ к приложению в локальной версии SharePoint.  Следуйте инструкциям ниже, чтобы задать разрешения для доступа к веб-приложению.

12. В центре администрирования щелкните "Управление приложениями" > "Управление веб-приложениями", а затем выберите веб-приложение, чтобы включить ленту, и щелкните "Политика пользователя".

    ![Центра администрирования](./media/sharepoint-on-premises-tutorial/centraladministration.png)

13. В диалоговом окне "Политика для веб-приложения" щелкните "Добавить пользователей", выберите зону, после чего нажмите кнопку "Далее".  Щелкните "Адресная книга".

    ![Политика для веб-приложения](./media/sharepoint-on-premises-tutorial/webapp-policy.png)

14. Найдите и добавьте группу безопасности Active Directory Azure, после чего нажмите кнопку "ОК".

    ![Добавление группы безопасности](./media/sharepoint-on-premises-tutorial/securitygroup.png)

15. Выберите "Разрешения", а затем нажмите кнопку "Готово".

    ![Добавление группы безопасности](./media/sharepoint-on-premises-tutorial/permissions1.png)

16. Теперь в диалоговом окне "Политика для веб-приложения" добавлена группа Azure Active Directory.  Утверждение группы содержит идентификатор объекта группы безопасности Azure Active Directory для имени пользователя.

    ![Добавление группы безопасности](./media/sharepoint-on-premises-tutorial/addgroup.png)

17. Перейдите к коллекции сайтов SharePoint и также добавьте в нее эту группу. Щелкните "Параметры сайта", а затем выберите "Разрешения для сайта" и "Предоставить разрешения".  Найдите утверждение роли группы, назначьте уровень разрешений и щелкните "Общий доступ".

    ![Добавление группы безопасности](./media/sharepoint-on-premises-tutorial/grantpermission1.png)

### <a name="configuring-one-trusted-identity-provider-for-multiple-web-applications"></a>Настройка одного доверенного поставщика удостоверений для нескольких веб-приложений

Эта конфигурации подходит для одного веб-приложения, но если вы планируете использовать тот же доверенный поставщик удостоверений для нескольких веб-приложений, нужна дополнительная настройка. Предположим, например, что мы расширили веб-приложение для использования URL-адреса `https://portal.contoso.local` и хотим также обеспечить проверку подлинности пользователей для `https://sales.contoso.local`. Для этого нужно обновить поставщик удостоверений, чтобы учесть параметр WReply, и обновить регистрацию приложения в Azure AD, чтобы добавить URL-адрес ответа.

1. На портале Azure откройте каталог Azure AD. Щелкните **Регистрация приложений** и затем **Показать все приложения**. Щелкните ранее созданное приложение (интеграция SAML с SharePoint).

2. Щелкните **Параметры**.

3. В колонке параметров щелкните **URL-адреса ответа**.

4. Добавьте URL-адрес для дополнительного веб-приложения, дополнив его `/_trust/default.aspx` (например, `https://sales.contoso.local/_trust/default.aspx`), и нажмите кнопку **Сохранить**.

5. На сервере SharePoint откройте **командную консоль SharePoint 2016** и выполните приведенные ниже команды, применив использованное ранее имя доверенного издателя токена удостоверения.

    ```powershell
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```

6. В центре администрирования перейдите к веб-приложению и включите существующий доверенный поставщик удостоверений. Не забудьте также настроить URL-адрес страницы входа в качестве настраиваемой страницы входа `/_trust/`.

7. В центре администрирования щелкните веб-приложение и выберите **Политика пользователей**. Добавьте пользователя с соответствующими разрешениями, как показано выше в данной статье.

### <a name="fixing-people-picker"></a>Исправление средства выбора людей

Теперь пользователи могут входить в SharePoint 2016, используя удостоверения из Azure AD. Но пользовательский интерфейс можно сделать еще лучше. Например, при поиске пользователя в средстве выбора людей система возвращает несколько результатов поиска. Для каждого из трех типов утверждений, созданных в сопоставлении утверждений, предусмотрен отдельный результат поиска. Чтобы выбрать пользователя с помощью средства выбора, вам потребуется ввести точное имя пользователя и выбрать результат утверждения **name**.

![Результаты поиска утверждений](./media/sharepoint-on-premises-tutorial/fig16-claimssearchresults.png)

Система не проверяет значения, по которым вы выполняете поиск. Это может привести к опечаткам и к тому, что пользователи могут случайно выбрать неправильный тип утверждения для назначения, например утверждение **SurName**. Это может помешать пользователям получить доступ к ресурсам.

Для этого сценария можно использовать решение с открытым кодом [AzureCP](https://yvand.github.io/AzureCP/), в котором есть настраиваемый поставщик утверждений для SharePoint 2016. С помощью Azure AD Graph он разрешает и проверяет вводимые пользователями данные. Дополнительные сведения см. в статье об [AzureCP](https://yvand.github.io/AzureCP/).

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>Назначение группы безопасности на портале Azure

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **Локальная версия SharePoint**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений введите и выберите **Локальная версия SharePoint**.

    ![Ссылка на локальную версию SharePoint в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. Найдите группу безопасности, которую вы хотите использовать, затем щелкните ее, чтобы добавить в раздел "Выбор элементов". Щелкните **Выбрать** и нажмите кнопку **Назначить**.

    ![Поиск группы безопасности](./media/sharepoint-on-premises-tutorial/securitygroup1.png)

    > [!NOTE]
    > Проверяйте уведомления в строке меню, чтобы узнать о том, что группа для корпоративного приложения на портале Azure успешно назначена.

### <a name="create-sharepoint-on-premises-test-user"></a>Создание тестового пользователя в локальной версии SharePoint

В этом разделе описано, как создать пользователя Britta Simon в локальной версии SharePoint. Обратитесь в  [группу поддержки локальной версии SharePoint](https://support.office.com/), чтобы добавить пользователей на платформу локальной версии SharePoint. Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку локальной версии SharePoint на панели доступа, вы автоматически войдете в локальную версию SharePoint, для которой настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)