---
title: Устранение неполадок с локальной средой выполнения интеграции в Фабрике данных Azure
description: Узнайте, как устранять неполадки с локальной средой выполнения интеграции в Фабрике данных Azure.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 06/24/2020
ms.author: abnarain
ms.openlocfilehash: e77d621d5699c434e691de0a523e58e49166d8d6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85315127"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Устранение неполадок с локальной средой выполнения интеграции

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

В этой статье рассматриваются стандартные методы устранения неполадок с локальной средой выполнения интеграции в Фабрике данных Azure.

## <a name="common-errors-and-resolutions"></a>Распространенные ошибки и способы их устранения

### <a name="error-message"></a>Сообщение об ошибке: 

`Self-hosted integration runtime can't connect to cloud service`

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

### <a name="error-message"></a>Сообщение об ошибке: 
`Self-hosted integration runtime node/ logical SHIR is in Inactive/ "Running (Limited)" state`

#### <a name="cause"></a>Причина 

Узел локальной среды выполнения интеграции может находиться в состоянии **Неактивно**, как показано на следующем снимке экрана.

![Узел локальной среды выполнения интеграции в неактивном состоянии](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

Такое может происходить, если узлы не могут нормально взаимодействовать.

#### <a name="resolution"></a>Решение

1. Войдите на виртуальную машину соответствующего узла. В разделе **Журналы приложений и служб** > **Среда выполнения интеграции** откройте "Просмотр событий" и отфильтруйте журналы ошибок.

1. Проверьте, содержит ли журнал ошибок следующую ошибку. 
    
    ```
    System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
    ```
       
1. Если вы видите эту ошибку, выполните следующую команду в командной строке: 

   ```
   telnet 10.2.4.10 8060
   ```
   
1. Если вы получаете следующую ошибку, обратитесь к ИТ-отделу за помощью в устранении этой проблемы. После успешной работы с Telnet обратитесь в служба поддержки Майкрософт, если у вас по-прежнему возникают проблемы с состоянием узла интегрированной среды выполнения.
        
   ![Ошибка командной строки](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1. Проверьте, содержит ли журнал ошибок следующее:

    ```
    Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)
    ```
    
1. Чтобы устранить эту проблему, попробуйте один из следующих методов или оба.
    - Разместите все узлы в одном домене.
    - Добавьте сопоставление IP и узлов во все файлы узлов размещенной виртуальной машины.


## <a name="troubleshoot-connectivity-issue"></a>Устранение проблем с подключением

### <a name="troubleshoot-connectivity-issue-between-self-hosted-ir-and-data-factory-or-self-hosted-ir-and-data-sourcesink"></a>Устранение проблем с подключением между локальными IR-и фабрикой данных или локально размещенным IR-кодом и приемником данных

Чтобы устранить неполадки с сетевым подключением, необходимо знать, как [получить трассировку сети](#how-to-collect-netmon-trace), понять, как ее использовать, и [проанализировать трассировку Netmon](#how-to-analyze-netmon-trace) перед применением средств NetMon в реальных случаях из автономной среды IR.

Иногда при устранении проблем с подключением, как показано ниже, между локальной сетью IR и фабрикой данных. 

![Сбой HTTP-запроса](media/self-hosted-integration-runtime-troubleshoot-guide/http-request-error.png)

Или между самостоятельно размещенным IR и источником данных или приемником, мы будем столкнуться со следующими ошибками:

**Сообщение об ошибке:**
`Copy failed with error:Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server: ‘IP address’`

**Сообщение об ошибке:**
`One or more errors occurred. An error occurred while sending the request. The underlying connection was closed: An unexpected error occurred on a receive. Unable to read data from the transport connection: An existing connection was forcibly closed by the remote host. An existing connection was forcibly closed by the remote host Activity ID.`

**Решение:** При возникновении описанных выше проблем ознакомьтесь со следующими инструкциями, чтобы устранить неполадки.

Возьмите трассировку Netmon и проанализируйте ее.
- В первую очередь можно настроить фильтр для просмотра сброса с сервера на стороне клиента. В приведенном ниже примере можно увидеть, что серверная часть — это сервер фабрики данных.

    ![Сервер фабрики данных](media/self-hosted-integration-runtime-troubleshoot-guide/data-factory-server.png)

- При получении пакета сброса можно найти диалог, выполнив следующие TCP.

    ![Найти беседу](media/self-hosted-integration-runtime-troubleshoot-guide/find-conversation.png)

- После этого можно получить сведения о преобразовании между клиентом и сервером фабрики данных, удалив фильтр.

    ![Получить беседу](media/self-hosted-integration-runtime-troubleshoot-guide/get-conversation.png)
- На основе собираемой трассировки Netmon мы можем определить, что сумма TTL (TimeToLive) равна 64. В соответствии со **ЗНАЧЕНИЯМИ TTL и предельным числом прыжков по умолчанию** , указанными в [этой статье](https://packetpushers.net/ip-time-to-live-and-hop-limit-basics/) (как показано ниже), мы видим, что это система Linux, которая сбрасывает пакет и вызывает отключение.

    Значения TTL и предельного числа прыжков по умолчанию различаются в разных операционных системах. по умолчанию используются следующие параметры.
    - Ядро Linux 2,4 (приблизительно 2001): 255 для TCP, UDP и ICMP
    - Ядро Linux 4,10 (2015): 64 для TCP, UDP и ICMP
    - Windows XP (2001): 128 для TCP, UDP и ICMP
    - Windows 10 (2015): 128 для TCP, UDP и ICMP
    - Windows Server 2008:128 для TCP, UDP и ICMP
    - Windows Server 2019 (2018): 128 для TCP, UDP и ICMP
    - macOS (2001): 64 для TCP, UDP и ICMP

    ![TTL 61](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-61.png)
    
    Однако в приведенном выше примере он отображается как 61 вместо 64, так как когда сетевой пакет доходит до назначения, он должен пройти через различные прыжки, такие как маршрутизаторы и сетевые устройства. Число маршрутизаторов и сетевых устройств будет вычтено в окончательном сроке жизни.
    В этом случае можно увидеть, что сброс может быть отправлен из системы Linux с TTL 64.

- Нам нужно проверить четвертый прыжок с локальной среды IR, чтобы подтвердить, откуда может быть сброшено устройство.
 
    *Сетевой пакет из Linux, система A с TTL 64-> B TTL 64 минус 1 = 63-> C TTL 63 минус 1 = 62-> TTL 62 минус 1 = 61 локальная среда IR*

- В идеальной ситуации TTL будет 128, что означает, что система Windows выполняет фабрику данных. Как показано в приведенном ниже примере, *128 – 107 = 21 прыжки*, то есть 21 прыжки для пакета были отправлены из фабрики данных в локально размещаемый IR во время подтверждения TCP 3.
 
    ![TTL 107](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-107.png)

    Поэтому необходимо привлечь сетевую группу, чтобы проверить, что четвертый прыжок относится к собственному IR. Если это брандмауэр в качестве системы Linux, проверьте все журналы, почему устройство сбрасывает пакет после подтверждения TCP 3. Тем не менее, если вы не знаете, где выполнить исследование, попытайтесь получить трассировку Netmon из локальной среды IR и межсетевого экрана вместе во время проблемного времени, чтобы выяснить, какое устройство может сбросить этот пакет и вызвать отключение. В этом случае необходимо также привлечь вас к группе по сети.

### <a name="how-to-collect-netmon-trace"></a>Как получить трассировку Netmon

1.  Загрузите средства Netmon с [этого веб-сайта](https://www.microsoft.com/en-sg/download/details.aspx?id=4865)и установите его на компьютере сервера (на котором возникла проблема) и на клиенте (например, в автономном среде).

2.  Создайте папку, например, в следующем пути: *д:\нетмон*. Убедитесь, что в нем достаточно места для сохранения журнала.

3.  Запишите сведения об IP-адресах и портах. 
    1. Запустите командную строку.
    2. Выберите Запуск от имени администратора и выполните следующую команду:
       
        ```
        Ipconfig /all >D:\netmon\IP.txt
        netstat -abno > D:\netmon\ServerNetstat.txt
        ```

4.  Запишите трассировку Netmon (сетевой пакет).
    1. Запустите командную строку.
    2. Выберите Запуск от имени администратора и выполните следующую команду:
        
        ```
        cd C:\Program Files\Microsoft Network Monitor 3
        ```
    3. Для записи сетевой страницы можно использовать три различные команды:
        - Параметр A: циклический перебор file команда (при этом будет записан только один файл и будут перезаписаны старые журналы).

            ```
            nmcap /network * /capture /file D:\netmon\ServerConnection.cap:200M
            ```         
        - Вариант б. команда в цепочке файлов (при достижении которого будет создан новый файл, если достигнуто 200 МБ).
        
            ```
            nmcap /network * /capture /file D:\netmon\ServerConnection.chn:200M
            ```          
        - Вариант C: команда запланированного файла.

            ```
            nmcap /network * /capture /StartWhen /Time 10:30:00 AM 10/28/2011 /StopWhen /Time 11:30:00 AM 10/28/2011 /file D:\netmon\ServerConnection.chn:200M
            ```  

5.  Нажмите клавиши **CTRL + C** , чтобы отключить запись трассировки Netmon.
 
> [!NOTE]
> Если вы можете получить трассировку Netmon только на клиентском компьютере, получите IP-адрес сервера, который поможет проанализировать трассировку.

### <a name="how-to-analyze-netmon-trace"></a>Анализ трассировки Netmon

При попытке установить Telnet **8.8.8.8 888** с помощью приведенной выше трассировки Netmon вы должны увидеть следующую трассировку:

![Трассировка NetMon 1](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-1.png)

![Трассировка Netmon 2](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-2.png)
 

Это означает, что вы не могли установить TCP-подключение к серверной части **8.8.8.8** на основе порта **888**, поэтому там вы увидите два **синретрансмит** дополнительных пакета. Поскольку источник **Self-HOST2** не смог установить соединение с **8.8.8.8** в первом пакете, он будет выполнять подключение.

> [!TIP]
> - Можно нажать кнопку **загрузить фильтр**  ->  **стандартный фильтр**  ->  **адреса**  ->  **IPv4 адреса**.
> - Введите **IPv4. Address = = 8.8.8.8** в качестве фильтра и нажмите кнопку " **Применить**". После этого вы увидите только подключение с локального компьютера к целевому **8.8.8.8**.

![фильтровать адреса 1](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-1.png)
        
![Фильтрация адресов 2](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-2.png)

Ниже приведен пример того, как будет выглядеть хороший сценарий. 

- Если Telnet **8.8.8.8 53** работает нормально без каких-либо проблем, можно увидеть подтверждение TCP 3, после чего сеанс завершится с помощью подтверждения TCP 4.

    ![хороший пример сценария 1](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-1.png)
     
    ![хороший пример сценария 2](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-2.png)

- В соответствии с приведенным выше подтверждением TCP 3 вы можете увидеть следующий рабочий процесс:

    ![Рабочий процесс подтверждения TCP 3](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-3-handshake-workflow.png)
 
- Подтверждение TCP 4 для завершения сеанса и его рабочего процесса будут показаны следующим образом:

    ![Подтверждение TCP 4](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake.png)

    ![Рабочий процесс подтверждения TCP 4](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake-workflow.png) 


## <a name="next-steps"></a>Дальнейшие шаги

Для получения дополнительных сведений об устранении неполадок воспользуйтесь следующими ресурсами:

*  [Блог о Фабрике данных](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Запросы на добавление функции в Фабрику данных](https://feedback.azure.com/forums/270578-data-factory)
*  [Видео по Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Раздел вопросов и ответов на сайте Майкрософт](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Форум о переполнении стека для фабрики данных](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Сведения о Фабрике данных в Twitter](https://twitter.com/hashtag/DataFactory)
*  [Пошаговое руководств по производительности потоков данных сопоставления](concepts-data-flow-performance.md)
