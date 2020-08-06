---
title: Устранение неполадок с локальной средой выполнения интеграции в Фабрике данных Azure
description: Узнайте, как устранять неполадки с локальной средой выполнения интеграции в Фабрике данных Azure.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 08/05/2020
ms.author: abnarain
ms.openlocfilehash: 49d173e0d0f2b96c385b4325335483d25e9a7c2d
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/05/2020
ms.locfileid: "87800719"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Устранение неполадок с локальной средой выполнения интеграции

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

В этой статье рассматриваются стандартные методы устранения неполадок с локальной средой выполнения интеграции в Фабрике данных Azure.

## <a name="gather-self-hosted-ir-logs-from-azure-data-factory"></a>Сбор собственных IR-журналов из фабрики данных Azure

Для неудачных действий, выполняемых на автономных и общих IR-серверах, фабрика данных Azure поддерживает просмотр и отправку журналов ошибок. Чтобы получить идентификатор отчета об ошибке, выполните следующие действия, а затем введите идентификатор отчета, чтобы найти связанные известные проблемы.

1. Перейдите на страницу **запуски действий** .

1. В столбце " **Ошибка** " нажмите кнопку ниже.

    ![Страница "запуски действий"](media/self-hosted-integration-runtime-troubleshoot-guide/activity-runs-page.png)

1. Вы увидите связанные журналы для запуска невыполненного действия. Нажмите кнопку **отправить журналы** , чтобы получить дополнительную помощь.

    ![Отправить журналы](media/self-hosted-integration-runtime-troubleshoot-guide/send-logs.png)

1. Можно выбрать журналы, которые вы хотите отправить. Для *автономных IR*-файлов можно передать журналы, связанные с неудачными действиями, или все журналы на собственном узле IR. Для *общих IR*-файлов можно отправлять только журналы, связанные с неудачными действиями.

    ![Выбор журналов](media/self-hosted-integration-runtime-troubleshoot-guide/choose-logs.png)

1. При отправке журналов следует сохранить запись идентификатора отчета, если требуется дополнительная помощь по устранению проблемы.

    ![Отправить журналы](media/self-hosted-integration-runtime-troubleshoot-guide/upload-logs.png)

> [!NOTE]
> Просмотр журнала и отправка запросов будут выполняться во всех подключенных к сети экземплярах для самостоятельного размещения. Убедитесь, что все автономные экземпляры IR в сети в случае отсутствия журналов. 


## <a name="self-hosted-ir-general-failure-or-error"></a>Общая неисправность или ошибка в локальной среде IR

### <a name="tlsssl-certificate-issue"></a>Проблемы с сертификатом TLS/SSL

#### <a name="symptoms"></a>Симптомы

При попытке включить сертификат TLS/SSL (дополнительно) из локальной сети **IR Configuration Manager**  ->  **Удаленный доступ из интрасети**после выбора сертификата TLS/SSL появляется следующее сообщение об ошибке:

`Remote access settings are invalid. Identity check failed for outgoing message. The expected DNS identity of the remote endpoint was ‘abc.microsoft.com’ but the remote endpoint provided DNS claim ‘microsoft.com’. If this is a legitimate remote endpoint, you can fix the problem by explicitly specifying DNS identity ‘microsoft.com’ as the Identity property of EndpointAddress when creating channel proxy.`

В приведенном выше случае пользователь использует сертификат с "microsoft.com" в качестве последнего элемента.

#### <a name="cause"></a>Причина

Это известная проблема в WCF: Проверка WCF TLS/SSL проверяет только последний DNSName в сети SAN. 

#### <a name="resolution"></a>Решение

Сертификат с подстановочными знаками поддерживается в локальной среде фабрики данных Azure версии 2. Как правило, эта проблема возникает из-за неверного сертификата SSL. Последний DNSName в сети SAN должен быть допустимым. Выполните следующие действия, чтобы проверить его. 
1.  Откройте консоль управления, дважды проверьте как *Тема* , так и *альтернативное имя субъекта* в сведениях о сертификате. В приведенном выше случае, например, последний элемент в *альтернативном имени субъекта*("DNS-имя = Microsoft.com.com") является недопустимым.
2.  Чтобы удалить неправильное DNS-имя, обратитесь в компанию по выдаче сертификата.

### <a name="concurrent-jobs-limit-issue"></a>Проблемы с ограничением параллельных заданий

#### <a name="symptoms"></a>Симптомы

При попытке увеличить ограничение на количество параллельных заданий в пользовательском интерфейсе фабрики данных Azure он зависает при неограниченном *обновлении* .
Максимальное значение одновременных заданий было равно 24, и вы хотите увеличить число, чтобы задания могли выполняться быстрее. Минимальное значение, которое можно ввести, равно 3, а максимальное значение — 32. Вы увеличили значение с 24 до 32 и нажимаю кнопку *Update (обновить* ) в пользовательском интерфейсе, который был задержан при *обновлении* , как показано ниже. После обновления клиент по-прежнему видел значение 24 и никогда не обновлялся до 32.

![Обновление состояния](media/self-hosted-integration-runtime-troubleshoot-guide/updating-status.png)

#### <a name="cause"></a>Причина

Существует ограничение для параметра, которое зависит от Логиккоре компьютера и памяти. можно просто изменить его на меньшее значение, например 24, и увидеть результат.

> [!TIP] 
> - Дополнительные сведения о том, что такое число ядер, и о том, как найти количество ядер на компьютере, см. в [этой статье](https://www.top-password.com/blog/find-number-of-cores-in-your-cpu-on-windows-10/).
> - Дополнительные сведения о вычислении Math. log см. в [этой статье](https://www.rapidtables.com/calc/math/Log_Calculator.html).


### <a name="self-hosted-ir-ha-ssl-certificate-issue"></a>Несамостоятельная Ошибка SSL-сертификата с высоким уровнем доступности

#### <a name="symptoms"></a>Симптомы

Автономный узел ИК-работ сообщил об ошибке ниже:

`Failed to pull shared states from primary node net.tcp://abc.cloud.corp.Microsoft.com:8060/ExternalService.svc/. Activity ID: XXXXX The X.509 certificate CN=abc.cloud.corp.Microsoft.com, OU=test, O=Microsoft chain building failed. The certificate that was used has a trust chain that cannot be verified. Replace the certificate or change the certificateValidationMode. The revocation function was unable to check revocation because the revocation server was offline.`

#### <a name="cause"></a>Причина

При обработке обращений, связанных с подтверждением SSL/TLS, могут возникать некоторые проблемы, связанные с проверкой цепочки сертификатов. 

#### <a name="resolution"></a>Решение

- Ниже приведен краткий и понятный способ устранения неполадок при сборке цепочки сертификатов X. 509.
 
    1. Экспортируйте сертификат, который необходимо проверить. Перейдите к разделу Управление сертификатом компьютера и найдите сертификат, который требуется проверить, и щелкните правой кнопкой мыши **все задачи**  ->  **Экспорт**.
    
        ![Экспорт задач](media/self-hosted-integration-runtime-troubleshoot-guide/export-tasks.png)

    2. Скопируйте экспортированный сертификат на клиентский компьютер. 
    3. На стороне клиента выполните приведенную ниже команду в CMD. Убедитесь, что вы заменили ниже *\<certificate path>* и *\<output txt file path>* заполнители с соответствующими путями.
    
        ```
        Certutil -verify -urlfetch    <certificate path>   >     <output txt file path> 
        ```

        Пример:

        ```
        Certutil -verify -urlfetch c:\users\test\desktop\servercert02.cer > c:\users\test\desktop\Certinfo.txt
        ```
    4. Проверьте, нет ли ошибок в выходном TXT-файле. Сводку ошибок можно найти в конце файла txt.

        Пример: 

        ![Сводка ошибок](media/self-hosted-integration-runtime-troubleshoot-guide/error-summary.png)

        Если в конце файла журнала нет ошибок, как показано ниже, можно считать, что цепочка сертификатов успешно создана на клиентском компьютере.
        
        ![Нет ошибок в файле журнала](media/self-hosted-integration-runtime-troubleshoot-guide/log-file.png)      

- Если в файле сертификата настроены AIA, CDP и OCSP. Мы можем проверить его более интуитивно понятным образом.
 
    1. Эти сведения можно получить, проверив сведения о сертификате.
    
        ![Сведения о сертификате](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-detail.png)
    1. Выполните команду ниже. Убедитесь, что заменили *\<certificate path>* заполнитель связанным путем к сертификату.
    
        ```
          Certutil   -URL    <certificate path> 
        ```
    1. Затем откроется **средство извлечения URL-адресов** . Чтобы проверить сертификаты из AIA, CDP и OCSP, нажмите кнопку **получить** .

        ![Кнопка извлечения](media/self-hosted-integration-runtime-troubleshoot-guide/retrieval-button.png)
 
        Цепочка сертификатов может быть создана успешно, если сертификат из AIA имеет значение "Проверено", а сертификат из CDP или OCSP — "Проверено".

        Если вы видите ошибку при получении AIA, CDP, обратитесь к группе Network, чтобы подготовить клиентский компьютер к подключению к целевому URL-адресу. Он будет достаточно, если можно проверить путь HTTP или путь LDAP.

### <a name="self-hosted-ir-could-not-load-file-or-assembly"></a>Локальной IR-среде не удалось загрузить файл или сборку

#### <a name="symptoms"></a>Симптомы

`Could not load file or assembly 'XXXXXXXXXXXXXXXX, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified. Activity ID: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3`
 
Пример: 

`Could not load file or assembly 'System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified. Activity ID: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3`

#### <a name="cause"></a>Причина

При работе с монитором обработки можно увидеть следующий результат:

[![Монитор процессов](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png)](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png#lightbox)

> [!TIP] 
> Вы можете задать фильтр, как показано на снимке экрана ниже.
> Он сообщает нам, что библиотека DLL **System. ValueTuple** не находится в папке, связанной с глобальным каталогом, или в папке *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway*или в каталоге *c:\Program Files\Microsoft Integration Runtime\4.0\Shared* .
> По сути, она сначала загрузит библиотеку DLL из папки *GAC* , а затем из папки *шлюза* — из *общего доступа* и из нее. Таким образом, можно разместить библиотеку DLL по любому пути, который может быть полезен.

![Настройка фильтров](media/self-hosted-integration-runtime-troubleshoot-guide/set-filters.png)

#### <a name="resolution"></a>Решение

Можно найти, что **System.ValueTuple.dll** находится в папке *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway\DataScan* . Скопируйте **System.ValueTuple.dll** в папку *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway* , чтобы устранить проблему.

Один и тот же метод можно использовать для устранения проблем с отсутствующими файлами или сборками.

#### <a name="more-information"></a>Дополнительные сведения

Причина, по которой вы видите System.ValueTuple.dll в разделе *%WINDIR%\Microsoft.NET\assembly* и *%виндир%\ассембли* , заключается в том, что это поведение .NET. 

В приведенной ниже ошибке можно ясно увидеть, что сборка *System. ValueTuple* отсутствует. Поэтому такая ситуация возникает, когда приложение пытается проверить сборку *System.ValueTuple.dll*.
 
`<LogProperties><ErrorInfo>[{"Code":0,"Message":"The type initializer for 'Npgsql.PoolManager' threw an exception.","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.TypeInitializationException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[{"Code":0,"Message":"Could not load file or assembly 'System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified.","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.IO.FileNotFoundException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[]}]}]</ErrorInfo></LogProperties>`
 
Дополнительные сведения о глобальном кэше сборок см. в [этой статье](https://docs.microsoft.com/dotnet/framework/app-domains/gac).


### <a name="how-to-audit-self-hosted-ir-key-missing"></a>Аудит отсутствия самостоятельно размещенного IR-ключа

#### <a name="symptoms"></a>Симптомы

Локальная среда выполнения интеграции внезапно переходит в автономный режим без ключа, в журнале событий отображается следующее сообщение об ошибке:`Authentication Key is not assigned yet`

![Отсутствует ключ проверки подлинности](media/self-hosted-integration-runtime-troubleshoot-guide/key-missing.png)

#### <a name="cause"></a>Причина

- Локально размещенный IR-узел или логический саморазмещаемый IR на портале удаляется.
- Очистка выполняется.

#### <a name="resolution"></a>Решение

Если ни одна из описанных выше причин не будет применена, перейдите в папку с именем *%Програмдата%\микрософт\дата трансфер\датаманажементгатевай*и проверьте, не удален ли файл **конфигурации** . Если она удалена, [следуйте указаниям по](https://www.netwrix.com/how_to_detect_who_deleted_file.html) аудиту удаления файла.

![Проверить файл конфигураций](media/self-hosted-integration-runtime-troubleshoot-guide/configurations-file.png)


### <a name="cannot-use-self-hosted-ir-to-bridge-two-on-premises-data-stores"></a>Невозможно использовать локально размещенное IR для моста двух локальных хранилищ данных

#### <a name="symptoms"></a>Симптомы

После создания самостоятельно размещенной IRs для исходных и целевых хранилищ данных необходимо подключить эти две учетные данные IRs для завершения копирования. Если хранилища данных настроены в разных виртуальных сетей или не могут понять механизм шлюза, будут обнаружены такие ошибки, как: *не удается найти драйвер источника на целевом IR*. *источник не может быть доступен для целевого IR*.
 
#### <a name="cause"></a>Причина

Локальная среда IR разработана как центральный узел действия копирования, а не агент клиента, который необходимо установить для каждого хранилища данных.
 
В приведенном выше случае связанная служба для каждого хранилища данных должна быть создана с одним и тем же IR, а IR должна иметь возможность доступа к обоим хранилищам данных через сеть. Независимо от того, установлен ли IR с исходным хранилищем данных, конечным хранилищем данных или третьим компьютером, если две связанные службы создаются с разными IRs, но используются в одном и том же действии копирования, будет использоваться целевой IR, а драйверы для обоих хранилищ данных должны быть установлены на компьютере назначения.

#### <a name="resolution"></a>Решение

Установите драйверы для источника и назначения на компьютере назначения IR и убедитесь, что он имеет доступ к исходному хранилищу данных.
 
Если трафик не может пройти через сеть между двумя хранилищами данных (например, они настроены в двух виртуальных сетейах), вы не сможете завершить копирование в одно действие даже при установленном IR. В этом случае вы можете создать два действия копирования с двумя IRs, каждое в вентиляционном канале: 1 IR для копирования из хранилища данных 1 в хранилище BLOB-объектов Azure, а другое — для копирования из хранилища BLOB-объектов Azure в хранилище данных 2. Это может имитировать требование использования IR для создания моста, соединяющего два хранилища данных, которые отключены.


### <a name="credential-sync-issue-causes-credential-lost-from-ha"></a>Ошибка синхронизации учетных данных приводит к потере высокой доступности учетных данных.

#### <a name="symptoms"></a>Симптомы

Учетные данные источника данных "КСКСКСКСКСКСКСКСКСКС" удаляются из текущего Integration Runtime узла с полезной нагрузкой "при удалении службы Link на портал Azure или если задача содержит неправильные полезные данные, создайте новую службу связи с вашими учетными данными".

#### <a name="cause"></a>Причина

Локальная среда IR встроена в режим высокой доступности с двумя узлами, но не находится в состоянии синхронизации учетных данных. Это означает, что учетные данные, хранящиеся в узле Dispatcher, не синхронизируются с другими рабочими узлами. Если отработка отказа происходит от узла Dispatcher к рабочему узлу, но учетные данные существовали только в предыдущем узле Dispatcher, то при попытке доступа к учетным данным задача завершится ошибкой, и вы увидите сообщение об ошибке выше.

#### <a name="resolution"></a>Решение

Единственный способ избежать этой проблемы — убедиться, что два узла находятся в состоянии синхронизации учетных данных. В противном случае необходимо переввести учетные данные для нового Dispatcher.


### <a name="cannot-choose-the-certificate-due-to-private-key-missing"></a>Не удается выбрать сертификат из-за отсутствия закрытого ключа

#### <a name="symptoms"></a>Симптомы

1.  Импортируйте PFX-файл в хранилище сертификатов.
2.  При выборе сертификата с помощью интерфейса Configuration Manager IR вы сталкиваетесь с указанным ниже сообщением об ошибке:

    ![Отсутствует закрытый ключ](media/self-hosted-integration-runtime-troubleshoot-guide/private-key-missing.png)

#### <a name="cause"></a>Причина

- Учетная запись пользователя имеет низкую привилегию и не может получить доступ к закрытому ключу.
- Сертификат был создан как подпись, но не является обменом ключами.

#### <a name="resolution"></a>Решение

1.  Используйте привилегированную учетную запись, которая может получить доступ к закрытому ключу для работы с пользовательским интерфейсом.
2.  Выполните следующую команду, чтобы импортировать сертификат:
    
    ```
    certutil -importpfx FILENAME.pfx AT_KEYEXCHANGE
    ```


## <a name="self-hosted-ir-setup"></a>Настройка самостоятельно размещенного IR

### <a name="the-integration-runtime-registration-error"></a>Ошибка регистрации Integration Runtime 

#### <a name="symptoms"></a>Симптомы

Иногда нам нужно выполнить локально размещенное IR-соединение в другой учетной записи по следующим причинам:
- Политика компании запрещает учетную запись службы.
- Требуется проверка подлинности.

После изменения учетной записи службы на панели службы может оказаться, что Integration Runtime перестанет работать.

![Ошибка регистрации IR](media/self-hosted-integration-runtime-troubleshoot-guide/ir-registration-error.png)

#### <a name="cause"></a>Причина

Существует множество ресурсов, которые предоставляются только учетной записи службы. При изменении учетной записи службы на другую учетную запись разрешения всех зависимых ресурсов остаются неизменными.

#### <a name="resolution"></a>Решение

Перейдите в журнал событий Integration Runtime, чтобы проверить ошибку.

![Журнал событий IR](media/self-hosted-integration-runtime-troubleshoot-guide/ir-event-log.png)

Если эта ошибка отображается как выше *UnauthorizedAccessException*, следуйте приведенным ниже инструкциям.


1. Проверьте учетную запись службы входа *DIAHostService* на панели служб Windows.

    ![Учетная запись службы входа](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

2. Проверьте, имеет ли учетная запись службы входа разрешение на чтение для папки: *%програмдата%\микрософт\дататрансфер\датаманажементгатевай*.

    - По умолчанию, если учетная запись входа в службу не была изменена, она должна иметь разрешение на доступ к R/W.

        ![Разрешение службы](media/self-hosted-integration-runtime-troubleshoot-guide/service-permission.png)

    - Если вы изменили учетную запись входа в службу, выполните следующие действия, чтобы устранить эту ошибку.
        1. Очистить Удаление текущего саморазмещенного IR.
        1. Установите собственные IR-биты.
        1. Чтобы изменить учетную запись службы, следуйте приведенным ниже инструкциям. 
            1. Перейдите в папку установки селфхостед IR, перейдите в папку: *Microsoft Integration Runtime\4.0\Shared*.
            1. Запустите командную строку с повышенными привилегиями. Замените *\<user>* и *\<password>* собственным именем пользователя и паролем, а затем выполните следующую команду:
                       
                ```
                dmgcmd.exe -SwitchServiceAccount "<user>" "<password>"
                ```
            1. Если вы хотите перейти на учетную запись LocalSystem, убедитесь, что для этой учетной записи используется правильный формат. Ниже приведен пример правильного формата.

                ```
                dmgcmd.exe -SwitchServiceAccount "NT Authority\System" ""
                ```         
                **Не** используйте формат, как показано ниже.

                ```
                dmgcmd.exe -SwitchServiceAccount "LocalSystem" ""
                ```              
            1. В качестве альтернативы, поскольку у локальной системы есть более высокий уровень привилегий, чем у администратора, вы можете напрямую изменить ее в службах.
            1. Для учетной записи входа в службу IR можно использовать локальную систему или пользователя домена.            
        1. Зарегистрируйте Integration Runtime.

Если ошибка отображается так: *не удалось запустить службу "Integration Runtime Service" (DIAHostService). Убедитесь, что у вас есть необходимые привилегии для запуска системных служб*, следуя приведенным ниже инструкциям.

1. Проверьте учетную запись службы входа *DIAHostService* на панели служб Windows.
   
    ![Учетная запись службы входа](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

2. Проверьте, имеет ли учетная запись службы входа разрешение **Вход в качестве службы** для запуска службы Windows:

    ![Вход в качестве службы](media/self-hosted-integration-runtime-troubleshoot-guide/logon-as-service.png)

#### <a name="more-information"></a>Дополнительные сведения

Если в вашем случае не применяется ни один из двух шаблонов, приведенных выше, попробуйте получить следующие журналы событий Windows: 
- Журналы приложений и служб — > Integration Runtime
- Журналы Windows — приложение >

### <a name="cannot-find-register-button-to-register-a-self-hosted-ir"></a>Не удается найти кнопку "зарегистрировать", чтобы зарегистрировать локальное IR-соединение    

#### <a name="symptoms"></a>Симптомы

Не удалось найти кнопку **регистрации** в пользовательском интерфейсе Configuration Manager при регистрации локальной среды IR.

![Кнопка "нет регистрации"](media/self-hosted-integration-runtime-troubleshoot-guide/no-register-button.png)

#### <a name="cause"></a>Причина

С момента выпуска *Integration Runtime 3,0*кнопка **регистрация** на существующем Integration Runtime узле была удалена, чтобы обеспечить более надежную и безопасную среду. Если узел был зарегистрирован в некоторых Integration Runtime (в сети или нет), для повторной регистрации его в другой Integration Runtime необходимо удалить предыдущий узел, а затем установить и зарегистрировать узел.

#### <a name="resolution"></a>Решение

1. Перейдите на панель управления, чтобы удалить существующие Integration Runtime.

    > [!IMPORTANT] 
    > В приведенном ниже процессе выберите Да. Не оставляйте данные во время процесса удаления.

    ![Удаление данных](media/self-hosted-integration-runtime-troubleshoot-guide/delete-data.png)

1. Если у вас нет MSI для установщика среды выполнения интеграции, перейдите в [Центр загрузки](https://www.microsoft.com/en-sg/download/details.aspx?id=39717) , чтобы скачать последнюю Integration Runtime.
1. Установите MSI и зарегистрируйте Integration Runtime.


### <a name="unable-to-register-the-self-hosted-ir-due-to-localhost"></a>Не удалось зарегистрировать локальную IR в связи с localhost    

#### <a name="symptoms"></a>Симптомы

Не удается зарегистрировать локальную IR на новом компьютере при get_LoopbackIpOrName.

**Отладка:** Произошла ошибка времени выполнения.
Инициализатор типа для "Microsoft. Диаженсост. Датасаурцекаче" вызвал исключение.
Во время поиска базы данных произошла неустранимая ошибка.
 
**Сведения об исключении:** System. TypeInitializationException: инициализатор типа для "Microsoft. передает. Диаженсост. Датасаурцекаче" вызвал исключение. ---> System .NET. Sockets. SocketException: Неустранимая ошибка произошла во время поиска в базе данных в System .NET. DNS. функцию getaddrinfo (строковое имя).

#### <a name="cause"></a>Причина

Эта ошибка обычно возникает при разрешении localhost.

#### <a name="resolution"></a>Решение

Используйте localhost 127.0.0.1 для размещения файла и устраните подобную проблему.


### <a name="self-hosted-setup-failed"></a>Сбой установки в автономном размещении    

#### <a name="symptoms"></a>Симптомы

Невозможно удалить существующий IR-объект или установить новый IR или обновить существующий IR на новый IR.

#### <a name="cause"></a>Причина

Установка зависит от службы установщик Windows. Возможны некоторые причины, которые могут вызвать проблемы при установке:
- Недостаточно места на диске
- Нехватка разрешений
- По какой-либо причине служба NT заблокирована
- Загрузка ЦП слишком высока
- Файл MSI размещается в сетевом расположении с задержкой
- Некоторые системные файлы или реестры были непреднамеренно затронуты


## <a name="self-hosted-ir-connectivity-issues"></a>Проблемы с подключением к локальной среде IR

### <a name="self-hosted-integration-runtime-cant-connect-to-cloud-service"></a>Локальной среде выполнения интеграции не удается подключиться к облачной службе

#### <a name="symptoms"></a>Симптомы

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

### <a name="connectivity-issue-between-self-hosted-ir-and-data-factory-or-self-hosted-ir-and-data-sourcesink"></a>Проблемы с подключением между локальным IR и фабрикой данных или автономным IR-кодом и приемником данных

Чтобы устранить неполадки с сетевым подключением, необходимо знать, как [получить трассировку сети](#how-to-collect-netmon-trace), понять, как ее использовать, и [проанализировать трассировку Netmon](#how-to-analyze-netmon-trace) перед применением средств NetMon в реальных случаях из автономной среды IR.

#### <a name="symptoms"></a>Симптомы

Иногда при устранении проблем с подключением, как показано ниже, между локальной сетью IR и фабрикой данных. 

![Сбой HTTP-запроса](media/self-hosted-integration-runtime-troubleshoot-guide/http-request-error.png)

Или между самостоятельно размещенным IR и источником данных или приемником, мы будем столкнуться со следующими ошибками:

`Copy failed with error:Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server: ‘IP address’`

`One or more errors occurred. An error occurred while sending the request. The underlying connection was closed: An unexpected error occurred on a receive. Unable to read data from the transport connection: An existing connection was forcibly closed by the remote host. An existing connection was forcibly closed by the remote host Activity ID.`

#### <a name="resolution"></a>Решение.

При возникновении описанных выше проблем ознакомьтесь со следующими инструкциями, чтобы устранить неполадки.

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


## <a name="self-hosted-ir-sharing"></a>Предоставление общего доступа к локальной среде IR

### <a name="share-self-hosted-ir-from-a-different-tenant-is-not-supported"></a>Совместное использование автономных IR-файлов из другого клиента не поддерживается 

#### <a name="symptoms"></a>Симптомы

Вы можете заметить другие фабрики данных (в разных клиентах) при попытке совместного использования локальной среды IR из пользовательского интерфейса фабрики данных Azure, но не может совместно использовать локальную IR в фабриках данных, находящиеся в разных клиентах.

#### <a name="cause"></a>Причина

Локальная среда IR не может быть общей для перекрестных клиентов.


## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительных сведений об устранении неполадок воспользуйтесь следующими ресурсами:

*  [Блог о Фабрике данных](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Запросы на добавление функции в Фабрику данных](https://feedback.azure.com/forums/270578-data-factory)
*  [Видео по Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Раздел вопросов и ответов на сайте Майкрософт](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Форум о переполнении стека для фабрики данных](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Сведения о Фабрике данных в Twitter](https://twitter.com/hashtag/DataFactory)
*  [Пошаговое руководств по производительности потоков данных сопоставления](concepts-data-flow-performance.md)
