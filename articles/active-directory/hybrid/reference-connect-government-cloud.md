---
title: 'Azure AD Connect: Соображения гибридных идентификационных данных для правительства Azure'
description: Особые соображения для развертывания Azure AD Connect с правительственным облаком.
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
ms.openlocfilehash: 2ad28beb68afb5207e7b36c5d76c4dac808c5114
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81378929"
---
# <a name="hybrid-identity-considerations-for-azure-government"></a>Соображения гибридной идентичности для правительства Azure 
В следующем документе описаны соображения по внедрению гибридной среды с облаком правительства Azure.  Эта информация предоставляется в качестве ссылки для администраторов и архитекторов, работающих с облаком правительства Azure.  
> [!NOTE] 
> Для интеграции предварительной среды AD с облаком Azure Governemnt необходимо обновиться до последнего выпуска [Azure AD Connect.](https://www.microsoft.com/download/details.aspx?id=47594) 

> [!NOTE] 
> Для получения полного списка конечных пунктов правительства США, обратитесь к [документации](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) 

## <a name="pass-through-authentication"></a>Сквозная проверка подлинности 
Приведена следующая информация для внедрения сквозной аутентификации (PTA) и облака правительства Azure.

### <a name="allow-access-to-urls"></a>Разрешение доступа к URL-адресам  
Перед развертыванием агента проверки подлинности убедитесь, есть ли между серверами и Azure AD брандмауэр. Если ваш брандмауэр или прокси позволяет DNS белый список, добавить следующие соединения: 
> [!NOTE]
> Следующее руководство также применяется к установке [разъема прокси-сервера приложения](https://aka.ms/whyappproxy) для правительственных сред Azure.

|URL-адрес |Как он используется|
|-----|-----| 
|В.msappproxy.us.servicebus.usgovcloudapi.net|Связь между агентом и облачным сервисом Azure AD |
|mscrl.microsoft.us:80 crl.microsoft.us:80 </br>ocsp.msocsp.us:80 www.microsoft.us:80| Агент использует эти URL-адреса для проверки сертификатов.| 
|login.windows.us secure.aadcdn.microsoftonline-p.com *.microsoftonline.us </br> *.microsoftonline-p.us </br>*.msauth.net </br> *.msauthimages.net </br>*.msecnd.net</br>*.msftauth.net </br>*.msftauthimages.net</br>*.phonefactor.net </br>enterpriseregistration.windows.net</br>management.azure.com </br>policykeyservice.dc.ad.msft.net</br>ctdl.windowsupdate.us:80| Агент использует эти URL-адреса в процессе регистрации.| 

### <a name="install-the-agent-for-the-azure-government-cloud"></a>Установка агента для облака правительства Azure 
Для установки агента для облака azure Government необходимо следовать следующим шагам: в терминале командной строки перейдите в папку, где находится выполняемая для установки агента. Выполнить следующую команду, которая определяет установку для правительства Azure. 

Для прохождения аутентификации: 
```
AADConnectAuthAgentSetup.exe ENVIRONMENTNAME="AzureUSGovernment"
```

Для прокси-сервера приложения:
```
AADApplicationProxyConnectorInstaller.exe ENVIRONMENTNAME="AzureUSGovernment" 
```

## <a name="single-sign-on"></a>Единый вход 
Настройка сервера Azure AD Connect: Если вы используете проверку подлинности Pass-through в качестве метода входной связи, дополнительная проверка не требуется. Если вы используете синхронизацию хэша паролей в качестве метода входного вввоза, и если между Azure AD Connect и Azure AD существует брандмауэр, убедитесь, что:
- Используется Azure AD Connect версии 1.1.644.0 или более поздней версии. 
- Если ваш брандмауэр или прокси позволяет DNS белый список, добавить соединения в msapproxy.us URL-адреса по порту 443. В противном случае разрешите доступ к IP-диапазонам Центра обработки данных Azure, которые обновляются еженедельно. Это предварительное условие применяется только в том случае, если вы включаете функцию. Его выполнение не обязательно для фактического входа пользователя. 

### <a name="rolling-out-seamless-sso"></a>Развертывание бесшовных SSO 
Вы можете постепенно развертывать простой единый вход для пользователей с помощью инструкций, приведенных ниже. Вы начинаете с добавления следующего URL-адреса Azure AD в настройки зоны intranet всех или выбранных пользователей, используя групповую политику в Active Directory:https://autologon.microsoft.us 

Кроме того, необходимо включить настройки политики зоны Интрасети под названием Разрешить обновления в бар статуса через скрипт через групповую политику. Браузер соображения Mozilla Firefox (все платформы) Mozilla Firefox не использует автоматически Kerberos аутентификации. Каждый пользователь должен вручную добавить URL-адрес Azure AD в параметры Firefox, выполнив следующие действия: 
1. Выполнить Firefox и введите около:конфигурация в адресном баре. Проигнорируйте все отображаемые уведомления. 
2. Поиск по сети.negotiate-auth.trusted-uris предпочтения. Этот параметр выводит список доверенных сайтов в Firefox для проверки подлинности Kerberos. 
3. Нажмите правой кнопкой мыши и выберите Modify. 
4. Введите https://autologon.microsoft.us в поле.
5. Выберите OK, а затем повторно откройте браузер. 

### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Microsoft Edge на основе Chromium (все платформы) 
Если `AuthNegotiateDelegateAllowlist` вы переопределили `AuthServerAllowlist` или настройки политики в вашей среде, убедитесь, что вы добавите URL-адрес Azure AD (кhttps://autologon.microsoft.us) ним также. 

### <a name="google-chrome-all-platforms"></a>Google Chrome (все платформы) 
Если `AuthNegotiateDelegateWhitelist` вы переопределили `AuthServerWhitelist` или настройки политики в вашей среде, убедитесь, что вы добавите URL-адрес Azure AD (кhttps://autologon.microsoft.us) ним также. 

## <a name="next-steps"></a>Следующие шаги
[Проходная аутентификация](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)
[Единый входе](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites) 
