---
title: Включить удаленный доступ к Прокси-приложению SharePoint - Azure AD
description: Рассматриваются основные сведения об интеграции локального сервера SharePoint с прокси приложения Azure AD.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 983470994c103cb25d0d2aff96ae8544080e6288
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481302"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Настройка удаленного доступа к SharePoint с помощью прокси приложения Azure AD

В этом пошаговом руководстве объясняется, как интегрировать локтую ферму SharePoint с azure Active Directory (Azure AD) Application Proxy.

## <a name="prerequisites"></a>Предварительные требования

Для выполнения конфигурации необходимы следующие ресурсы:
- Ферма SharePoint 2013 или более новая.
- Арендатор Azure AD с планом, включающий прокси-сервер приложения. Узнайте больше о [планах Azure AD и ценах.](https://azure.microsoft.com/pricing/details/active-directory/)
- [Пользовательский, проверенный домен](../fundamentals/add-custom-domain.md) в агонере Azure AD.
- В помещении Active Directory синхронизирован с Azure AD Connect, с помощью которого пользователи могут [войти в Azure.](../hybrid/plan-connect-user-signin.md)
- Разъем Application Proxy установлен и работает на машине в корпоративном домене.

Настройка SharePoint с помощью прокси-сервера приложений требует двух URL-адресов:
- Внешний URL-адрес, видимый конечным пользователям и определяемый в Azure AD. Этот URL может использовать пользовательский домен. Подробнее о [работе с пользовательскими доменами читайте в приложении Azure AD Proxy.](application-proxy-configure-custom-domain.md)
- Внутренний URL, известный только в корпоративном домене и никогда не используемый напрямую.

> [!IMPORTANT]
> Чтобы убедиться, что ссылки правильно отображены, следуйте этим рекомендациям для внутреннего URL:
> - Использовать HTTPS.
> - Не используйте пользовательские порты.
> - В корпоративной системе доменных имен (DNS) создайте хост (A), чтобы указать на SharePoint WFE (или балансомер нагрузки), а не псевдоним (CName).

В этой статье используются следующие значения:
- Внутренний URL:`https://sharepoint`
- Внешний URL:`https://spsites-demo1984.msappproxy.net/`
- Учетная запись пула приложений для веб-приложения SharePoint:`Contoso\spapppool`

## <a name="step-1-configure-an-application-in-azure-ad-that-uses-application-proxy"></a>Шаг 1: Настройка приложения в Azure AD, использующее прокси-сервер приложений

На этом этапе вы создаете приложение в вашем арендаторе Active Directory Azure, использующем прокси-сервер приложения. Вы устанавливаете внешний URL и указываете внутренний URL,оба из которых используются позже в SharePoint.

1. Создайте приложение, как описано со следующими настройками. Пошаговые инструкции см. в статье [Публикация приложений с помощью прокси приложения Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).
   * **Внутренний URL:** внутренний URL SharePoint, который будет `https://sharepoint`установлен позже в SharePoint, например.
   * **Предварительная аутентификация**: Активный каталог Azure
   * **Перевод URL-адресов в заголовки**: Нет
   * **Перевод URL-адресов в орган приложения:** Нет

   ![Публикация SharePoint в качестве приложения](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

1. После публикации приложения выполните следующие действия, чтобы настроить единые настройки:

   1. На странице приложения на портале выберите **Единый вход**.
   1. Для **единого режима регистрации**выберите **интегрированную аутентификацию Windows.**
   1. Установите **внутреннее приложение SPN** в значение, установленное ранее. Для этого примера `HTTP/sharepoint`значение .
   1. В соответствии с **делегированной идентификацией входа**выберите наиболее подходящий вариант для конфигурации active Directory forest. Например, если в вашем лесу есть один домен Active Directory, выберите **имя учетной записи НА месте SAM** (как показано на следующем скриншоте). Но если ваши пользователи не в том же домене, как SharePoint и серверов подключения приложений, выберите **на месте имя пользователя** (не показано на скриншоте).

   ![Настройка встроенной проверки подлинности Windows для единого входа](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

1. Чтобы завершить настройку приложения, перейдите к разделу **Пользователи и группы** и назначьте доступ к этому приложению необходимым пользователям. 

## <a name="step-2-configure-the-sharepoint-web-application"></a>Шаг 2: Настройка веб-приложения SharePoint

Веб-приложение SharePoint должно быть настроено с помощью Kerberos и соответствующих альтернативных картдляй доступа для правильной работы с azure AD Application Proxy. Есть два возможных варианта:

- Создайте новое веб-приложение и используйте только зону по умолчанию. Это предпочтительный вариант, так как он предлагает лучший опыт с SharePoint (например, ссылки в оповещения электронной почты, генерируемые SharePoint всегда указывают на зону по умолчанию).
- Расширьте существующее веб-приложение для настройки Kerberos в зоне без дефолта.

> [!IMPORTANT]
> Независимо от используемой зоны, учетная запись пула приложений веб-приложения SharePoint должна быть учетной записью домена, чтобы Kerberos работала правильно.

### <a name="provision-the-sharepoint-web-application"></a>Предоставление веб-приложения SharePoint

- Если вы создаете новое веб-приложение и используете только зону по умолчанию (предпочтительный вариант):

    1. Запустите **Shell Management Shell** и запустите следующий сценарий:

       ```powershell
       # This script creates a web application and configures the Default zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment. Note that the managed account must exist and it must be a domain account
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       $applicationPoolManagedAccount = "Contoso\spapppool"
            
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = New-SPWebApplication -Name "SharePoint - AAD Proxy" -Port 443 -SecureSocketsLayer -URL $externalUrl -ApplicationPool "SharePoint - AAD Proxy" -ApplicationPoolAccount (Get-SPManagedAccount $applicationPoolManagedAccount) -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Default -Internal
       ```

    2. Откройте страницу **центра администрирования SharePoint**.
    1. В разделе **Параметры системы** выберите **Настройка сопоставлений альтернативного доступа**. Открывается коробка **для картирования альтернативного доступа.**
    1. Фильтр дисплей с новым веб-приложением и подтвердить, что вы видите что-то вроде этого:

       ![Альтернативные картографы доступа веб-приложений](./media/application-proxy-integrate-with-sharepoint-server/new-webapp-aam.png)

- Если вы расширяете существующее веб-приложение в новую зону (в случае, если вы не можете использовать зону по умолчанию):

    1. Запустите Shell Management Shell и запустите следующий сценарий:

       ```powershell
       # This script extends an existing web application to Internet zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment
       $webAppUrl = "http://spsites/"
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = Get-SPWebApplication $webAppUrl
       New-SPWebApplicationExtension -Name "SharePoint - AAD Proxy" -Identity $wa -SecureSocketsLayer -Zone Extranet -Url $externalUrl -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Extranet -Internal
       ```

    2. Откройте страницу **центра администрирования SharePoint**.
    1. В разделе **Параметры системы** выберите **Настройка сопоставлений альтернативного доступа**. Открывается коробка **для картирования альтернативного доступа.**
    1. Фильтр дисплей с веб-приложение, которое было расширено и подтвердить, что вы видите что-то вроде этого:

        ![Альтернативные картографы доступа расширенного приложения](./media/application-proxy-integrate-with-sharepoint-server/extend-webapp-aam.png)

### <a name="make-sure-the-sharepoint-web-application-is-running-under-a-domain-account"></a>Убедитесь, что веб-приложение SharePoint работает под учетной записью домена

Чтобы определить учетную запись, входящей в пул приложений веб-приложения SharePoint, и убедиться, что это учетная запись домена, выполните следующие действия:

1. Откройте страницу **центра администрирования SharePoint**.
1. Щелкните **Безопасность** и выберите **Настройка учетных записей служб**.
1. Выберите **веб-приложение пула - YourWebApplicationName**.

   ![Параметры для настройки учетной записи службы](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

1. Подтвердите, что **Выберите учетную запись для этого компонента** возвращает учетную запись домена, и помните об этом, так как она будет необходима на следующем этапе.

### <a name="make-sure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>Убедитесь, что сертификат HTTPS настроен для сайта IIS зоны Экстранет

Поскольку внутренний URL использует`https://SharePoint/`протокол HTTPS (), сертификат должен быть установлен на сайте Информационных служб Интернета (IIS).

1. Откройте консоль Windows PowerShell.
1. Выполнить следующий скрипт для создания сертификата, подписанного самостоятельно, и добавить его в магазин MY компьютера:

   ```powershell
   # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
   New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
   ```

   > [!IMPORTANT]
   > Самозаверяющие сертификаты допустимо использовать только для целей тестирования. В производственных средах мы настоятельно рекомендуем использовать сертификаты, выданные органом по сертификации.

1. Откройте консоль «Менеджер по информационным услугам Интернета».
1. Расширьте сервер в представлении дерева, расширьте **сайты,** выберите сайт **SharePoint - AAD Proxy** и выберите **привязки.**
1. Выберите **привязку https,** а затем выберите **Edit.**
1. В поле сертификата TLS/SSL выберите сертификат **SharePoint,** а затем выберите **OK.**

Теперь вы можете получить доступ к сайту SharePoint извне через прокси-приложение Azure AD.

## <a name="step-3-configure-kerberos-constrained-delegation"></a>Шаг 3: Настройка Kerberos ограниченная делегация

Пользователи сначала будут аутентифицировать сяврин в Azure AD, а затем в SharePoint, используя Kerberos через разъем Azure AD Proxy. Чтобы разъем получил токен Kerberos от имени пользователя Azure AD, необходимо настроить kerberos Constrained Delegation (KCD) с переходом протокола. Чтобы узнать больше о KCD, [см.](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11))

### <a name="set-the-spn-for-the-sharepoint-service-account"></a>Установите SPN для учетной записи службы SharePoint

В этой статье, внутренний URL `https://sharepoint`, и поэтому основное `HTTP/sharepoint`имя службы (SPN) является . Эти значения должны замениться значениями, которые соответствуют вашей среде.
Чтобы зарегистрировать `HTTP/sharepoint` SPN для учетной `Contoso\spapppool`записи пула приложений SharePoint, запустите следующую команду из запроса команды в качестве администратора домена:

`setspn -S HTTP/sharepoint Contoso\spapppool`

Команда `Setspn` выполняет поиск SPN, прежде чем добавить его. Если SPN уже существует, вы видите ошибку **двойного значения SPN.** В этом случае рассмотрите возможность удаления существующего SPN, если он не установлен под правильной учетной записью пула приложений. Вы можете убедиться, что SPN `Setspn` был успешно добавлен, запустив команду с опцией -L. Дополнительные сведения о команде setspn см. в [этой статье](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc731241(v=ws.11)).

### <a name="make-sure-the-connector-is-trusted-for-delegation-to-the-spn-that-was-added-to-the-sharepoint-application-pool-account"></a>Убедитесь, что разъем доверен для делегирования в SPN, который был добавлен в пул пула приложений SharePoint

Настройте ограниченное делегирование Kerberos, чтобы служба прокси приложения Azure AD могла делегировать удостоверения пользователей для учетной записи пула приложений SharePoint. Для этого настройте для соединителя прокси приложения возможность получать билеты Kerberos для пользователей, которые прошли проверку подлинности в Azure AD. Затем этот сервер передает контекст целевому приложению (в данном случае SharePoint).

Чтобы настроить KCD, выполните следующие действия для каждого разъема машины:

1. Восватрите контроллер домена в качестве администратора домена, а затем откройте активных пользователей каталогов и компьютеров.
1. Найдите компьютер под управлением разъема Azure AD Proxy. В этом примере это сам сервер SharePoint.
1. Дважды щелкните на компьютер, а затем выберите вкладку **«Делегация».**
1. Убедитесь, что варианты делегирования настроены на **доверие этот компьютер для делегирования в указанные услуги только**. а затем — **Use any authentication protocol** (Использовать любой протокол проверки подлинности).
1. Выберите кнопку **Добавить,** выберите **пользователей или компьютеров**и найдите учетную запись пула приложений SharePoint. Например: `Contoso\spapppool`.
1. В списке имен участников-служб выберите того, который вы создали ранее для учетной записи службы.
1. Выберите **OK,** а затем выберите **OK** снова, чтобы сохранить изменения.
  
   ![Параметры делегирования](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

Теперь вы готовы войти в SharePoint, используя внешний URL и проверить подлинность с помощью Azure.

## <a name="troubleshoot-sign-in-errors"></a>Ошибки в вхомошибоза

Если вход на сайт не работает, вы можете получить более подробную информацию о проблеме в журналах Connector: Из машины, работающей на разъете, откройте просмотр событий, перейдите в **журналы** > приложений и служб**Microsoft** > **AadApplicationProxy** > **Connector**и проинспектируйте журнал **Admin.**

## <a name="next-steps"></a>Дальнейшие действия

* [Работа с пользовательскими доменами в прокси приложения Azure AD](application-proxy-configure-custom-domain.md)
* [Понять разъемы прокси-приложений Azure AD](application-proxy-connectors.md)
