---
title: Устранение неполадок с локальной средой выполнения интеграции в Фабрике данных Azure
description: Узнайте, как устранять неполадки с локальной средой выполнения интеграции в Фабрике данных Azure.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/07/2019
ms.author: abnarain
ms.openlocfilehash: 94e214c55a0109beb85cd08ce87303e5bd0f8016
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835433"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Устранение неполадок с локальной средой выполнения интеграции

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

В этой статье рассматриваются стандартные методы устранения неполадок с локальной средой выполнения интеграции в Фабрике данных Azure.

## <a name="common-errors-and-resolutions"></a>Распространенные ошибки и способы их устранения

### <a name="error-message-self-hosted-integration-runtime-cant-connect-to-cloud-service"></a>Сообщение об ошибке: Локальная среда выполнения интеграции не может подключиться к облачной службе

![Проблема с подключением локальной среды выполнения интеграции](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>Причина 

Локальная среда выполнения интеграции не может подключиться к серверной части службы Фабрики данных. Обычно такая проблема возникает из-за настроек брандмауэра.

#### <a name="resolution"></a>Решение

1. Убедитесь, что среда выполнения интеграции запущена.
    
   ![Рабочее состояние локальной среды выполнения интеграции](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. Если служба запущена, перейдите к шагу 3.

1. Если для локальной среды выполнения интеграции не используется прокси-сервер (это вариант по умолчанию), выполните следующую команду PowerShell на том компьютере, где установлена локальная среда выполнения интеграции.

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > URL-адрес службы может отличаться в зависимости от расположения Фабрики данных. URL-адрес службы см. в разделе **Пользовательский интерфейс ADF** > **Подключения** > **Среды выполнения интеграции** > **Изменить локальные IR** > **Узлы** > **Просмотреть URL-адреса службы**.
            
    Ожидается примерно такой ответ:
            
    ![Ответ на команду PowerShell](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. Если полученный ответ будет отличаться, воспользуйтесь одним из описанных ниже методов в соответствии со своим сценарием.
            
    * Если вы получаете сообщение Remote name could not be resolved (Не удалось разрешить удаленное имя), это указывает на проблему в службе доменных имен (DNS). Для устранения этой проблемы свяжитесь с командой, ответственной за работу сети.
    * Если вы получаете сообщение ssl/tls cert is not trusted (Сертификат SSL/TLS не является доверенным), проверьте, какой сертификат для https://wu2.frontend.clouddatahub.net/ является доверенным на этом компьютере, а затем установите открытый сертификат с помощью диспетчера сертификатов. Это действие должно устранить проблему.
    * Откройте раздел **Windows** > **Просмотр событий (журналы)**  > **Журналы приложений и служб** > **Среда выполнения интеграции** и проверьте, нет ли здесь сведений об ошибках, вызванных DNS, правилами брандмауэра или настройками корпоративной сети. Если такие ошибки есть, принудительно закройте подключение. В каждой компании настройки сети будут отличаться, поэтому устранение неполадок следует поручить команде, ответственной за работу сети.

1. Если для локальной среды выполнения интеграции настроен прокси-сервер, убедитесь, что этот прокси-сервер имеет доступ к конечной точке службы. Пример команды для такой проверки см. [здесь](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies).    
                
    ```powershell
    $user = $env:username
    $webproxy = (get-itemproperty 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Internet
    Settings').ProxyServer
    $pwd = Read-Host "Password?" -assecurestring
    $proxy = new-object System.Net.WebProxy
    $proxy.Address = $webproxy
    $account = new-object System.Net.NetworkCredential($user,[Runtime.InteropServices.Marshal]::PtrToStringAuto([Runtime.InteropServices.Marshal]::SecureStringToBSTR($pwd)), "")
    $proxy.credentials = $account
    $url = "https://wu2.frontend.clouddatahub.net/"
    $wc = new-object system.net.WebClient
    $wc.proxy = $proxy
    $webpage = $wc.DownloadData($url)
    $string = [System.Text.Encoding]::ASCII.GetString($webpage)
    $string
    ```

Ожидается примерно такой ответ:
            
![Ответ на команду PowerShell 2](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> Рекомендации по использованию прокси-сервера:
> *    Проверьте, не нужно ли добавить прокси-сервер в список надежных получателей. Если это так, включите в список надежных получателей [эти домены](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations#firewall-requirements-for-on-premisesprivate-network).
> *    Убедитесь, что сертификат TLS/SSL с именем wu2.frontend.clouddatahub.net/ является доверенным на прокси-сервере.
> *    Если вы используете в прокси-сервере проверку подлинности Active Directory, укажите в поле "Служба среды выполнения интеграции" вместо учетной записи службы учетную запись пользователя с доступом к прокси-серверу.

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>Сообщение об ошибке: Self-hosted integration runtime node/ logical SHIR is in Inactive/ "Running (Limited)" state (Узел локальной среды выполнения интеграции или логическая локальная среда выполнения интеграции находится в неактивном или ограниченном состоянии)

#### <a name="cause"></a>Причина 

Узел локальной среды выполнения интеграции может находиться в состоянии **Неактивно**, как показано на следующем снимке экрана.

![Узел локальной среды выполнения интеграции в неактивном состоянии](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

Такое может происходить, если узлы не могут нормально взаимодействовать.

#### <a name="resolution"></a>Решение

1. Войдите на виртуальную машину соответствующего узла. В разделе **Журналы приложений и служб** > **Среда выполнения интеграции** откройте "Просмотр событий" и отфильтруйте журналы ошибок.

1. Проверьте, содержит ли журнал ошибок следующую ошибку. 
    
    ```System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
        
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
               
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
               
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
       
1. If you see this error, run the following on a command line: 

   **telnet 10.2.4.10 8060**.
1. If you receive the following error, contact your IT department for help with fixing this issue. After you can successfully telnet, contact Microsoft Support if you still have issues with the integrative runtime node status.
        
   ![Command-line error](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1.    Check whether the error log contains the following:

    ```Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)```
    
1. To resolve the issue, try one or both of the following methods:
    - Put all the nodes in the same domain.
    - Add the IP to host mapping in all the hosted VM's host files.


## Next steps

For more help with troubleshooting, try the following resources:

*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory feature requests](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&A question page](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Stack overflow forum for Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter information about Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Mapping data flows performance guide](concepts-data-flow-performance.md)
