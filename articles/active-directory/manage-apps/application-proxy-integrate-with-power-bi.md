---
title: Включить удаленный доступ к Power BI с помощью прокси-приложения Azure AD
description: Охватывает основы о том, как интегрировать предприимчивый Power BI с Azure AD Application Proxy.
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
ms.date: 07/25/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc96c94152b39cc70cfc4553690faaa5b9cb8d20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77111574"
---
# <a name="enable-remote-access-to-power-bi-mobile-with-azure-ad-application-proxy"></a>Обеспечение удаленного доступа к Power BI Mobile с помощью Azure Active Directory Application Proxy

В этой статье обсуждается, как использовать прокси-сервер Azure AD Application, чтобы позволить мобильному приложению Power BI подключиться к серверу отчетов Power BI (PBIRS) и службам отчетности серверов серверов S'L (SSRS) 2016 и позже. Благодаря этой интеграции пользователи, находившиеся вдали от корпоративной сети, могут получить доступ к своим отчетам Power BI из мобильного приложения Power BI и быть защищены аутентификацией Azure AD. Эта защита включает в себя [преимущества безопасности,](application-proxy-security.md#security-benefits) такие как условный доступ и многофакторная аутентификация.  

## <a name="prerequisites"></a>Предварительные требования

В этой статье предполагается, что вы уже развернули службы отчетов и [включили прокси-сервер приложений.](application-proxy-add-on-premises-application.md)

- Включение приложения Proxy требует установки разъема на сервере Windows и выполнения [предпосылок,](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) чтобы разъем мог общаться с службами Azure AD.  
- При публикации Power BI мы рекомендуем использовать одни и те же внутренние и внешние домены. Чтобы узнать больше о пользовательских доменов, [см. Работа с пользовательскими доменами в приложении Прокси](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-domain).
- Эта интеграция доступна для **приложения Power BI Mobile iOS и Android.**

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>Шаг 1: Настройка Kerberos ограниченная делегация (KCD)

Для локальных приложений, использующих проверку подлинности Windows, единый вход можно обеспечить с помощью протокола проверки подлинности Kerberos и функции, называемой ограниченным делегированием Kerberos (KCD). При настройке KCD позволяет разъему прокси-приложений получить маркер Windows для пользователя, даже если пользователь не зарегистрировался напрямую в Windows. Чтобы узнать больше о KCD, см. [Kerberos Ограниченная делегация Обзор](https://technet.microsoft.com/library/jj553400.aspx) и [Kerberos ограничена делегация для одного входного в ваших приложений с приложением прокси](application-proxy-configure-single-sign-on-with-kcd.md).

На стороне служб Reporting Services не требуется сложная настройка. Просто убедитесь, что у вас есть действительное имя главного обслуживания (SPN), чтобы обеспечить надлежащее аутентификацию Kerberos. Также убедитесь, что сервер служб отчетности включен для проверки подлинности.

Чтобы настроить KCD для служб отчетности, продолжайте следующие шаги.

### <a name="configure-the-service-principal-name-spn"></a>Настройка главного имени службы (SPN)

Имя субъекта-службы — это уникальный идентификатор для службы, которая использует проверку подлинности Kerberos. Вы должны убедиться, что у вас есть надлежащий подарок HTTP SPN для сервера отчетов. Руководство по настройке правильного имени субъекта-службы (SPN) для сервера отчетов см. в статье о [регистрации имени субъекта-службы (SPN) для сервера отчетов](https://msdn.microsoft.com/library/cc281382.aspx).
Чтобы проверить добавление имени субъекта-службы, выполните команду Setspn с параметром -L. Дополнительные сведения о команде setspn см. в [этой статье](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spn-setspn-syntax.aspx).

### <a name="enable-negotiate-authentication"></a>Включить проверку подлинности переговоров

Чтобы сервер отчета мог использовать аутентификацию Kerberos, настройте тип аутентификации сервера отчета на RSWindowsNegotiate. Настроили эту настройку с помощью файла rsreportserver.config.

```xml
<AuthenticationTypes>
    <RSWindowsNegotiate />
    <RSWindowsKerberos />
    <RSWindowsNTLM />
</AuthenticationTypes>
```

Дополнительные сведения см. в статьях об [изменении файла конфигурации Reporting Services ](https://msdn.microsoft.com/library/bb630448.aspx) и [настройке проверки подлинности Windows на сервере отчетов](https://msdn.microsoft.com/library/cc281253.aspx).

### <a name="ensure-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-reporting-services-application-pool-account"></a>Убедитесь, что коннектора доверяют делегированию в пул приложения SPN, добавленной в пул приложения Reporting Services
Настройте KCD таким образом, чтобы служба Azure AD Application Proxy была делегирована идентификаторам пользователей в пул пул приложений Reporting Services. Для этого настройте для соединителя прокси приложения возможность получать билеты Kerberos для пользователей, которые прошли проверку подлинности в Azure AD. Затем этот сервер передает контекст целевому приложению или службам отчетности в этом случае.

Чтобы настроить KCD, повторите следующие шаги для каждого разъема машины:

1. Восватрите контроллер домена в качестве администратора домена, а затем откройте **активных пользователей каталогов и компьютеров.**
2. Найдите компьютер, на котором запущен соединитель.  
3. Дважды щелкните на компьютер, а затем выберите вкладку **«Делегация».**
4. Установите настройки делегирования, чтобы **доверять этому компьютеру только для делегирования указанным службам.** а затем — **Use any authentication protocol** (Использовать любой протокол проверки подлинности).
5. Выберите **Добавить,** а затем выберите **пользователей или компьютеров.**
6. Введите учетную запись службы, которую вы используете для служб отчетности. Это учетная запись, в которую вы добавили имя субъекта-службы во время настройки Reporting Services.
7. Нажмите кнопку **ОК**. Чтобы сохранить изменения, нажмите **OK** еще раз.

Для получения дополнительной информации см. [Kerberos Constrained Delegation для единого ввоза в приложения с помощью приложения Proxy](application-proxy-configure-single-sign-on-with-kcd.md).

## <a name="step-2-publish-report-services-through-azure-ad-application-proxy"></a>Шаг 2: Публиковать службы отчетов через прокси-приложение Azure AD

Теперь вы готовы настроить прокси-сервер Azure AD Application.

1. Публикация служб отчетов через application Proxy со следующими настройками. Для пошаговых инструкций о том, как опубликовать приложение через Application Proxy, см. [Издательские приложения с помощью Azure AD Application Proxy.](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)
   - **Внутренний URL:** Введите URL-адрес сервера отчетов, до которого разъем может достичь в корпоративной сети. Убедитесь, что этот URL довется с сервера, на который установлен разъем. Наилучшей практикой является использование домена `https://servername/` верхнего уровня, например, чтобы избежать проблем с подпатами, опубликованными через Application Proxy. Например, `https://servername/` использовать, `https://servername/reports/` `https://servername/reportserver/`а не или .
     > [!NOTE]
     > Рекомендуем использовать безопасное подключение HTTPS к серверу отчетов. Для получения информации о том, как это сделать, можно найти на [сервере отчетов на родном режиме.](https://docs.microsoft.com/sql/reporting-services/security/configure-ssl-connections-on-a-native-mode-report-server?view=sql-server-2017)
   - **Внешний URL:** Введите общедоступный URL мобильное приложение Power BI подключится к. Например, при использовании пользовательского домена может выглядеть `https://reports.contoso.com` пользовательский домен. Чтобы использовать пользовательский домен, загрузите сертификат для домена и направьте запись DNS на домен по умолчанию msappproxy.net для вашего приложения. Для получения подробных мер смотрите [работа с пользовательскими доменами в прокси-приложении Azure AD.](application-proxy-configure-custom-domain.md)

   - **Метод предварительной аутентификации**: Активный каталог Azure

2. После публикации приложения необходимо настроить параметры единого входа с помощью следующих действий:

   а. На странице приложения на портале выберите **Единый вход**.

   b. Для **единого режима регистрации**выберите **интегрированную аутентификацию Windows.**

   c. Задайте для параметра **Внутреннее имя субъекта-службы приложения** значение, заданное ранее.  

   d. Выберите **делегированную идентификацию для входа**, которую соединитель сможет использовать от имени пользователей. Для получения дополнительной информации [см.](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities)

   д) Нажмите кнопку **Сохранить**, чтобы сохранить изменения.

Чтобы закончить настройку приложения, перейдите в раздел **«Пользователи и группы»** и назначьте пользователям доступ к этому приложению.

## <a name="step-3-modify-the-reply-uris-for-the-application"></a>Шаг 3: Изменить ответ URI для приложения

Прежде чем мобильное приложение Power BI сможет подключиться и получить доступ к службам отчетов, необходимо настроить регистрацию приложений, которая была автоматически создана для вас на шаг2. 

1. На **странице** обзора active Directory Azure выберите **регистрацию приложений.**
2. В рамках вкладки **«Все приложения»** поиск приложения, созданного в шаге 2.
3. Выберите приложение, а затем выберите **аутентификацию.**
4. Добавьте следующие URIs перенаправления на основе того, какую платформу вы используете.

   При настройке приложения для Power BI Mobile **iOS,** добавьте следующие ПЕРЕнаправить URI типа общественного клиента (Mobile & Desktop):
   - `msauth://code/mspbi-adal%3a%2f%2fcom.microsoft.powerbimobile`
   - `msauth://code/mspbi-adalms%3a%2f%2fcom.microsoft.powerbimobilems`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `mspbi-adalms://com.microsoft.powerbimobilems`
   
   При настройке приложения для Power BI Mobile **Android,** добавить следующие перенаправить URIs типа общественного клиента (Мобильный & desktop):
   - `urn:ietf:wg:oauth:2.0:oob`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `msauth://com.microsoft.powerbim/g79ekQEgXBL5foHfTlO2TPawrbI%3D` 
   - `msauth://com.microsoft.powerbim/izba1HXNWrSmQ7ZvMXgqeZPtNEU%3D`

   > [!IMPORTANT]
   > Для правильной работы приложения необходимо добавить УРИ-инаков перенаправления. Если вы настраиваете приложение как для Power BI Mobile iOS, так и для Android, добавьте следующее перенаправление URI типа Public `urn:ietf:wg:oauth:2.0:oob`Client (Mobile & Desktop) в список URL-изображений, настроенных для iOS: .

## <a name="step-4-connect-from-the-power-bi-mobile-app"></a>Шаг 4: Подключение от Power BI Мобильное приложение

1. В мобильном приложении Power BI подключитесь к экземпляру Службы отчетности. Для этого введите **внешний URL-адрес** приложения, которое вы опубликовали через приложение Proxy.

   ![Мобильное приложение Power BI с внешним URL](media/application-proxy-integrate-with-power-bi/app-proxy-power-bi-mobile-app.png)

2. Выберите **Подключиться**. Вы будете направлены на значк Azure Active Directory на странице.

3. Введите действительные учетные данные для пользователя и выберите **войти в систему.** Элементы с сервера служб отчетности будут видеться.

## <a name="step-5-configure-intune-policy-for-managed-devices-optional"></a>Шаг 5: Настройка политики Intune для управляемых устройств (по желанию)

Вы можете использовать Microsoft Intune для управления клиентскими приложениями, которые используют сотрудники вашей компании. Intune позволяет использовать такие возможности, как шифрование данных и дополнительные требования к доступу. Чтобы узнать больше об управлении приложениями с помощью Intune, см. Чтобы мобильное приложение Power BI работало с политикой Intune, используйте следующие действия.

1. Перейдите в **Active Directory Azure,** а затем **регистрацию приложений.**
2. Выберите приложение, настроенное в шаге 3 при регистрации приложения для нативного клиента.
3. На странице приложения выберите **разрешения API**.
4. Нажмите **Добавить разрешение**. 
5. Под **AIS моя организация использует,** поиск "Microsoft Mobile Application Management" и выберите его.
6. Добавьте в приложение разрешение **DeviceManagementManagedApps.ReadWrite**
7. Нажмите **Грант админ согласие** предоставить разрешение доступа к приложению.
8. Настройте политику Intune, которую вы хотите, сославшись на [как создать и назначить политики защиты приложений.](https://docs.microsoft.com/intune/app-protection-policies)

## <a name="troubleshooting"></a>Устранение неполадок

Если приложение возвращает страницу ошибки после попытки загрузить отчет более чем на несколько минут, возможно, потребуется изменить настройку тайм-аута. По умолчанию Application Proxy поддерживает приложения, на ответ на запрос — до 85 секунд. Чтобы удлинить эту настройку до 180 секунд, выберите тайм-аут бэк-энда на **страницу настроек** App Proxy для приложения. Советы о том, как создавать быстрые и надежные отчеты, [см.](https://docs.microsoft.com/power-bi/power-bi-reports-performance)

## <a name="next-steps"></a>Дальнейшие действия

- [Включить приложения нативного клиента для взаимодействия с прокси-приложениями](application-proxy-configure-native-client-application.md)
- [Просмотр локальных отчетов на сервере отчетов и ключевых показателей эффективности в мобильных приложениях Power BI](https://docs.microsoft.com/power-bi/consumer/mobile/mobile-app-ssrs-kpis-mobile-on-premises-reports)
