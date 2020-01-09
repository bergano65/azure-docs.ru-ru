---
title: Устранение неполадок с локальной средой выполнения интеграции в фабрике данных Azure
description: Узнайте, как устранять проблемы с локальной средой выполнения интеграции в фабрике данных Azure.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/07/2019
ms.author: abnarain
ms.openlocfilehash: b8492e8934c782451fb77d5a0ff56b96c34c9a00
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75439874"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Устранение неполадок в локальной среде выполнения интеграции

В этой статье рассматриваются распространенные методы устранения неполадок для локальной среды выполнения интеграции в фабрике данных Azure.

## <a name="common-errors-and-resolutions"></a>Распространенные ошибки и способы их устранения

### <a name="error-message-self-hosted-integration-runtime-cant-connect-to-cloud-service"></a>Сообщение об ошибке: локальной среде выполнения интеграции не удается подключиться к облачной службе

![Проблемы с подключением к собственному соединению](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>Причина 

Локальная среда выполнения интеграции не может подключиться к службе фабрики данных (серверной части). Эта проблема обычно вызвана параметрами сети в брандмауэре.

#### <a name="resolution"></a>Разрешение

1. Проверьте, запущена ли служба среды выполнения интеграции.
    
   ![Состояние выполнения локальной службы IR](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. Если служба запущена, перейдите к шагу 3.

1. Если прокси-сервер не настроен в локальной среде выполнения интеграции (это значение по умолчанию), выполните следующую команду PowerShell на компьютере, где установлена локальная среда выполнения интеграции:

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > URL-адрес службы может отличаться в зависимости от расположения фабрики данных. URL-адрес службы можно найти в **пользовательском интерфейсе ADF** > **подключения** > **среды выполнения интеграции** > изменить локальные **узлы** **IR** >  > **просмотра URL-адресов служб**.
            
    Ниже приведен ожидаемый ответ.
            
    ![Ответ команды PowerShell](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. Если ожидаемый ответ не получен, используйте один из следующих методов в зависимости от ситуации.
            
    * Если вы получаете сообщение "удаленное имя не удалось разрешить", существует проблема с DNS-именем. Чтобы устранить эту проблему, обратитесь к команде сети.
    * Если появляется сообщение "сертификат SSL/TLS не является доверенным", проверьте, является ли сертификат для https://wu2.frontend.clouddatahub.net/ доверенным на компьютере, а затем установите открытый сертификат с помощью диспетчера сертификатов. Это действие должно устранить эту ошибку.
    * Перейдите в **окно просмотра событий Windows > (журналы)**  > **приложения и службы журналы** > **Integration Runtime** и проверьте на наличие ошибок, вызванных DNS, правилом брандмауэра или корпоративными параметрами сети. (Если вы обнаружите такой сбой, принудительно Закройте подключение.) Так как в каждой компании настроены параметры сети, обратитесь к группе сети, чтобы устранить эти проблемы.

1. Если в локальной среде выполнения интеграции настроен параметр "Proxy", убедитесь, что прокси-сервер имеет доступ к конечной точке службы. Пример команды см. в разделе [PowerShell, веб-запросы и прокси](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies).    
                
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

Ниже приведен ожидаемый ответ.
            
![Ответ команды PowerShell 2](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> Вопросы прокси-сервера:
> * Убедитесь, что прокси-сервер должен быть размещен в списке надежных получателей. Если это так, убедитесь, что [эти домены](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations#firewall-requirements-for-on-premisesprivate-network) находятся в списке Надежные получатели.
> * Проверьте, является ли сертификат TLS/SSL "wu2.frontend.clouddatahub.net/" доверенным на прокси-сервере.
> * Если вы используете Active Directoryную проверку подлинности на прокси-сервере, измените учетную запись службы на учетную запись пользователя, которая может обращаться к прокси-серверу как "служба Integration Runtime".

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>Сообщение об ошибке: узел локальной среды выполнения интеграции/логический Шир находится в неактивном состоянии или "выполняется (ограничено)"

#### <a name="cause"></a>Причина 

Изолированный узел интегрированной среды выполнения может иметь **неактивное** состояние, как показано на следующем снимке экрана:

![Неактивный узел IR с автономным размещением](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

Такое поведение возникает, когда узлы не могут взаимодействовать друг с другом.

#### <a name="resolution"></a>Разрешение

1. Войдите на виртуальную машину, размещенную на узле. В разделе **журналы приложений и служб** > **Integration Runtime**, откройте Просмотр событий и отфильтруйте все журналы ошибок.

1. Проверьте, содержит ли журнал ошибок следующую ошибку: 
    
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
        
1.  Check whether the error log contains the following:

    ```Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)```
    
1. To resolve the issue, try one or both of the following methods:
    - Put all the nodes in the same domain.
    - Add the IP to host mapping in all the hosted VM's host files.


## Next steps

For more help with troubleshooting, try the following resources:

*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory feature requests](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN forum](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack overflow forum for Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter information about Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Mapping data flows performance guide](concepts-data-flow-performance.md)
