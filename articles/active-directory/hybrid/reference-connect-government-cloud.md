---
title: 'Azure AD Connect: рекомендации по гибридной идентификации для облака Azure для государственных организаций'
description: Специальные рекомендации по развертыванию Azure AD Connect в облаке Azure для государственных организаций.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: acdc99ca50255bd9b75828f0a051f364c5218471
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83115495"
---
# <a name="hybrid-identity-considerations-for-the-azure-government-cloud"></a>Рекомендации по гибридной идентификации для облака Azure для государственных организаций

В этой статье приводятся рекомендации по интеграции гибридной среды с Microsoft Azure для государственных организацийным облаком. Эти сведения предоставляются в качестве справочника для администраторов и архитекторов, работающих с облаком Azure для государственных организаций.

> [!NOTE]
> Чтобы интегрировать локальную среду Microsoft Azure Active Directory (Azure AD) с облаком Azure для государственных организаций, необходимо выполнить обновление до последней версии [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).

Полный список СШАных конечных точек обороны для государственных организаций см. в [документации](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints).

## <a name="azure-ad-pass-through-authentication"></a>Сквозная аутентификация Azure AD

Ниже приведены сведения о реализации сквозной проверки подлинности и облака Azure для государственных организаций.

### <a name="allow-access-to-urls"></a>Разрешение доступа к URL-адресам

Перед развертыванием агента сквозной аутентификации проверьте, существует ли брандмауэр между вашими серверами и Azure AD. Если брандмауэр или прокси-сервер допускает программы, заблокированные или защищенные службой доменных имен (DNS), добавьте следующие подключения.

> [!NOTE]
> Приведенные ниже рекомендации также применимы к установке [соединителя azure AD application proxy](https://aka.ms/whyappproxy) для сред Azure для государственных организаций.

|URL-адрес |Как он используется|
|-----|-----|
|&#42;. msappproxy.us</br>&#42;.servicebus.usgovcloudapi.net|Агент использует эти URL-адреса для взаимодействия с облачной службой Azure AD. |
|mscrl.microsoft.us:80 </br>crl.microsoft.us:80 </br>ocsp.msocsp.us:80 </br>www.microsoft.us:80| Агент использует эти URL-адреса для проверки сертификатов.|
|login.windows.us </br>secure.aadcdn.microsoftonline-p.com </br>&#42;. microsoftonline.us </br>&#42;. microsoftonline-p.us </br>&#42;. msauth.net </br>&#42;. msauthimages.net </br>&#42;. msecnd.net</br>&#42;. msftauth.net </br>&#42;. msftauthimages.net</br>&#42;. phonefactor.net </br>enterpriseregistration.windows.net</br>management.azure.com </br>policykeyservice.dc.ad.msft.net</br>ctdl.windowsupdate.us:80| Агент использует эти URL-адреса в процессе регистрации.

### <a name="install-the-agent-for-the-azure-government-cloud"></a>Установка агента для облака Azure для государственных организаций

Выполните следующие действия, чтобы установить агент для облака Azure для государственных организаций:

1. В терминале командной строки перейдите в папку, содержащую исполняемый файл, который устанавливает агент.
1. Выполните следующие команды, которые указывают, что установка предназначена для Azure для государственных организаций.

   Для сквозной проверки подлинности:

   ```
   AADConnectAuthAgentSetup.exe ENVIRONMENTNAME="AzureUSGovernment"
   ```

   Для прокси приложения:

   ```
   AADApplicationProxyConnectorInstaller.exe ENVIRONMENTNAME="AzureUSGovernment" 
   ```

## <a name="single-sign-on"></a>Единый вход

### <a name="set-up-your-azure-ad-connect-server"></a>Настройка сервера Azure AD Connect

Если в качестве метода входа используется сквозная проверка подлинности, дополнительная проверка необходимых компонентов не требуется. Если вы используете синхронизацию хэшей паролей в качестве метода входа и между Azure AD Connect и Azure AD используется брандмауэр, убедитесь, что:

- Используйте Azure AD Connect версии 1.1.644.0 или более поздней.
- Если брандмауэр или прокси-сервер разрешает блокированные или защищенные программы DNS, добавьте подключения к URL-адресам &#42;. msappproxy.us через порт 443.

  В противном случае разрешите доступ к диапазонам IP-адресов центра обработки данных Azure, которые обновляются еженедельно. Это условие применяется только при включении функции. Это не требуется для реальных входов пользователей.

### <a name="roll-out-seamless-single-sign-on"></a>Развертывание простого единого входа

Вы можете постепенно развернуть простой единый вход Azure AD для пользователей, выполнив следующие инструкции. Начните с добавления URL-адреса Azure AD [https://autologon.microsoft.us](https://autologon.microsoft.us) во все или выбранные параметры зоны интрасети пользователей с помощью групповая политика в Active Directory.

Кроме того, необходимо включить параметр политики зоны интрасети **Разрешить обновления в строке состояния с помощью сценария, используя групповая политика**.

## <a name="browser-considerations"></a>Рекомендации для браузера

### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox (все платформы)

Mozilla Firefox не выполняет аутентификацию Kerberos автоматически. Каждый пользователь должен вручную добавить URL-адрес Azure AD в параметры Firefox, выполнив следующие действия:

1. Запустите Firefox и введите **about: config**   в адресной строке. Закройте все уведомления, которые могут отображаться.
1. Найдите параметр **Network. Negotiate-auth. Trusted-URI**   . Этот параметр выводит список сайтов, заслуживающих доверия Firefox для проверки подлинности Kerberos.
1. Щелкните правой кнопкой мыши имя предпочтения и выберите пункт **изменить**.
1. Введите  [**https://autologon.microsoft.us**](https://autologon.microsoft.us**)   в поле.
1. Нажмите кнопку **ОК**   , а затем снова откройте браузер.

### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Microsoft ребро на основе Chromium (все платформы)

Если вы переопределили  `AuthNegotiateDelegateAllowlist`   `AuthServerAllowlist`   Параметры политики или в своей среде, убедитесь, что к ним добавлен URL-адрес Azure AD [https://autologon.microsoft.us](https://autologon.microsoft.us) .

### <a name="google-chrome-all-platforms"></a>Google Chrome (все платформы)

Если вы переопределили  `AuthNegotiateDelegateWhitelist`   `AuthServerWhitelist`   Параметры политики или в своей среде, убедитесь, что к ним добавлен URL-адрес Azure AD [https://autologon.microsoft.us](https://autologon.microsoft.us) .

## <a name="next-steps"></a>Дальнейшие действия

- [Сквозная проверка подлинности](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)
- [Единый вход](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites)
