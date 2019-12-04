---
title: Необходимые условия для Azure AD Connect подготовки облака в Azure AD
description: В этом разделе описаны необходимые компоненты и требования к оборудованию для подготовки облака в облаке.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 155edf72a60e079a609853e953e3cf66024cc83c
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794254"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-provisioning"></a>Предварительные требования для подготовки облачных Azure AD Connect
В этом разделе приводятся рекомендации по выбору и использованию Azure AD Connect подготовки облака в качестве решения для идентификации.



## <a name="cloud-provisioning-agent-requirements"></a>Требования к агенту подготовки облака
Для использования Azure AD Connect подготовки облака потребуется следующее:
    
- Учетная запись глобального администратора для клиента Azure AD;
- локальный сервер для агента подготовки с Windows 2012 R2 или более поздней версии
- локальные конфигурации брандмауэра

В оставшейся части документа будут представлены пошаговые инструкции по выполнению этих предварительных требований.

### <a name="in-the-azure-active-directory-admin-center"></a>В центре администрирования Azure Active Directory

1. Создайте облачную учетную запись глобального администратора в клиенте Azure AD. Таким образом, вы сможете управлять конфигурацией клиента, если работа локальных служб завершится сбоем или они станут недоступными. Узнайте больше о [добавлении облачной учетной записи глобального администратора](../active-directory-users-create-azure-portal.md). Этот шаг очень важен, чтобы не потерять доступ к клиенту.
2. Добавьте одно [имя личного домена](../active-directory-domains-add-azure-portal.md) (или несколько) в клиент Azure AD. Пользователи могут выполнить вход с помощью одного из этих доменных имен.

### <a name="in-your-on-premises-environment"></a>В локальной среде

1. Идентификация присоединенного к домену сервера узла под управлением Windows Server 2012 R2 или более поздней версии с минимальным объемом ОЗУ 4 ГБ и .NET 4.7.1 + Runtime 

2. Если между серверами и Azure AD настроен брандмауэр, необходимо настроить указанные ниже элементы.
   - Убедитесь, что агенты могут выполнять *Исходящие* запросы к Azure AD через следующие порты:

     | Номер порта | Как он используется |
     | --- | --- |
     | **80** | Скачивание списков отзыва сертификатов при проверке SSL-сертификата. |
     | **443** | Обработка всего исходящего трафика для службы. |
     | **8080** (необязательно) | Агенты сообщают о своем состоянии каждые десять минут через порт 8080, если порт 443 недоступен. Это данные о состоянии отображаются на портале Azure AD. Порт 8080 _не_ используется для входа пользователей в систему. |
     
     Если брандмауэр применяет правила в соответствии с отправляющими трафик пользователями, откройте эти порты для трафика, поступающего от служб Windows, которые работают как сетевая служба.
   - Если брандмауэр или прокси-сервер позволяет указать надежные суффиксы, добавьте подключения к **\*. msappproxy.NET** и **\*. servicebus.Windows.NET**. Если нет, разрешите доступ к [диапазонам IP-адресов центра обработки данных Azure](https://www.microsoft.com/download/details.aspx?id=41653). Список диапазонов IP-адресов обновляется еженедельно.
   - Агентам требуется доступ к **Login.Windows.NET** и **Login.microsoftonline.com** для первоначальной регистрации. Откройте эти URL-адреса в брандмауэре.
   - Для проверки сертификата Разблокируйте следующие URL-адреса: **mscrl.Microsoft.com:80**, **CRL.Microsoft.com:80**, **OCSP.msocsp.com:80**и **www\.Microsoft.com:80**. Так как эти URL-адреса используются для проверки сертификатов в других продуктах Майкрософт, они уже могут быть разблокированы.

### <a name="verify-the-port"></a>Проверка порта
Чтобы убедиться, что Azure прослушивает порт 443 и что агент может взаимодействовать с ним, можно использовать следующую команду:

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Этот тест проверит, что агенты могут взаимодействовать с Azure через порт 443.  Откройте браузер и перейдите по указанному выше URL-адресу с сервера, на котором установлен агент.
![Службы](media/how-to-install/verify2.png)

### <a name="additional-requirements"></a>Дополнительные требования
- [Платформа Microsoft .NET 4.7.1](https://www.microsoft.com/download/details.aspx?id=56116) 

#### <a name="tls-requirements"></a>Требования к TLS

>[!NOTE]
>TLS — это протокол, обеспечивающий безопасность обмена данными.  Изменение параметров TLS влияет на весь лес.  Дополнительные сведения см. в [статье обновление для включения tls 1,1 и tls 1,2 в качестве защищенных протоколов по умолчанию в WinHTTP в Windows](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi) .

Перед установкой в Windows Server, где будет размещен агент подготовки облака Azure AD Connect, необходимо включить TLS 1,2.

Включение протокола TLS 1.2

1. Настройте следующие разделы реестра:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Перезапустите сервер.


## <a name="next-steps"></a>Дальнейшие действия 

- [Что такое подготовка?](what-is-provisioning.md)
- [Что такое Azure AD Connect подготовки облака?](what-is-cloud-provisioning.md)

