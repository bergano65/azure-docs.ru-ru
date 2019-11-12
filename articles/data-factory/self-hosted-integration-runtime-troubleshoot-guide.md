---
title: Устранение неполадок с локальной средой выполнения интеграции в фабрике данных Azure
description: Узнайте, как устранять проблемы с локальной средой выполнения интеграции в фабрике данных Azure.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/07/2019
ms.author: abnarain
ms.openlocfilehash: 9adbc3d7d30aeb8c7cb2b89c326ac2b39a2e8d2b
ms.sourcegitcommit: 6dec090a6820fb68ac7648cf5fa4a70f45f87e1a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/11/2019
ms.locfileid: "73907286"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Устранение неполадок в локальной среде выполнения интеграции

В этой статье рассматриваются распространенные методы устранения неполадок для локальной среды выполнения интеграции в фабрике данных Azure.

## <a name="common-errors-and-resolutions"></a>Распространенные ошибки и способы их устранения

### <a name="error-message-self-hosted-integration-runtime-is-unable-to-connect-to-cloud-service"></a>Сообщение об ошибке: локальной среде выполнения интеграции не удается подключиться к облачной службе.

- **Признак**. 

    ![Проблемы с подключением к собственному соединению](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

- **Причина**: локальная среда выполнения интеграции не может подключиться к службе фабрики данных (серверной части). Чаще всего это не вызывается из-за параметров сети в брандмауэре.

- **Решение:** 

    1. Проверьте, запущена ли служба Windows "Integration Runtime Service".
    
        ![Состояние выполнения локальной службы IR](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
    2. Если служба Windows, как показано в [1], запущена, выполните приведенные ниже инструкции.

        1. Если параметр "прокси" не настроен для локальной среды выполнения интеграции (параметры по умолчанию не являются конфигурацией прокси-сервера), выполните следующую команду PowerShell на компьютере, где установлена локальная среда выполнения интеграции: 
            
            ```powershell
            (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
            ```
            > [!NOTE] 
            > URL-адрес службы может отличаться в зависимости от расположения фабрики данных. URL-адрес службы можно найти в разделе Пользовательский интерфейс ADF — > подключения — > среды выполнения интеграции — > изменить локальные узлы IR > — > URL-адреса службы просмотра.
            
            Ниже приведен ожидаемый ответ:
            
            ![Ответ команды PowerShell](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
            Если ответ отличается, выполните приведенные ниже инструкции.
            
            * Если возникает ошибка "не удалось разрешить удаленное имя", возникает проблема с DNS. Обратитесь в службу поддержки сети, чтобы устранить неполадки с устранением DNS! 
            * Если отображается сообщение об ошибке "сертификат SSL/TLS не является доверенным", проверьте, является ли сертификат для "https://wu2.frontend.clouddatahub.net/" доверенным на компьютере, установите общедоступный сертификат с помощью диспетчера сертификатов, чтобы устранить эту ошибку.
            * Проверьте окно просмотра событий Windows-> (журналы) — > журналы приложений и служб — > Integration Runtime для любых сбоев, в основном вызванных DNS, правилом брандмауэра и сетевыми параметрами компании (принудительное закрытие подключения). Чтобы получить дополнительные устранять, обратитесь к группе разработчиков сети, так как каждая компания имеет настроенные параметры сети.

        2. Если для локальной среды выполнения интеграции настроено значение "прокси", проверьте, имеет ли прокси-сервер доступ к конечной точке службы. Пример команды см. [здесь](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies).    
                
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

            Ниже приведен ожидаемый ответ:
            
            ![Ответ команды PowerShell 2](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

            > [!NOTE] 
            > Вопросы прокси-сервера:
            > * Проверьте, требует ли прокси-сервер список разрешений. В этом случае [эти домены](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations#firewall-requirements-for-on-premisesprivate-network) список разрешений.
            > * Проверьте, является ли сертификат TLS/SSL для "wu2.frontend.clouddatahub.net/" доверенным на прокси-сервере.
            > * При использовании проверки подлинности Active Directory в прокси-сервере измените учетную запись службы на учетную запись пользователя, которая может обращаться к прокси-серверу как "Integration Runtime Service".

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>Сообщение об ошибке: узел локальной среды выполнения интеграции/логический Шир находится в неактивном состоянии или "выполняется (ограничено)"

- **Причина**. в неактивном состоянии может отображаться локально размещенный IR-узел, как показано на снимке экрана ниже:

    ![Неактивный узел IR с автономным размещением](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

    Это происходит, когда узлы не могут взаимодействовать друг с другом. 

- **Решение:** 

    Войдите в размещенную виртуальную машину узла и откройте представление событий в разделе Журналы приложений и служб — > Integration Runtime, отфильтруйте все журналы ошибок. 

     1. Если журнал ошибок содержит: 
    
        **Журнал ошибок**: System. ServiceModel. EndpointNotFoundException: не удалось подключиться к net. TCP://кскскскскскскс.бвлд.ком: 8060/екстерналсервице. svc/воркерманажер. Попытки подключения последней за период времени 00:00:00.9940994. Код ошибки TCP 10061: не удалось установить подключение, так как целевой компьютер отказался от его 10.2.4.10:8060.  ---> System .NET. Sockets. SocketException: не удалось установить подключение, так как целевой компьютер отказался от его 10.2.4.10:8060.
    
           на компьютере .NET. Sockets. Socket. Доконнект (конечная точка Ендпоинтснапшот, Соккетаддресс Соккетаддресс)
           
           на компьютере .NET. Sockets. Socket. Connect (конечная точка Ремотип)
           
           в System. ServiceModel. Channels. Соккетконнектионинитиатор. Connect (URI URI, время ожидания TimeSpan)
    
        **Решение.** запустите командную строку: telnet 10.2.4.10 8060.
        
        Если вы получите сообщение об ошибке ниже, обратитесь к ИТ для получения помощи в решении этой проблемы. После успешной установки Telnet обратитесь в службу поддержки Майкрософт, если у вас по-прежнему возникают проблемы с состоянием ИНФРАКРАСного узла.
        
        ![Ошибка командной строки](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
     2. Если журнал ошибок содержит:
     
        **Журнал ошибок:** Не удается подключиться к диспетчеру рабочих ролей: net. TCP://кскскскскскс: 8060/Екстерналсервице. svc/отсутствуют записи DNS для узла azranlcir01r1. Отсутствуют известные сведения об исключении: System. ServiceModel. EndpointNotFoundException: отсутствуют записи DNS для узла XXXXX. ---> System .NET. Sockets. SocketException. такой узел не известен в системе .NET. DNS. функцию getaddrinfo (строковое имя) в System .NET. DNS. Интерналжесостбинаме (строка hostName, логическое includeIPv6) в системе .NET. DNS. GetHostEntry (String Хостнамеораддресс ) в System. ServiceModel. Channels. Днскаче. Resolve (URI URI)---конец трассировки стека внутреннего исключения---трассировка стека сервера: в System. ServiceModel. Channels. Днскаче. Resolve (URI URI) 
    
        **Решение:** Одно из следующих двух действий может помочь решить проблему:
         1. Разместите все узлы в одном домене.
         2. Добавьте сопоставление IP и узлов во все файлы узлов размещенной виртуальной машины.


## <a name="next-steps"></a>Дополнительная информация

Для получения дополнительных сведений об устранении неполадок воспользуйтесь следующими ресурсами:

*  [Блог, посвященный службе "Фабрика данных"](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Запросы функций фабрики данных](https://feedback.azure.com/forums/270578-data-factory)
*  [Видеоролики по Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Форум MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Форум Stack Overflow для фабрики данных](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Сведения о фабрике данных в Twitter](https://twitter.com/hashtag/DataFactory)
*  [Пошаговое руководств по производительности потоков данных сопоставления ADF](concepts-data-flow-performance.md)
