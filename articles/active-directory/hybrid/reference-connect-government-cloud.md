---
title: 'Azure AD Connect: рекомендации по гибридной идентификации для Azure для государственных организаций'
description: Специальные рекомендации по развертыванию Azure AD Connect в облаке для государственных организаций.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81378929"
---
# <a name="hybrid-identity-considerations-for-azure-government"></a>Рекомендации по гибридной идентификации для Azure для государственных организаций 
В следующем документе приводятся рекомендации по реализации гибридной среды с облаком Azure для государственных организаций.  Эти сведения предоставляются в качестве справочных сведений для администраторов и архитекторов, работающих с облаком Azure для государственных организаций.  
> [!NOTE] 
> Чтобы интегрировать локальную среду AD с Azure Говернемнт Cloud, необходимо выполнить обновление до последней версии [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594). 

> [!NOTE] 
> Полный список конечных точек DoD для государственных организаций США см. в [документации](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) . 

## <a name="pass-through-authentication"></a>Сквозная проверка подлинности 
Ниже приведены сведения о реализации сквозной проверки подлинности (PTA) и облака Azure для государственных организаций.

### <a name="allow-access-to-urls"></a>Разрешение доступа к URL-адресам  
Перед развертыванием агента сквозной аутентификации проверьте наличие брандмауэра между вашими серверами и Azure AD. Если брандмауэр или прокси-сервер допускает DNS список разрешений, добавьте следующие подключения: 
> [!NOTE]
> Приведенные ниже рекомендации также применимы к установке [соединителя прокси приложения](https://aka.ms/whyappproxy) для сред Azure для государственных организаций.

|URL-адрес |Как он используется|
|-----|-----| 
|*. msappproxy.us *. servicebus.usgovcloudapi.net|Обмен данными между агентом и облачной службой Azure AD |
|mscrl.microsoft.us:80 crl.microsoft.us:80 </br>ocsp.msocsp.us:80 www.microsoft.us:80| Агент использует эти URL-адреса для проверки сертификатов.| 
|Login.Windows.US Secure.aadcdn.microsoftonline-p.com *. microsoftonline.US </br> *. microsoftonline-p.US </br>*. msauth.NET </br> *. msauthimages.NET </br>*. msecnd.NET</br>*. msftauth.NET </br>*. msftauthimages.NET</br>*. phonefactor.NET </br>enterpriseregistration.windows.net</br>management.azure.com </br>policykeyservice.dc.ad.msft.net</br>ctdl.windowsupdate.us:80| Агент использует эти URL-адреса в процессе регистрации.| 

### <a name="install-the-agent-for-the-azure-government-cloud"></a>Установка агента для облака Azure для государственных организаций 
Чтобы установить агент для облака Azure для государственных организаций, необходимо выполнить следующие действия. в терминале командной строки перейдите в папку, где находится исполняемый файл для установки агента. Выполните следующую команду, которая указывает установку для Azure для государственных организаций. 

Для сквозной проверки подлинности: 
```
AADConnectAuthAgentSetup.exe ENVIRONMENTNAME="AzureUSGovernment"
```

Для прокси приложения:
```
AADApplicationProxyConnectorInstaller.exe ENVIRONMENTNAME="AzureUSGovernment" 
```

## <a name="single-sign-on"></a>Единый вход 
Настройка сервера Azure AD Connect. Если в качестве метода входа используется сквозная проверка подлинности, дополнительная проверка необходимых компонентов не требуется. Если вы используете синхронизацию хэшей паролей в качестве метода входа и при наличии брандмауэра между Azure AD Connect и Azure AD, убедитесь, что:
- Используется Azure AD Connect версии 1.1.644.0 или более поздней версии. 
- Если брандмауэр или прокси-сервер допускает список разрешений DNS, добавьте подключения к URL-адресам *. msapproxy.us через порт 443. В противном случае разрешите доступ к диапазонам IP-адресов центра обработки данных Azure, которые обновляются еженедельно. Это предварительное условие применяется только в том случае, если вы включаете функцию. Его выполнение не обязательно для фактического входа пользователя. 

### <a name="rolling-out-seamless-sso"></a>Развертывание простого единого входа 
Вы можете постепенно развертывать простой единый вход для пользователей с помощью инструкций, приведенных ниже. Для начала добавьте следующий URL-адрес Azure AD в параметры зоны интрасети "все" или "Выбранные пользователи" с помощью групповая политика в Active Directory:https://autologon.microsoft.us 

Кроме того, необходимо включить параметр политики зоны интрасети с именем разрешить обновления в строке состояния с помощью сценария, используя групповая политика. Рекомендации для браузера Mozilla Firefox (все платформы) Mozilla Firefox не использует проверку подлинности Kerberos автоматически. Каждый пользователь должен вручную добавить URL-адрес Azure AD в параметры Firefox, выполнив следующие действия: 
1. Запустите Firefox и введите about: config в адресной строке. Проигнорируйте все отображаемые уведомления. 
2. Найдите параметр Network. Negotiate-auth. Trusted-URI. Этот параметр выводит список доверенных сайтов в Firefox для проверки подлинности Kerberos. 
3. Щелкните правой кнопкой мыши и выберите Изменить. 
4. Введите https://autologon.microsoft.us в поле.
5. Нажмите кнопку ОК, а затем снова откройте браузер. 

### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Microsoft ребро на основе Chromium (все платформы) 
Если `AuthNegotiateDelegateAllowlist` вы переопределили параметры или `AuthServerAllowlist` политики в среде, убедитесь, что вы добавили URL-адресhttps://autologon.microsoft.us) Azure AD. 

### <a name="google-chrome-all-platforms"></a>Google Chrome (все платформы) 
Если `AuthNegotiateDelegateWhitelist` вы переопределили параметры или `AuthServerWhitelist` политики в среде, убедитесь, что вы добавили URL-адресhttps://autologon.microsoft.us) Azure AD. 

## <a name="next-steps"></a>Дальнейшие действия
[Единый вход сквозной проверки подлинности](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)
[Single Sign-on](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites) 
