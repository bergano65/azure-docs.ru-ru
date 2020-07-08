---
title: 'Azure AD Connect: оборудование и необходимые компоненты | Документация Майкрософт'
description: В этом разделе описаны необходимые условия и требования к оборудованию для Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 91b88fda-bca6-49a8-898f-8d906a661f07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/27/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2bcf7b5b8791b813a28133d8a662d1736aacf35a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85358724"
---
# <a name="prerequisites-for-azure-ad-connect"></a>Необходимые условия для Azure AD Connect
В этой статье описаны необходимые условия и требования к оборудованию для Azure AD Connect.

## <a name="before-you-install-azure-ad-connect"></a>Перед установкой Azure AD Connect
Прежде чем установить Azure AD Connect и обновить DirSync, вам потребуется ряд элементов.

### <a name="azure-ad"></a>Azure AD
* Клиент Azure AD. Вы получите его с [бесплатной пробной версией Azure](https://azure.microsoft.com/pricing/free-trial/). Вы можете использовать один из следующих порталов для управления Azure AD Connect:
  * [Портал Azure](https://portal.azure.com).
  * [Портал Office](https://portal.office.com).  
* [Добавьте и подтвердите домен](../active-directory-domains-add-azure-portal.md) , который вы планируете использовать в Azure AD. Например, если вы планируете использовать для своих пользователей домен contoso.com, убедитесь, что он был подтвержден и вы используете не просто домен по умолчанию contoso.onmicrosoft.com.
* Клиент Azure AD по умолчанию может вмещать 50 тыс. объектов. После подтверждения домена этот предел увеличивается до 300 тыс. объектов. Если вам нужно еще больше объектов в Azure AD, необходимо отправить обращение в службу технической поддержки, чтобы дополнительно увеличить данный предел. Если вам необходимо более 500 тыс. объектов, то потребуется лицензия, например на Office 365, Azure AD Basic, Azure AD Premium или Enterprise Mobility + Security.

### <a name="prepare-your-on-premises-data"></a>Подготовка локальных данных
* Прежде чем выполнять синхронизацию с Azure AD и Office 365, воспользуйтесь инструментом [IdFix](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac) для выявления в каталоге ошибок, таких как повторяющиеся записи или проблемы с форматированием.
* Просмотрите [необязательные функции синхронизации, которые можно включить в Azure AD](how-to-connect-syncservice-features.md) , и решите, какие их них необходимо использовать.

### <a name="on-premises-active-directory"></a>Локальная служба Active Directory
* Версия схемы и режим работы леса AD должны предполагать использование ОС Windows Server, начиная с версии 2003. Контроллеры домена могут работать под управлением любой версии, если выполняются требования к схеме и уровню леса.
* Если вы планируете использовать **компонент обратной записи паролей**, то контроллеры домена должны работать под управлением Windows Server 2008 R2 или более поздней версии.
* Контроллер домена, используемый Azure AD, должен быть доступен для записи. Использование RODC (контроллера домена только для чтения) **не поддерживается** , и Azure AD Connect не подчиняются перенаправлениям записи.
* **Не поддерживаются** локальные леса и домены, использующие NetBIOS-имена с точками.
* Рекомендуется [включить корзину Active Directory](how-to-connect-sync-recycle-bin.md).

### <a name="azure-ad-connect-server"></a>Сервер Azure AD Connect
>[!IMPORTANT]
>Сервер Azure AD Connect содержит важные данные идентификации и должен рассматриваться как компонент уровня 0, как описано в [модели административного уровня Active Directory](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material) .

* Службу Azure AD Connect нельзя установить на версии Small Business Server или Windows Server Essentials, которые предшествуют версиям 2019 года (поддерживается Windows Server Essentials 2019). Сервер должен использовать Windows Server Standard или более поздней версии.
* Установка Azure AD Connect на контроллере домена не рекомендуется из-за методов обеспечения безопасности и более ограниченных параметров, которые могут препятствовать правильной установке Azure AD Connect.
* На сервере Azure AD Connect должен быть установлен полный графический интерфейс пользователя. Установка на ядро сервера **не поддерживается**.
>[!IMPORTANT]
>Установка Azure AD Connect на Small Business Server, Server Essentials или Server Core не поддерживается.

* Azure AD Connect должны быть установлены на Windows Server 2012 или более поздней версии. Этот сервер должен быть присоединен к домену и может быть контроллером домена или рядовым сервером.
* Azure AD Connect сервер не должен включать групповая политика записи PowerShell, если для управления конфигурацией ADFS используется мастер Azure AD Connect. Вы можете включить транскрипцию PowerShell, если используете мастер Azure AD Connect для управления конфигурацией синхронизации.
* При развертывании служб федерации Active Directory (AD FS) серверы, на которых будут установлены службы AD FS или прокси-служба веб-приложения, должны работать под управлением Windows Server 2012 R2 или более поздней версии. [удаленное управление Windows](#windows-remote-management) .
* Если развертывается службы федерации Active Directory (AD FS), необходимы [сертификаты TLS/SSL](#tlsssl-certificate-requirements).
* При развертывании служб федерации Active Directory необходимо настроить [разрешение имен](#name-resolution-for-federation-servers).
* Если для глобальных администраторов включен MFA, URL-адрес **https://secure.aadcdn.microsoftonline-p.com** должен находиться в списке надежных сайтов. Если он не добавлен, вам будет предложено добавить этот сайт в список надежных сайтов перед появлением запроса MFA. Добавить его в список надежных сайтов можно в Internet Explorer.
* Корпорация Майкрософт рекомендует повысить безопасность сервера Azure AD Connect, чтобы сократить направления атак на систему безопасности этого критически важного компонента ИТ-среды.  Следуя приведенным ниже рекомендациям, вы уменьшите угрозы безопасности для своей организации.

* Разверните Azure AD Connect на присоединенном к домену сервере и ограничьте административный доступ к администраторам домена или другим жестко управляемым группам безопасности.

Дополнительные сведения см. на следующих ресурсах: 

* [Защита групп администраторов](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-g--securing-administrators-groups-in-active-directory)

* [Защита встроенных учетных записей администратора](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-d--securing-built-in-administrator-accounts-in-active-directory)

* [Повышение безопасности и поддержка за счет сокращения направлений атак](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access#2-reduce-attack-surfaces )

* [Сокращение направлений атак на Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)

### <a name="sql-server-used-by-azure-ad-connect"></a>SQL Server, используемый Azure AD Connect
* Azure AD Connect требуется база данных SQL Server для хранения учетных данных. По умолчанию устанавливается SQL Server 2012 Express LocalDB (облегченная версия SQL Server Express). Размер экземпляра SQL Server Express может достигать 10 ГБ, позволяя управлять примерно 100 000 объектов. Если вам нужно управлять более значительным числом объектов каталога, в мастере установки укажите другую установку SQL Server. Тип установки SQL Server может повлиять на [производительность Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-performance-factors#sql-database-factors).
* Если используется другая установка SQL Server, то применяются следующие требования.
  * Azure AD Connect поддерживает все версии Microsoft SQL Server от 2012 (с последним пакетом обновления) до SQL Server 2019. База данных SQL Microsoft Azure в качестве базы данных **не поддерживается**.
  * Необходимо использовать параметры сортировки SQL без учета регистра. Их можно определить по суффиксу \_CI_ в имени. Параметры сортировки с учетом регистра, имена которых содержат суффикс \_CS_, **не поддерживаются**.
  * На один экземпляр SQL может приходиться только один модуль синхронизации. Совместное использование экземпляра SQL модулями FIM/MIM Sync, DirSync и Azure AD Sync **не поддерживается**.

### <a name="accounts"></a>Учетные записи
* Учетная запись глобального администратора Azure AD для клиента Azure AD, с которым необходима интеграция. Это должна быть **учебная или рабочая учетная запись**. **Учетную запись Майкрософт** использовать нельзя.
* Если вы используете [быстрые параметры](reference-connect-accounts-permissions.md#express-settings-installation) или обновляете DirSync, у вас должна быть учетная запись администратора предприятия для локального Active Directory.
* Если используется путь установки пользовательских параметров, то доступны дополнительные параметры. Дополнительные сведения см. в разделе [Параметры выборочной установки](reference-connect-accounts-permissions.md#custom-installation-settings).

### <a name="connectivity"></a>Соединение
* Для серверов Azure AD Connect требуется разрешение DNS как для интрасети, так и для Интернета. DNS-сервер должен иметь возможность разрешения имен как для локальной службы Active Directory, так и для конечных точек Azure AD.
* Если в вашей интрасети есть брандмауэры и вам необходимо открыть порты между серверами Azure AD Connect и контроллерами доменов, то дополнительные сведения см. в статье [Порты и протоколы, необходимые для гибридной идентификации](reference-connect-ports.md).
* Если прокси-сервер или брандмауэр ограничивает доступные URL-адреса, то на нем необходимо открыть URL-адреса, указанные в разделе [URL-адреса и диапазоны IP-адресов Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).
  * Если вы используете платформу Microsoft Cloud в Германии или облако Microsoft Azure для государственных организаций, то сведения об URL-адресах см. в статье [Azure AD Connect: специальные рекомендации для экземпляров](reference-connect-instances.md).
* Azure AD Connect (версия 1.1.614.0 и более поздние версии) по умолчанию использует TLS 1.2 для шифрования связи между модулем синхронизации и Azure AD. Если TLS 1.2 не поддерживается для базовой операционной системы, Azure AD Connect последовательно пытается перейти на более старые протоколы (TLS 1.1 и TLS 1.0).
* До версии 1.1.614.0 Azure AD Connect по умолчанию использует TLS 1.0 для шифрования связи между модулем синхронизации и Azure AD. Чтобы перейти на использование TLS 1.2, нужно выполнить инструкции из раздела [Включение протокола TLS 1.2 для Azure AD Connect](#enable-tls-12-for-azure-ad-connect).
* Если для подключения к Интернету используется прокси-сервер, то в файл **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** необходимо добавить приведенные ниже параметры, чтобы мастер установки и функция синхронизации Azure AD Connect могли подключаться к Интернету и Azure AD. Этот текст необходимо добавить в конец указанного файла. В этом коде &lt;PROXYADDRESS&gt; означает фактический IP-адрес или имя узла прокси-сервера.

```
    <system.net>
        <defaultProxy>
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

* Если на прокси-сервере требуется аутентификация, то [учетная запись службы](reference-connect-accounts-permissions.md#adsync-service-account) должна находиться в домене. Кроме того, во время установки вам необходимо указать путь к [настраиваемой учетной записи службы](how-to-connect-install-custom.md#install-required-components). Кроме того, для machine.config потребуется другое изменение. После этого изменения в machine.config мастер установки и модуль синхронизации отвечают на запросы проверки подлинности от прокси-сервера. На всех страницах мастера установки, за исключением страницы **Настройка**, используются учетные данные пользователя, выполнившего вход. На странице **Настройка** перед завершением работы мастера установки контекст переключается на созданную вами [учетную запись службы](reference-connect-accounts-permissions.md#adsync-service-account). Раздел machine.config должен выглядеть следующим образом.

```
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

* Когда в процессе синхронизации каталогов Azure AD Connect отправляет веб-запрос в Azure AD, то для ответа Azure AD может потребоваться до 5 минут. Для прокси-серверов обычно настраивается время ожидания простоя подключения. Убедитесь, что в настройках для этого параметра задано не менее 6 минут.

Дополнительную информацию об [элементе defaultProxy](https://msdn.microsoft.com/library/kd3cf2ex.aspx) вы можете найти на сайте MSDN.  
В случае проблем с подключением изучите статью [Устранение неполадок подключения в Azure AD Connect](tshoot-connect-connectivity.md).

### <a name="other"></a>Другое
* Необязательно: тестовая учетная запись пользователя для проверки синхронизации.

## <a name="component-prerequisites"></a>Предварительные требования к компонентам
### <a name="powershell-and-net-framework"></a>PowerShell и .NET Framework
Работа Azure AD Connect основана на Microsoft PowerShell и .NET Framework 4.5.1. На сервере должна быть установлена эта или более поздняя версия. В зависимости от установленной версии Windows Server выполните следующие действия.

* Windows Server 2012 R2;
  * Microsoft PowerShell устанавливается по умолчанию. Никаких действий не требуется.
  * Платформа .NET Framework 4.5.1 и более поздних версий распространяется через Центр обновления Windows. Убедитесь, что установлены последние обновления для Windows Server в панели управления.
* Windows Server 2012
  * Последняя версия Microsoft PowerShell доступна в составе платформы **Windows Management Framework 4.0**, которую можно скачать из [центра загрузки Майкрософт](https://www.microsoft.com/downloads).
  * .NET Framework 4.5.1 и более поздние версии платформы доступны в [центре загрузки Майкрософт](https://www.microsoft.com/downloads).


### <a name="enable-tls-12-for-azure-ad-connect"></a>Включение протокола TLS 1.2 для Azure AD Connect
До версии 1.1.614.0 Azure AD Connect по умолчанию использует TLS 1.0 для шифрования связи между сервером модуля синхронизации и Azure AD. Это можно изменить, настроив приложения .NET на использование TLS 1,2 по умолчанию на сервере. Дополнительные сведения о протоколе TLS 1.2 см. в статье [Советы по безопасности (Microsoft) (2960358)](https://technet.microsoft.com/security/advisory/2960358).

1.  Убедитесь, что для вашей операционной системы установлено исправление .NET 4.5.1, см. [рекомендации по безопасности Microsoft 2960358](https://technet.microsoft.com/security/advisory/2960358). Это исправление или его более поздняя версия может быть уже установлена на вашем сервере.
    ```
2. For all operating systems, set this registry key and restart the server.
    ```
    HKEY_LOCAL_MACHINE \Софтваре\микрософт \. NETFramework\v4.0.30319 "SchUseStrongCrypto" = DWORD: 00000001
    ```
4. If you also want to enable TLS 1.2 between the sync engine server and a remote SQL Server, then make sure you have the required versions installed for [TLS 1.2 support for Microsoft SQL Server](https://support.microsoft.com/kb/3135244).

## Prerequisites for federation installation and configuration
### Windows Remote Management
When using Azure AD Connect to deploy Active Directory Federation Services or the Web Application Proxy, check these requirements:

* If the target server is domain joined, then ensure that Windows Remote Managed is enabled
  * In an elevated PowerShell command window, use command `Enable-PSRemoting –force`
* If the target server is a non-domain joined WAP machine, then there are a couple of additional requirements
  * On the target machine (WAP machine):
    * Ensure the winrm (Windows Remote Management / WS-Management) service is running via the Services snap-in
    * In an elevated PowerShell command window, use command `Enable-PSRemoting –force`
  * On the machine on which the wizard is running (if the target machine is non-domain joined or untrusted domain):
    * In an elevated PowerShell command window, use the command `Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`
    * In Server Manager:
      * add DMZ WAP host to machine pool (server manager -> Manage -> Add Servers...use DNS tab)
      * Server Manager All Servers tab: right click WAP server and choose Manage As..., enter local (not domain) creds for the WAP machine
      * To validate remote PowerShell connectivity, in the Server Manager All Servers tab: right click WAP server and choose Windows PowerShell. A remote PowerShell session should open to ensure remote PowerShell sessions can be established.

### TLS/SSL Certificate Requirements
* It's strongly recommended to use the same TLS/SSL certificate across all nodes of your AD FS farm and all Web Application proxy servers.
* The certificate must be an X509 certificate.
* You can use a self-signed certificate on federation servers in a test lab environment. However, for a production environment, we recommend that you obtain the certificate from a public CA.
  * If using a certificate that is not publicly trusted, ensure that the certificate installed on each Web Application Proxy server is trusted on both the local server and on all federation servers
* The identity of the certificate must match the federation service name (for example, sts.contoso.com).
  * The identity is either a subject alternative name (SAN) extension of type dNSName or, if there are no SAN entries, the subject name specified as a common name.  
  * Multiple SAN entries can be present in the certificate, provided one of them matches the federation service name.
  * If you are planning to use Workplace Join, an additional SAN is required with the value **enterpriseregistration.** followed by the User Principal Name (UPN) suffix of your organization, for example, **enterpriseregistration.contoso.com**.
* Certificates based on CryptoAPI next generation (CNG) keys and key storage providers are not supported. This means you must use a certificate based on a CSP (cryptographic service provider) and not a KSP (key storage provider).
* Wild-card certificates are supported.

### Name resolution for federation servers
* Set up DNS records for the AD FS federation service name (for example sts.contoso.com) for both the intranet (your internal DNS server) and the extranet (public DNS through your domain registrar). For the intranet DNS record, ensure that you use A records and not CNAME records. This is required for windows authentication to work correctly from your domain joined machine.
* If you are deploying more than one AD FS server or Web Application Proxy server, then ensure that you have configured your load balancer and that the DNS records for the AD FS federation service name (for example sts.contoso.com) point to the load balancer.
* For windows integrated authentication to work for browser applications using Internet Explorer in your intranet, ensure that the AD FS federation service name (for example sts.contoso.com) is added to the intranet zone in IE. This can be controlled via group policy and deployed to all your domain joined computers.

## Azure AD Connect supporting components
The following is a list of components that Azure AD Connect installs on the server where Azure AD Connect is installed. This list is for a basic Express installation. If you choose to use a different SQL Server on the Install synchronization services page, then SQL Express LocalDB is not installed locally.

* Azure AD Connect Health
* Microsoft SQL Server 2012 Command Line Utilities
* Microsoft SQL Server 2012 Express LocalDB
* Microsoft SQL Server 2012 Native Client
* Microsoft Visual C++ 2013 Redistribution Package

## Hardware requirements for Azure AD Connect
The table below shows the minimum requirements for the Azure AD Connect sync computer.

| Number of objects in Active Directory | CPU | Memory | Hard drive size |
| --- | --- | --- | --- |
| Fewer than 10,000 |1.6 GHz |4 GB |70 GB |
| 10,000–50,000 |1.6 GHz |4 GB |70 GB |
| 50,000–100,000 |1.6 GHz |16 GB |100 GB |
| For 100,000 or more objects the full version of SQL Server is required | | | |
| 100,000–300,000 |1.6 GHz |32 GB |300 GB |
| 300,000–600,000 |1.6 GHz |32 GB |450 GB |
| More than 600,000 |1.6 GHz |32 GB |500 GB |

The minimum requirements for computers running AD FS or Web Application Proxy Servers is the following:

* CPU: Dual core 1.6 GHz or higher
* MEMORY: 2 GB or higher
* Azure VM: A2 configuration or higher

## Next steps
Learn more about [Integrating your on-premises identities with Azure Active Directory](whatis-hybrid-identity.md).
