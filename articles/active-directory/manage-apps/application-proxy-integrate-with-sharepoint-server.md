---
title: Настройка удаленного доступа к SharePoint с помощью прокси приложения Azure AD | Документы Майкрософт
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
ms.openlocfilehash: a8f9cba1072866a3efdeb7b99981d0bfda22ab00
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286097"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Настройка удаленного доступа к SharePoint с помощью прокси приложения Azure AD

В этом пошаговом руководство объясняется, как интегрировать локальную ферму SharePoint с прокси приложения Azure Active Directory (Azure AD).

## <a name="prerequisites"></a>Предварительные требования

Для выполнения настройки необходимы следующие ресурсы:
- Ферма SharePoint 2013 или более поздняя версия.
- Клиент Azure Active Directory с планом, включающим прокси приложения. Узнайте больше о [Azure Active Directory планах и ценах](https://azure.microsoft.com/pricing/details/active-directory/).
- [Пользовательский проверенный домен](../fundamentals/add-custom-domain.md) в клиенте Azure AD.
- Локальные Active Directory синхронизируются с Azure AD Connect с пользователями, которые могут [входить в Azure](../hybrid/plan-connect-user-signin.md).
- Соединитель прокси приложения установлен и работает на компьютере в корпоративном домене.

Для настройки SharePoint с помощью прокси приложения требуется два URL-адреса:
- Внешний URL-адрес, видимый для конечных пользователей и определенный в Azure Active Directory. Этот URL-адрес может использовать личный домен. Дополнительные сведения о [работе с пользовательскими доменами в Azure AD application proxy](application-proxy-configure-custom-domain.md).
- Внутренний URL-адрес, известный только в корпоративном домене и не использующийся напрямую.

> [!IMPORTANT]
> Чтобы убедиться, что ссылки сопоставлены правильно, следуйте следующим рекомендациям по внутреннему URL-адресу:
> - Используйте протокол HTTPS.
> - Не используйте пользовательские порты.
> - В корпоративной службе DNS создайте узел (A), указывающий на WFE SharePoint (или подсистему балансировки нагрузки), а не псевдоним (CName).

В этой статье будут использоваться следующие значения:
- Внутренний URL-адрес: `https://sharepoint`
- Внешний URL-адрес: `https://spsites-demo1984.msappproxy.net/`
- Учетная запись пула приложений для веб-приложения SharePoint: `Contoso\spapppool`

## <a name="step-1-configure-an-application-in-azure-ad-that-uses-application-proxy"></a>Шаг 1. Настройка приложения в Azure AD, использующего прокси приложения

На этом шаге вы создадите в клиенте Azure Active Directory приложение, которое будет использовать прокси приложения. Вы настроите внешний URL-адрес и укажите внутренний URL-адрес, который будет использоваться позже в SharePoint.

1. Создайте приложение, как описано в следующих параметрах. Пошаговые инструкции см. в статье [Публикация приложений с помощью прокси приложения Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).
   * **Внутренний URL-адрес**. Внутренний URL-адрес SharePoint, который будет задан позже в SharePoint, например `https://sharepoint`.
   * **Метод предварительной проверки подлинности**. Azure Active Directory
   * **Перевод веб-страниц в заголовках**. Нет
   * **URL-адрес перевода в тексте приложения**: Нет

   ![Публикация SharePoint в качестве приложения](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

1. После публикации приложения необходимо настроить параметры единого входа с помощью следующих действий:

   1. На странице приложения на портале выберите **Единый вход**.
   1. **В режиме единого входа**выберите **Встроенная проверка подлинности Windows**.
   1. Задайте для **внутреннего субъекта-службы приложения** значение, заданное ранее. В этом примере значением будет `HTTP/sharepoint`.
   1. В поле **делегированное удостоверение входа**выберите наиболее подходящий вариант для конфигурации леса Active Directory. Например, если в лесу есть один домен AD, выберите **Локальное имя учетной записи SAM** (как показано ниже), но если пользователи находятся не в том же домене, что и SharePoint и серверы соединителя прокси приложения, выберите **локальный субъект-пользователь. имя** (не показано).

   ![Настройка встроенной проверки подлинности Windows для единого входа](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

1. Чтобы завершить настройку приложения, перейдите к разделу **Пользователи и группы** и назначьте доступ к этому приложению необходимым пользователям. 

## <a name="step-2-configure-the-sharepoint-web-application"></a>Шаг 2. Настройка веб-приложения SharePoint

Веб-приложение SharePoint должно быть настроено с использованием протокола Kerberos и соответствующих сопоставлений альтернативного доступа для правильной работы с Azure AD Application Proxy. Существует два возможных варианта.

1. Создайте новое веб-приложение и используйте только зону по умолчанию. Это предпочтительный вариант для обеспечения оптимальной работы с SharePoint (например, ссылки в оповещениях по электронной почте, созданные SharePoint, всегда указывают на зону по умолчанию).
1. Расширение существующего веб-приложения для настройки Kerberos в зоне, отличной от зоны по умолчанию.

> [!IMPORTANT]
> Независимо от зоны, которая будет использоваться, учетная запись пула приложений веб-приложения SharePoint должна быть учетной записью домена для правильной работы Kerberos.

### <a name="provision-the-sharepoint-web-application"></a>Подготавливает веб-приложение SharePoint

- При создании нового веб-приложения и использовании только зоны по умолчанию (предпочтительный вариант):

    1. Запустите **консоль управления SharePoint** и выполните следующий скрипт:

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

    1. Откройте страницу **центра администрирования SharePoint**.
    1. В разделе **Параметры системы** выберите **Настройка сопоставлений альтернативного доступа**. Откроется окно "Сопоставления для альтернативного доступа".
    1. Отфильтруйте экран с помощью нового веб-приложения и убедитесь, что вы видите нечто вроде этого:

       ![Сопоставления альтернативного доступа веб-приложения](./media/application-proxy-integrate-with-sharepoint-server/new-webapp-aam.png)

- При расширении существующего веб-приложения в новую зону (если вы не можете использовать зону по умолчанию):

    1. Запустите **консоль управления SharePoint** и выполните следующий скрипт:

       ```powershell
       # This scripts extends an existing web application to Internet zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment
       $webAppUrl = "http://spsites/"
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = Get-SPWebApplication $webAppUrl
       New-SPWebApplicationExtension -Name "SharePoint - AAD Proxy" -Identity $wa -SecureSocketsLayer -Zone Extranet -Url $externalUrl -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Extranet -Internal
       ```

    1. Откройте страницу **центра администрирования SharePoint**.
    1. В разделе **Параметры системы** выберите **Настройка сопоставлений альтернативного доступа**. Откроется окно "Сопоставления для альтернативного доступа".
    1. Отфильтруйте отображение веб-приложения, которое было расширено, и убедитесь, что вы видите нечто вроде этого:

        ![Сопоставления альтернативного доступа расширенного приложения](./media/application-proxy-integrate-with-sharepoint-server/extend-webapp-aam.png)

### <a name="ensure-that-the-sharepoint-web-application-is-running-under-a-domain-account"></a>Убедитесь, что веб-приложение SharePoint работает под учетной записью домена.

Выполните следующие действия, чтобы указать учетную запись, запускающую пул приложений веб-приложения SharePoint, и убедитесь, что это учетная запись домена.

1. Откройте страницу **центра администрирования SharePoint**.
1. Щелкните **Безопасность** и выберите **Настройка учетных записей служб**.
1. Выберите **пул веб-приложений — йоурвебаппликатионнаме**.

   ![Параметры для настройки учетной записи службы](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

1. Убедитесь, что **параметр выбрать учетную запись для этого компонента** Возвращает учетную запись домена, и помните, что он потребуется на следующем шаге.

### <a name="ensure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>Проверка того, что сертификат HTTPS настроен для использования сайта IIS в зоне экстрасети

Так как внутренний URL-адрес использует протокол HTTPS (`https://SharePoint/`), сертификат должен быть установлен на сайте IIS.

1. Откройте консоль Windows PowerShell
1. Выполните следующий скрипт, чтобы создать самозаверяющий сертификат, и добавьте его в My store на компьютере.

   ```powershell
   # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
   New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
   ```

   > [!IMPORTANT]
   > Самозаверяющие сертификаты допустимо использовать только для целей тестирования. В рабочей среде мы настоятельно рекомендуем использовать только сертификаты, выданные центром сертификации.

1. Откройте консоль службы IIS.
1. Разверните сервер в представлении дерева, разверните "Сайты", выберите узел "SharePoint – AAD Proxy" и щелкните **Привязки**.
1. Выберите привязку https и щелкните **Изменить...** .
1. В поле сертификата SSL выберите сертификат **SharePoint** и щелкните ОК.

Теперь вы можете получать внешний доступ к сайту SharePoint через прокси приложения Azure AD.

## <a name="step-3-configure-kerberos-constrained-delegation-kcd"></a>Шаг 3. Настройка ограниченного делегирования Kerberos

Пользователи сначала проходят проверку подлинности в Azure Active Directory, а затем — в SharePoint, используя Kerberos через соединитель прокси-сервера Azure AD. Чтобы разрешить соединителю получать маркер Kerberos от имени пользователя Azure Active Directory, необходимо настроить ограниченное делегирование Kerberos при смене протокола. Дополнительные сведения об ограниченном делегировании Kerberos см. в [этой статье](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11)).

### <a name="set-the-service-principal-name-for-the-sharepoint-service-account"></a>Задание имени субъекта-службы для учетной записи службы SharePoint

В этой статье внутренний URL-адрес имеет значение `https://sharepoint`, поэтому имя субъекта-службы — @no__t – 1. Эти значения необходимо заменить соответствующими параметрами среды.
Чтобы зарегистрировать SPN `HTTP/sharepoint` для учетной записи пула приложений SharePoint `Contoso\spapppool`, выполните следующую команду из командной строки в качестве администратора домена:

`setspn -S HTTP/sharepoint Contoso\spapppool`

Команда setspn выполняет поиск имени субъекта-службы, прежде чем его добавить. Если он уже существует, вы увидите ошибку **повторяющегося значения имени субъекта-службы** . В этом случае рекомендуется удалить существующее имя субъекта-службы, если оно не задано в правильной учетной записи пула приложений.  
Чтобы убедиться, что имя участника-службы успешно добавлено, запустите команду SetSPN с параметром – L. Дополнительные сведения о команде setspn см. в [этой статье](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc731241(v=ws.11)).

### <a name="ensure-that-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-sharepoint-application-pool-account"></a>Убедитесь, что соединитель является доверенным для делегирования на имя субъекта-службы, добавленное в учетную запись пула приложений SharePoint.

Настройте ограниченное делегирование Kerberos, чтобы служба прокси приложения Azure AD могла делегировать удостоверения пользователей для учетной записи пула приложений SharePoint. Для этого настройте для соединителя прокси приложения возможность получать билеты Kerberos для пользователей, которые прошли проверку подлинности в Azure AD. Затем этот сервер передает контекст в целевое приложение (SharePoint в нашем случае).

Чтобы настроить ограниченное делегирование Kerberos, выполните приведенные далее действия для каждого компьютера соединителя.

1. Войдите на контроллер домена в качестве администратора домена, а затем откройте **Active Directory пользователи и компьютеры**.
1. Найдите компьютер, на котором работает соединитель прокси-сервера Azure AD. В этом примере это сам сервер SharePoint.
1. Дважды щелкните имя компьютера, а затем перейдите на вкладку **Делегирование**.
1. Установите переключатель **Trust this computer for delegation to the specified services only** (Доверять этому компьютеру для делегирования только указанным службам), а затем — **Use any authentication protocol** (Использовать любой протокол проверки подлинности).
1. Нажмите кнопку **Добавить** , выберите **Пользователи или компьютеры**и укажите учетную запись пула приложений SharePoint, например `Contoso\spapppool`.
1. В списке имен участников-служб выберите того, который вы создали ранее для учетной записи службы.
1. Нажмите кнопку **ОК**. Нажмите кнопку **ОК** еще раз, чтобы сохранить изменения.
  
   ![Параметры делегирования](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

Теперь все готово для входа в SharePoint с использованием внешнего URL-адреса и проверки подлинности в Azure.

## <a name="troubleshoot-sign-in-errors"></a>Устранение ошибок входа

Если вход на сайт не работает, можно получить дополнительные сведения о данной ошибке в журналах соединителя: С компьютера, на котором работает соединитель, откройте средство просмотра событий, перейдите в раздел **журналы приложений и служб** > **Microsoft** > **AadApplicationProxy** > **Connector**и проверьте журнал **администратора** .

## <a name="next-steps"></a>Следующие шаги

* [Работа с пользовательскими доменами в прокси приложения Azure AD](application-proxy-configure-custom-domain.md)
* [Сведения о соединителях прокси приложения Azure AD](application-proxy-connectors.md)
