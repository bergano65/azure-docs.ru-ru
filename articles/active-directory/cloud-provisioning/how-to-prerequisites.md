---
title: Предпосылки для подготовки облака Azure AD Connect в Azure AD
description: В этой статье описаны предпосылки и требования к оборудованию, необходимые для подготовки облака.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45648170f69d513b15e79cdd76f56e66bbc88bfa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332084"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-provisioning"></a>Предпосылки для подготовки облака Azure AD Connect
В этой статье содержатся рекомендации по выбору и использованию облачного каталога Azure (Azure AD) Connect в качестве решения для идентификации.



## <a name="cloud-provisioning-agent-requirements"></a>Требования к облачному агенту
Для использования облачного облака Azure AD Connect требуется следующее:
    
- Глобальная учетная запись администратора для вашего клиента Azure AD, который не является гостем.
- На-центре сервера для агента подготовки с Windows 2012 R2 или позже.
- Конфигурации брандмауэра.

>[!NOTE]
>В настоящее время агент по подготовке может быть установлен только на серверах на английском языке. Установка пакета на английском языке на неанглийском сервере не является действительным решением и приведет к тому, что агент не установится. 

Остальная часть документа содержит пошаговые инструкции для этих предварительных условий.

### <a name="in-the-azure-active-directory-admin-center"></a>В центре администрирования Azure Active Directory

1. Создайте облачную учетную запись глобального администратора в клиенте Azure AD. Таким образом, вы можете управлять конфигурацией арендатора, если ваши службы не удаются или становятся недоступными. Узнайте о том, как [добавить учетную запись глобального администратора только](../active-directory-users-create-azure-portal.md)для облака. Завершение этого шага имеет решающее значение для обеспечения того, чтобы вы не были заблокированы из вашего арендатора.
1. Добавьте одно [имя личного домена](../active-directory-domains-add-azure-portal.md) (или несколько) в клиент Azure AD. Пользователи могут выполнить вход с помощью одного из этих доменных имен.

### <a name="in-your-directory-in-active-directory"></a>В каталоге в Active Directory

Выполнить [инструмент IdFix](https://docs.microsoft.com/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix) для подготовки атрибутов каталога для синхронизации.

### <a name="in-your-on-premises-environment"></a>В локальной среде

1. Определите сервер, работающий на домене, под управлением Windows Server 2012 R2 или более с минимумом 4 ГБ оперативной памяти и время выполнения .NET 4.7.1.

1. Если между серверами и Azure AD есть брандмауэр, назначьте следующие элементы:
   - Убедитесь, что агенты могут передавать *исходящие* запросы в Azure AD через приведенные ниже порты.

        | Номер порта | Как он используется |
        | --- | --- |
        | **80** | Загружает списки аннулирования сертификатов (CRLs) при проверке сертификата TLS/SSL.  |
        | **443** | Обрабатывает всю исходящие связи с службой. |
        | **8080** (необязательно) | Агенты передают данные о своем состоянии каждые 10 минут через порт 8080, если порт 443 недоступен. Этот статус отображается на портале Azure AD. |
     
   - Если брандмауэр применяет правила в соответствии с отправляющими трафик пользователями, откройте эти порты для трафика, поступающего от служб Windows, которые работают как сетевая служба.
   - Если брандмауэр или прокси позволяет указать безопасные суффиксы, добавьте соединения в \*.msappproxy.net и \*.servicebus.windows.net. Если нет, разрешите доступ к [диапазонам IP-адресов центра обработки данных Azure](https://www.microsoft.com/download/details.aspx?id=41653). Список диапазонов IP-адресов обновляется еженедельно.
   - Агентам требуется доступ к адресам login.windows.net и login.microsoftonline.com для первоначальной регистрации. Откройте эти URL-адреса в брандмауэре.
   - Для проверки сертификата разблокируйте следующие URL-адреса:\.mscrl.microsoft.com:80, crl.microsoft.com:80, ocsp.msocsp.com:80 и wwwmicrosoft.com:80. Эти URL-адреса используются для проверки сертификатов с другими продуктами Майкрософт, поэтому, возможно, эти URL-адреса уже будут разблокированы.

### <a name="verify-the-port"></a>Проверить порт
Чтобы убедиться, что Azure слушает на порту 443 и что ваш агент может общаться с ним, используйте следующий URL:

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Этот тест проверяет, что агенты могут общаться с Azure через порт 443. Откройте браузер и перейдите на предыдущий URL с сервера, на котором установлен агент.

![Проверка доступности порта](media/how-to-install/verify2.png)

### <a name="additional-requirements"></a>Дополнительные требования
- [Microsoft .NET Framework 4.7.1](https://www.microsoft.com/download/details.aspx?id=56116) 

#### <a name="tls-requirements"></a>Требования к TLS

>[!NOTE]
>Transport Layer Security (TLS) — это протокол, обеспечивающий безопасную связь. Изменение настроек TLS влияет на весь лес. Для получения дополнительной информации в Windows в Подоконне можно [ознакомиться с обновлением, включавшим TLS 1.1 и TLS 1.2 в качестве защищенных протоколов по умолчанию.](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi)

Сервер Windows, на котором размещается облачный агент Azure AD Connect, должен иметь tLS 1.2, прежде чем установить его.

Чтобы включить TLS 1.2, выполните следующие действия.

1. Настройте следующие разделы реестра:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Перезапустите сервер.


## <a name="next-steps"></a>Дальнейшие действия 

- [Что собой представляет подготовка?](what-is-provisioning.md)
- [Что такое подготовка облака Azure AD Connect?](what-is-cloud-provisioning.md)

