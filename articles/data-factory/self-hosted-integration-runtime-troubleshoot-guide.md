---
title: Устранение проблем самохлаженное время выполнения интеграции на фабрике данных Azure
description: Узнайте, как устранить проблемы самостоятельной интеграции в фазе данных Azure.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/07/2019
ms.author: abnarain
ms.openlocfilehash: b8492e8934c782451fb77d5a0ff56b96c34c9a00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75439874"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Устранение проблем самохонсеченное время выполнения интеграции

В этой статье рассматриваются общие методы устранения неполадок для самостоятельного запуска интеграции в Azure Data Factory.

## <a name="common-errors-and-resolutions"></a>Распространенные ошибки ASR и способы их устранения

### <a name="error-message-self-hosted-integration-runtime-cant-connect-to-cloud-service"></a>Сообщение об ошибке: Самохотваченное время выполнения интеграции не может подключиться к облачному сервису

![Самостоятельная проблема ИК-соединения](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>Причина 

Самохлажен интеграции время выполнения не может подключиться к службе Data Factory (бэкэнд). Эта проблема обычно вызвана настройками сети в брандмауэре.

#### <a name="resolution"></a>Решение

1. Проверьте, работает ли служба времени выполнения интеграции.
    
   ![Статус автономного ИК-сервиса](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. Если служба работает, перейдите к шагу 3.

1. Если на автономном времени выполнения интеграции (который является параметром по умолчанию) нет прокси-сервера, запустите следующую команду PowerShell на машине, где установлено самоходное время выполнения интеграции:

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > URL-адрес службы может варьироваться в зависимости от местоположения фабрики данных. Вы можете найти URL-адрес службы в соответствии с **ADF UI** > **Подключения Интеграция** > **Runtimes** > **Отсеивать самостоятельно размещенные ИК-узлы** > **Nodes** > View Service**URL-услуг.**
            
    Ниже приводится ожидаемый ответ:
            
    ![Ответ команды PowerShell](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. Если вы не получили ожидаемого ответа, используйте один из следующих методов, соответствующих вашей ситуации:
            
    * Если вы получили сообщение "Удаленное имя не может быть решено", существует проблема системы доменных имен (DNS). Свяжитесь с сетевой командой, чтобы устранить эту проблему.
    * Если вы получили сообщение "ssl/tls cert is not trusted" , проверьте, доверяют ли сертификату https://wu2.frontend.clouddatahub.net/ на машине, а затем установите общедоступный сертификат с помощью certificate Manager. Это действие должно смягчить проблему.
    * Перейдите к просмотру **windows** > **Event (журналы)** > **Приложения и службы Интеграция журналов** > **Runtime** и проверить на наличие сбоя, вызванного DNS, правило мгновения брандмауэра, или настройки сети компании. (Если вы обнаружите такой сбой, принудительно закройте соединение.) Поскольку каждая компания имеет настраиваемые настройки сети, свяжитесь с вашей сетевой командой, чтобы устранить эти проблемы.

1. Если "прокси" был настроен на автономном времени выполнения интеграции, убедитесь, что ваш прокси-сервер может получить доступ к конечному пункту службы. Для примера команды [см. PowerShell, веб-запросы и прокси.](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies)    
                
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

Ниже приводится ожидаемый ответ:
            
![Ответ команды Powershell 2](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> Прокси соображения:
> * Проверьте, нужно ли внести прокси-сервер в список безопасных получателей. Если это так, убедитесь, что [эти домены](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations#firewall-requirements-for-on-premisesprivate-network) находятся в списке безопасных получателей.
> * Проверьте, доверяют ли сертификату TLS/SSL "wu2.frontend.clouddatahub.net/" на прокси-сервере.
> * Если вы используете проверку подлинности Active Directory на прокси-сервере, измените учетную запись службы на учетную запись пользователя, которая может получить доступ к прокси-серверу как "Служба интеграции Runtime".

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>Сообщение об ошибке: Самоходное узло запуска интеграции/ логическое SHIR находится в неактивном/ "Бегущий (ограниченный)" состояние

#### <a name="cause"></a>Причина 

Самоходное интегрированное узло времени выполнения может иметь **неактивный** статус, как показано на следующем скриншоте:

![Неактивный самохонсводное ИК-узла](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

Такое поведение происходит, когда узлы не могут общаться друг с другом.

#### <a name="resolution"></a>Решение

1. Войти в узла-хостинг VM. В соответствии с **системами интеграции** > приложений и служб**Runtime,** открыть viewer событий и отфильтровать все журналы ошибок.

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
