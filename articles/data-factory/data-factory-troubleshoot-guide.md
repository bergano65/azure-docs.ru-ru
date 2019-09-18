---
title: Устранение неполадок фабрики данных Azure | Документация Майкрософт
description: Узнайте, как устранять неполадки действий внешнего управления в фабрике данных Azure.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: 45aa1354f6009d5eccd48f85f993bae8949139e3
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058978"
---
# <a name="troubleshoot-azure-data-factory"></a>Устранение неполадок фабрики данных Azure

В этой статье рассматриваются распространенные методы устранения неполадок для действий внешнего управления в фабрике данных Azure.

## <a name="connector-and-copy-activity"></a>Действие соединителя и копирования

Сведения о проблемах с соединителем, например ошибка при использовании действия копирования, см. в статье [Устранение неполадок соединителей фабрики данных Azure](connector-troubleshoot-guide.md).

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code--3200"></a>Код ошибки:  3200

- **Сообщение**. Ошибка 403.

- **Причина**:`The Databricks access token has expired.`

- **Рекомендация**. По умолчанию маркер доступа Azure Databricks действителен в течение 90 дней. Создайте новый токен и обновите связанную службу.


### <a name="error-code--3201"></a>Код ошибки:  3201

- **Сообщение**:`Missing required field: settings.task.notebook_task.notebook_path.`

- **Причина**:`Bad authoring: Notebook path not specified correctly.`

- **Рекомендация**. Укажите путь к записной книжке в действии "кирпичы".

<br/>

- **Сообщение**:`Cluster   ... does not exist.`

- **Причина**:`Authoring error: Databricks cluster does not exist or has been deleted.`

- **Рекомендация**. Убедитесь, что кластер кирпичей существует.

<br/>

- **Сообщение**:`Invalid Python file URI.... Please visit Databricks user guide for supported URI schemes.`

- **Причина**:`Bad authoring.`

- **Рекомендация**. Укажите абсолютные пути для схем адресации рабочей области или `dbfs:/folder/subfolder/foo.py` для файлов, хранящихся в файловой системе "кирпичы".

<br/>

- **Сообщение**:`{0} LinkedService should have domain and accessToken as required properties.`

- **Причина**:`Bad authoring.`

- **Рекомендация**. Проверьте [определение связанной службы](compute-linked-services.md#azure-databricks-linked-service).

<br/>

- **Сообщение**:`{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **Причина**:`Bad authoring.`

- **Рекомендация**. Проверьте [определение связанной службы](compute-linked-services.md#azure-databricks-linked-service).

<br/>

- **Сообщение**:`Node type Standard_D16S_v3 is not supported. Supported node types:   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3,   Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2,   Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3,   Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2,   Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2,   Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3,   Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s,   Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2,   Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2,   Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3,   Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2,   Standard_L64s_v2, Standard_L80s_v2.`

- **Причина**:`Bad authoring.`

- **Рекомендация**. См. сообщение об ошибке. 

<br/>

### <a name="error-code--3202"></a>Код ошибки:  3202

- **Сообщение**:`There were already 1000 jobs created in past 3600 seconds, exceeding rate limit:   1000 job creations per 3600 seconds.`

- **Причина**:`Too many Databricks runs in an hour.`

- **Рекомендация**. Проверьте все конвейеры, использующие эту рабочую область блоков, для их частоты создания заданий.  Если конвейеры запустили слишком много блоков обработки в статистическом модуле, перенесите некоторые конвейеры в новую рабочую область.

<br/>

- **Сообщение**:`Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **Причина**:`Authoring error: No value provided for the parameter.`

- **Рекомендация**. Проверьте JSON конвейера и убедитесь, что все параметры в записной книжке Басепараметерс указывают непустое значение.

<br/>

- **Сообщение**. `User: `Симплеусерконтекст {UserID =..., Name =user@company.com, orgId =...}` is not   authorized to access cluster.`

- **Причина.** Пользователь, создавший маркер доступа, не может получить доступ к кластеру кирпичей данных, указанному в связанной службе.

- **Рекомендация**. Убедитесь, что пользователь имеет необходимые разрешения в рабочей области.


### <a name="error-code--3203"></a>Код ошибки:  3203

- **Сообщение**:`The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **Причина.** Работа кластера завершена. Для интерактивных кластеров это может быть условие состязания.

- **Рекомендация**. Лучший способ избежать этой ошибки — использовать кластеры заданий.


### <a name="error-code--3204"></a>Код ошибки:  3204

- **Сообщение**:`Job execution failed.`

- **Причина.**  Сообщения об ошибках указывают на различные проблемы, например непредвиденное состояние кластера или определенное действие. Чаще всего сообщение об ошибке не отображается. 

- **Рекомендация**. Н/Д


## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

Следующая таблица относится к U-SQL.


### <a name="error-code--2709"></a>Код ошибки:  2709

- **Сообщение**:`The access token is from the wrong tenant.`

- **Причина.**  Недопустимый клиент Azure Active Directory (Azure AD).

- **Рекомендация**. Недопустимый клиент Azure Active Directory (Azure AD).

<br/>

- **Сообщение**:`We cannot accept your job at this moment. The maximum number of queued jobs for   your account is 200. `

- **Причина.**  Эта ошибка вызвана регулированием Data Lake Analytics.

- **Рекомендация**. Сократите число отправленных заданий, чтобы Data Lake Analytics, изменив триггеры фабрики данных и параметры параллелизма для действий. Или увеличьте ограничения на Data Lake Analytics.

<br/>

- **Сообщение**:`This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **Причина.**  Эта ошибка вызвана регулированием Data Lake Analytics. 

- **Рекомендация**. Сократите число отправленных заданий, чтобы Data Lake Analytics, изменив триггеры фабрики данных и параметры параллелизма для действий. Или увеличьте ограничения на Data Lake Analytics.


### <a name="error-code--2705"></a>Код ошибки:  2705

- **Сообщение**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **Причина.**  Субъект-служба или сертификат не имеет доступа к файлу в хранилище.

- **Рекомендация**. Убедитесь, что субъект-служба или сертификат, предоставляемый пользователем для заданий Data Lake Analytics, имеет доступ к учетной записи Data Lake Analytics и экземпляру Data Lake Storage по умолчанию из корневой папки.


### <a name="error-code--2711"></a>Код ошибки:  2711

- **Сообщение**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **Причина.**  Субъект-служба или сертификат не имеет доступа к файлу в хранилище.

- **Рекомендация**. Убедитесь, что субъект-служба или сертификат, предоставляемый пользователем для заданий Data Lake Analytics, имеет доступ к учетной записи Data Lake Analytics и экземпляру Data Lake Storage по умолчанию из корневой папки.

<br/>

- **Сообщение**:`Cannot find the 'Azure Data Lake Store' file or folder.`

- **Причина.**  Неверный путь к файлу U-SQL, или учетные данные связанной службы не имеют доступа.

- **Рекомендация**. Проверьте путь и учетные данные, указанные в связанной службе.


### <a name="error-code--2704"></a>Код ошибки:  2704

- **Сообщение**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **Причина.**  Субъект-служба или сертификат не имеет доступа к файлу в хранилище.

- **Рекомендация**. Убедитесь, что субъект-служба или сертификат, предоставляемый пользователем для заданий Data Lake Analytics, имеет доступ к учетной записи Data Lake Analytics и экземпляру Data Lake Storage по умолчанию из корневой папки.


### <a name="error-code--2707"></a>Код ошибки:  2707

- **Сообщение**:`Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and   'DataLakeAnalyticsUri'.`

- **Причина.**  Неверная учетная запись Data Lake Analytics в связанной службе.

- **Рекомендация**. Убедитесь, что указана правильная учетная запись.


### <a name="error-code--2703"></a>Код ошибки:  2703

- **Сообщение**:`Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error   Id:").`

- **Причина.**  Ошибка связана с Data Lake Analytics. 

- **Рекомендация**. Ошибка, аналогичная примеру, означает, что задание было отправлено в Data Lake Analytics и Скрипт завершился неудачно. Исследование в Data Lake Analytics. На портале перейдите к учетной записи Data Lake Analytics и найдите задание с помощью идентификатора запуска действия фабрики данных (не идентификатора запуска конвейера). Задание содержит дополнительные сведения об ошибке и поможет вам устранить неполадки. Если разрешение неясно, обратитесь в службу поддержки Data Lake Analytics и укажите URL-адрес задания, включающий имя учетной записи и идентификатор задания.



## <a name="azure-functions"></a>Функции Azure

### <a name="error-code--3602"></a>Код ошибки:  3602

- **Сообщение**:`Invalid HttpMethod: {method}.`

- **Причина.** Метод HTTP, указанный в полезных данных действия, не поддерживается действием функции Azure. 

- **Рекомендация**. Поддерживаются Поддерживаемые методы HTTP: размещение, публикация, получение, удаление, параметры, ГОЛОВной элемент и ТРАССИРОВКа.


### <a name="error-code--3603"></a>Код ошибки:  3603

- **Сообщение**:`Response content is not a valid JObject.`

- **Причина.** Вызванная функция Azure не вернула полезные данные JSON в ответе. Действие функции Azure в фабрике данных поддерживает только содержимое ответа JSON. 

- **Рекомендация**. Обновите функцию Azure, чтобы она возвращала допустимые полезные данные JSON. Например, C# функция может возвращать `(ActionResult)new<OkObjectResult("{` \"идентификатор\":\"123.\"`}");`


### <a name="error-code--3606"></a>Код ошибки:  3606

- **Сообщение**:`Azure function activity missing function key.`

- **Причина.** Определение действия функции Azure не завершено. 

- **Рекомендация**. Убедитесь, что определение JSON входного Азурефунктион действия имеет свойство с именем "Функтионкэй".


### <a name="error-code--3607"></a>Код ошибки:  3607

- **Сообщение**:`Azure function activity missing function name.`

- **Причина.** Определение действия функции Azure не завершено. 

- **Рекомендация**. Убедитесь, что определение JSON входного действия Азурефунктион имеет свойство с именем functionName.


### <a name="error-code--3608"></a>Код ошибки:  3608

- **Сообщение**:`Call to provided Azure function '{FunctionName}' failed with status-'{statusCode}' and message - '{message}'.` 

- **Причина.** Сведения о функции Azure в определении действия могут быть неправильными. 

- **Рекомендация**. Исправьте сведения о функции Azure и повторите попытку.


### <a name="error-code--3609"></a>Код ошибки:  3609

- **Сообщение**:`Azure function activity missing functionAppUrl.` 

- **Причина.** Определение действия функции Azure не завершено. 

- **Рекомендация**. Убедитесь, что определение JSON входного Азурефунктион действия имеет свойство с именем "Функтионаппурл".


### <a name="error-code--3610"></a>Код ошибки:  3610

- **Сообщение**:`There was an error while calling endpoint.`

- **Причина.** Возможно, URL-адрес функции неверен.

- **Рекомендация**. Убедитесь, что значение "Функтионаппурл" в JSON действия указано правильно, и повторите попытку.


### <a name="error-code--3611"></a>Код ошибки:  3611

- **Сообщение**:`Azure function activity missing Method in JSON.` 

- **Причина.** Определение действия функции Azure не завершено.

- **Рекомендация**. Убедитесь, что определение JSON входного действия Азурефунктион имеет свойство с именем "метод".


### <a name="error-code--3612"></a>Код ошибки:  3612

- **Сообщение**:`Azure function activity missing LinkedService definition in JSON.`

- **Причина.** Определение действия функции Azure не завершено.

- **Рекомендация**. Убедитесь, что в определении JSON входного действия Азурефунктион содержатся связанные сведения о связанной службе.



## <a name="custom"></a>Настраиваемый

Следующая таблица относится к пакетной службе Azure.


### <a name="error-code--2500"></a>Код ошибки:  2500

- **Сообщение**:`Hit unexpected exception and execution failed.`

- **Причина**:`Can't launch command, or the program returned an error code.`

- **Рекомендация**.  Убедитесь, что исполняемый файл существует. Если программа запущена, убедитесь, что *stdout. txt* и *stderr. txt* были переданы в учетную запись хранения. Рекомендуется создавать в коде журналы копиаус для отладки.


### <a name="error-code--2501"></a>Код ошибки:  2501

- **Сообщение**:`Cannot access user batch account; please check batch account settings.`

- **Причина.** Неверный ключ доступа пакетной службы или имя пула.

- **Рекомендация**. Проверьте имя пула и ключ доступа пакетной службы в связанной службе.


### <a name="error-code--2504"></a>Код ошибки:  2504

- **Сообщение**:`Operation returned an invalid status code 'BadRequest'.` 

- **Причина.** Слишком много файлов в folderPath для настраиваемого действия. Общий размер resourceFiles не может превышать 32 768 символов.

- **Рекомендация**. Удалите ненужные файлы. Или ZIP, и добавьте команду unzip, чтобы извлечь их. Например, используйте`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;  $folder\yourProgram.exe`


### <a name="error-code--2505"></a>Код ошибки:  2505

- **Сообщение**:`Cannot create Shared Access Signature unless Account Key credentials are used.`

- **Причина.** Пользовательские действия поддерживают только учетные записи хранения, использующие ключ доступа.

- **Рекомендация**. См. описание ошибки.


### <a name="error-code--2507"></a>Код ошибки:  2507

- **Сообщение**:`The folder path does not exist or is empty: ....`

- **Причина.** В учетной записи хранения нет файлов по указанному пути.

- **Рекомендация**. Путь к папке должен содержать исполняемые файлы, которые требуется запустить.


### <a name="error-code--2508"></a>Код ошибки:  2508

- **Сообщение**:`There are duplicate files in the resource folder.`

- **Причина.** Несколько файлов с одинаковыми именами находятся в разных вложенных папках folderPath.

- **Рекомендация**. Структура папок с нестандартными действиями в разделе folderPath.  Если необходимо сохранить структуру папок, ZIP-файлы и извлечь их в пакетной службе Azure с помощью команды unzip. Например, используйте`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;   $folder\yourProgram.exe`


### <a name="error-code--2509"></a>Код ошибки:  2509

- **Сообщение**:`Batch   url ... is invalid; it must be in Uri format.` 

- **Причина.** URL-адреса пакетной службы должны быть похожи на`https://mybatchaccount.eastus.batch.azure.com`

- **Рекомендация**. См. описание ошибки.


### <a name="error-code--2510"></a>Код ошибки:  2510

- **Сообщение**:`An   error occurred while sending the request.`

- **Причина.** Недопустимый URL-адрес пакета. 

- **Рекомендация**. Проверьте URL-адрес пакета.


## <a name="hdinsight"></a>HDInsight

Следующая таблица применяется к потоковой передаче Spark, Hive, MapReduce, Pig и Hadoop.


### <a name="error-code--2300"></a>Код ошибки:  2300

- **Сообщение**:`Hadoop job submission failed. Error: The remote name could not be resolved. <br/><br/>The cluster is not found.`

- **Причина.** Указан недопустимый URI кластера. 

- **Рекомендация**.  Убедитесь, что кластер не удален и предоставлен правильный URI. При открытии URI в браузере должен отобразиться пользовательский интерфейс Ambari. Если кластер находится в виртуальной сети, URI должен быть частным URI. Чтобы открыть его, используйте виртуальную машину, которая является частью той же виртуальной сети. Дополнительные сведения см. [в разделе прямое подключение к службам Apache Hadoop](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services).

<br/>

- **Сообщение**:`Hadoop job submission failed. Job: …, Cluster: …/. Error: A task was canceled.`

- **Причина.** Истекло время ожидания отправки задания. 

- **Рекомендация**. Проблема может быть либо общим подключением HDInsight, либо сетевым подключением. Сначала убедитесь, что пользовательский интерфейс HDInsight Ambari доступен из любого браузера. Убедитесь, что учетные данные все еще действительны. Если вы используете локальную среду выполнения (IR), убедитесь, что это делается с виртуальной машины или с компьютера, на котором установлено локальное IR-соединение. Затем попробуйте отправить задание из фабрики данных еще раз. Если по-прежнему происходит сбой, обратитесь за поддержкой к группе фабрики данных.


- **Сообщение**:`Unauthorized: Ambari user name or password is incorrect  <br/><br/>Unauthorized: User admin is locked out in Ambari.   <br/><br/>403 - Forbidden: Access is denied.`

- **Причина.** Учетные данные для HDInsight неверны или истек срок их действия.

- **Рекомендация**. Исправьте учетные данные и повторно разверните связанную службу. Сначала убедитесь, что учетные данные работают в HDInsight, открыв универсальный код ресурса (URI) кластера в любом браузере и пытаясь войти в систему. Если учетные данные не работают, их можно сбросить с портал Azure.

<br/>

- **Сообщение**:`502 - Web server received an invalid response while acting as a gateway or proxy server. <br/>Bad gateway.`

- **Причина.** Эта ошибка возникла в HDInsight.

- **Рекомендация**. Эта ошибка связана с кластером HDInsight. Дополнительные сведения см. в разделе [AMBARI UI 502 Error](https://hdinsight.github.io/ambari/ambari-ui-502-error.html), [502 Errors Connect to Spark Thrift Server](https://hdinsight.github.io/spark/spark-thriftserver-errors.html), [502 Errors Connected to Spark Thrift Server](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)и [Устранение ошибок шлюза в шлюзе приложений](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502).

<br/>

- **Сообщение**:`Hadoop job submission failed. Job: …, Cluster: ... Error:   {\"error\":\"Unable to service the submit job request as   templeton service is busy with too many submit job requests. Please wait for some time before retrying the operation. Please refer to the config   templeton.parallellism.job.submit to configure concurrent requests. <br/><br/>Hadoop job submission failed. Job: xx, Cluster: name.   Error: {\"error\":\"java.io.IOException:   org.apache.hadoop.yarn.exceptions.YarnException: Failed to submit   application_1561147195099_3730 to YARN :   org.apache.hadoop.security.AccessControlException: Queue root.joblauncher already has 500 applications, cannot accept submission of application:   application_1561147195099_3730\`

- **Причина.** Слишком много заданий отправляются в HDInsight одновременно.

- **Рекомендация**. Попробуйте ограничить количество параллельных заданий, отправляемых в HDInsight. См. сведения о параллелизме действий фабрики данных, если задания отправляются одним действием. Измените триггеры, чтобы параллельные запуски конвейера были распределены по времени. Сведения об ошибке см. `templeton.parallellism.job.submit` в документации по HDInsight.


### <a name="error-code--2303"></a>Код ошибки:  2303

- **Сообщение**:`Hadoop job failed with exit code '5'. See   'wasbs://adfjobs@xx.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr' for more details. <br/><br/>Hive execution failed with error code 'UserErrorHiveOdbcCommandExecutionFailure'.   See 'wasbs://adfjobs@xx.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out' for more details.`

- **Причина.** Задание было отправлено в HDInsight и завершилось сбоем в HDInsight.

- **Рекомендация**. Задание успешно отправлено в HDInsight. Произошел сбой в кластере. Откройте задание и журналы в пользовательском интерфейсе Ambari HDInsight или откройте файл из хранилища, как было предложено в сообщении об ошибке. В файле отображаются сведения об ошибке.


### <a name="error-code--2310"></a>Код ошибки:  2310

- **Сообщение**:`Hadoop job submission failed. Error: The remote name could not be resolved. <br/><br/>The cluster is not found.`

- **Причина.** Указан недопустимый URI кластера. 

- **Рекомендация**.  Убедитесь, что кластер не удален и предоставлен правильный URI. При открытии URI в браузере должен отобразиться пользовательский интерфейс Ambari. Если кластер находится в виртуальной сети, URI должен быть частным URI. Чтобы открыть его, используйте виртуальную машину, которая является частью той же виртуальной сети. Дополнительные сведения см. [в разделе прямое подключение к службам Apache Hadoop](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services).

<br/>

- **Сообщение**:`502 - Web server received an invalid response while acting as a gateway or proxy server. <br/>Bad gateway.`

- **Причина.** Эта ошибка возникла в HDInsight.

- **Рекомендация**. Эта ошибка связана с кластером HDInsight. Дополнительные сведения см. в разделе [AMBARI UI 502 Error](https://hdinsight.github.io/ambari/ambari-ui-502-error.html), [502 Errors Connect to Spark Thrift Server](https://hdinsight.github.io/spark/spark-thriftserver-errors.html), [502 Errors Connected to Spark Thrift Server](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)и [Устранение ошибок шлюза в шлюзе приложений](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502).

<br/>

- **Сообщение**:`java.lang.NullPointerException`

- **Причина.** Эта ошибка возникает при отправке задания в кластер Spark. 

- **Рекомендация**. Это исключение поступает из HDInsight. Она скрывает фактическую ошибку. Обратитесь в службу поддержки по HDInsight. Укажите имя кластера и диапазон времени выполнения действия.


### <a name="error-code--2347"></a>Код ошибки:  2347

- **Сообщение**:`Hadoop job failed with exit code '5'. See 'wasbs://adfjobs@xx.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr' for more details. <br/><br/>Hive execution failed with error code 'UserErrorHiveOdbcCommandExecutionFailure'.   See 'wasbs://adfjobs@xx.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out' for more details.`

- **Причина.** Задание было отправлено в HDInsight и завершилось сбоем в HDInsight.

- **Рекомендация**. Задание успешно отправлено в HDInsight. Произошел сбой в кластере. Откройте задание и журналы в пользовательском интерфейсе Ambari HDInsight или откройте файл из хранилища, как было предложено в сообщении об ошибке. В файле отображаются сведения об ошибке.


### <a name="error-code--2328"></a>Код ошибки:  2328

- **Сообщение**:`Internal server error occurred while processing the request. Please retry the request or contact support. `

- **Причина.** Эта ошибка возникает в HDInsight по запросу.

- **Рекомендация**. Эта ошибка получена из службы HDInsight при сбое подготовки HDInsight. Обратитесь в службу HDInsight и укажите имя кластера по запросу.



## <a name="web-activity"></a>Веб-действие

### <a name="error-code--2310"></a>Код ошибки:  2310

- **Сообщение**:`Invalid HttpMethod: '...'.`

- **Причина.** Веб-действие не поддерживает метод HTTP, указанный в полезных данных действия.

- **Рекомендация**.  Поддерживаемые методы HTTP: WHERE, POST, GET и DELETE.

<br/>

- **Сообщение**:`Invalid Server Error 500.`

- **Причина.** Внутренняя ошибка в конечной точке.

- **Рекомендация**.  Используйте Fiddler или POST для проверки функциональных возможностей URL-адреса.

<br/>

- **Сообщение**:`Unauthorized 401.`

- **Причина.** Отсутствует допустимая проверка подлинности для запроса.

- **Рекомендация**.  Возможно, истек срок действия маркера. Укажите допустимый метод проверки подлинности. Используйте Fiddler или POST для проверки функциональных возможностей URL-адреса.

<br/>

- **Сообщение**:`Forbidden 403.`

- **Причина.** Отсутствуют необходимые разрешения.

- **Рекомендация**.  Проверьте разрешения пользователя на доступ к ресурсу. Используйте Fiddler или POST для проверки функциональных возможностей URL-адреса.

<br/>

- **Сообщение**:`Bad Request 400.`

- **Причина.** Недопустимый HTTP-запрос.

- **Рекомендация**.   Проверьте URL-адрес, глагол и текст запроса. Используйте Fiddler или POST для проверки запроса.

<br/>

- **Сообщение**:`Not found 404.` 

- **Причина.** Ресурс не найден.   

- **Рекомендация**.  Используйте Fiddler или POST для проверки запроса.

<br/>

- **Сообщение**:`Service unavailable.`

- **Причина.** Служба недоступна.

- **Рекомендация**.  Используйте Fiddler или POST для проверки запроса.

<br/>

- **Сообщение**:`Unsupported Media Type.`

- **Причина.** Тип содержимого не соответствует тексту веб-действия.

- **Рекомендация**.  Укажите тип содержимого, соответствующий формату полезных данных. Используйте Fiddler или POST для проверки запроса.

<br/>

- **Сообщение**:`The resource you are looking for has been removed, has had its name changed, or is temporarily unavailable.`

- **Причина.** Ресурс недоступен. 

- **Рекомендация**.  Чтобы проверить конечную точку, используйте Fiddler или POST.

<br/>

- **Сообщение**:`The page you are looking for cannot be displayed because an invalid method (HTTP verb) is being used.`

- **Причина.** В запросе указан недопустимый метод веб-действия.

- **Рекомендация**.  Чтобы проверить конечную точку, используйте Fiddler или POST.

<br/>

- **Сообщение**:`invalid_payload`

- **Причина.** Неверный текст веб-действия.

- **Рекомендация**.  Чтобы проверить конечную точку, используйте Fiddler или POST.

Чтобы использовать Fiddler для создания сеанса HTTP отслеживаемого веб-приложения, выполните следующие действия.

1. Скачайте, установите и откройте [Fiddler](https://www.telerik.com/download/fiddler).

1. Если веб-приложение использует протокол HTTPS, перейдите в **меню Сервис** > **Fiddler параметры** > **HTTPS**. Выберите **захват HTTPS подключение** и **расшифровка трафика HTTPS**. 
   
   ![Параметры Fiddler](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. Если приложение использует SSL-сертификаты, добавьте сертификат Fiddler на устройство. Последовательно выберите **пункты Сервис** > **Fiddler параметры** > **HTTPS** > действияЭкспорт > корневого**сертификата на Рабочий стол**.

1. Отключите захват, перейдя к**трафику записи** **файлов** > . Или нажмите клавишу **F12**.

1. Очистите кэш браузера, чтобы все кэшированные элементы были удалены и их нужно было снова скачать.

1. Создайте запрос: 

   1\. Перейдите на вкладку **Composer** .

   2\. Задайте метод HTTP и URL-адрес.

   В. При необходимости добавьте заголовки и текст запроса.

   Г. Нажмите кнопку **Выполнить**.

9. Включите повторную запись трафика и завершите проблемную транзакцию на странице.

10. Выберите **файл** > сохранитьвсе > **сеансы**.

Дополнительные сведения см. в статье Приступая к [работе с Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler).

## <a name="next-steps"></a>Следующие шаги

Для получения дополнительных сведений об устранении неполадок воспользуйтесь следующими ресурсами:

*  [Блог фабрики данных](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Запросы функций фабрики данных](https://feedback.azure.com/forums/270578-data-factory)
*  [Видеоролики по Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Форум MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Форум Stack Overflow для фабрики данных](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Сведения о фабрике данных в Twitter](https://twitter.com/hashtag/DataFactory)



