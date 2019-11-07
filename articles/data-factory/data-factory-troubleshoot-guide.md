---
title: Устранение неполадок фабрики данных Azure
description: Узнайте, как устранять неполадки действий внешнего управления в фабрике данных Azure.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: 66590e40ff2440a166310ec3d57026f5a2cdbd1d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73676847"
---
# <a name="troubleshoot-azure-data-factory"></a>Устранение неполадок фабрики данных Azure

В этой статье рассматриваются распространенные методы устранения неполадок для действий внешнего управления в фабрике данных Azure.

## <a name="connector-and-copy-activity"></a>Действие соединителя и копирования

Сведения о проблемах с соединителем, например ошибка при использовании действия копирования, см. в статье [Устранение неполадок соединителей фабрики данных Azure](connector-troubleshoot-guide.md).

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code--3200"></a>Код ошибки: 3200

- **Сообщение**: Ошибка 403.

- **Причина**: `The Databricks access token has expired.`

- **Рекомендация**. по умолчанию маркер доступа Azure Databricks действителен в течение 90 дней. Создайте новый токен и обновите связанную службу.


### <a name="error-code--3201"></a>Код ошибки: 3201

- **Сообщение**: `Missing required field: settings.task.notebook_task.notebook_path.`

- **Причина**: `Bad authoring: Notebook path not specified correctly.`

- **Рекомендация**: укажите путь к записной книжке в действии "кирпичы".

<br/>

- **Сообщение**: `Cluster   ... does not exist.`

- **Причина**: `Authoring error: Databricks cluster does not exist or has been deleted.`

- **Рекомендация**: Убедитесь, что кластер кирпичей существует.

<br/>

- **Сообщение**: `Invalid Python file URI.... Please visit Databricks user guide for supported URI schemes.`

- **Причина**: `Bad authoring.`

- **Рекомендация**: укажите абсолютные пути для схем адресации рабочей области или `dbfs:/folder/subfolder/foo.py` для файлов, хранящихся в файловой системе кирпичей.

<br/>

- **Сообщение**: `{0} LinkedService should have domain and accessToken as required properties.`

- **Причина**: `Bad authoring.`

- **Рекомендация**: Проверьте [определение связанной службы](compute-linked-services.md#azure-databricks-linked-service).

<br/>

- **Сообщение**: `{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **Причина**: `Bad authoring.`

- **Рекомендация**: Проверьте [определение связанной службы](compute-linked-services.md#azure-databricks-linked-service).

<br/>

- **Сообщение**: `Node type Standard_D16S_v3 is not supported. Supported node types:   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3,   Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2,   Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3,   Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2,   Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2,   Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3,   Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s,   Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2,   Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2,   Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3,   Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2,   Standard_L64s_v2, Standard_L80s_v2.`

- **Причина**: `Bad authoring.`

- **Рекомендация**: см. сообщение об ошибке. 

<br/>

### <a name="error-code--3202"></a>Код ошибки: 3202

- **Сообщение**: `There were already 1000 jobs created in past 3600 seconds, exceeding rate limit:   1000 job creations per 3600 seconds.`

- **Причина**: `Too many Databricks runs in an hour.`

- **Рекомендация**. Проверьте все конвейеры, использующие эту рабочую область блоков, для их частоты создания заданий.  Если конвейеры запустили слишком много блоков обработки в статистическом модуле, перенесите некоторые конвейеры в новую рабочую область.

<br/>

- **Сообщение**: `Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **Причина**: `Authoring error: No value provided for the parameter.`

- **Рекомендация**: Проверьте JSON конвейера и убедитесь, что все параметры в записной книжке басепараметерс указывают непустое значение.

<br/>

- **Сообщение**: `User: `Симплеусерконтекст {UserID =..., name =user@company.com, orgId =...}` is not   authorized to access cluster.`

- **Причина**: пользователь, создавший маркер доступа, не может получить доступ к кластеру кирпичей данных, указанному в связанной службе.

- **Рекомендация**: Убедитесь, что пользователь имеет необходимые разрешения в рабочей области.


### <a name="error-code--3203"></a>Код ошибки: 3203

- **Сообщение**: `The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **Причина**: работа кластера была завершена. Для интерактивных кластеров это может быть условие состязания.

- **Рекомендация**. Лучший способ избежать этой ошибки — использовать кластеры заданий.


### <a name="error-code--3204"></a>Код ошибки: 3204

- **Сообщение**: `Job execution failed.`

- **Причина**: сообщения об ошибках указывают на различные проблемы, например непредвиденное состояние кластера или определенное действие. Чаще всего сообщение об ошибке не отображается. 

- **Рекомендация**: н/д


## <a name="azure-data-lake-analytics"></a>Аналитика озера данных Azure

Следующая таблица относится к U-SQL.


### <a name="error-code--2709"></a>Код ошибки: 2709

- **Сообщение**: `The access token is from the wrong tenant.`

- **Причина**: неверный клиент Azure Active Directory (Azure AD).

- **Рекомендация**: неправильный клиент Azure Active Directory (Azure AD).

<br/>

- **Сообщение**: `We cannot accept your job at this moment. The maximum number of queued jobs for   your account is 200. `

- **Причина**: Эта ошибка вызвана регулированием на Data Lake Analytics.

- **Рекомендация**. Сократите число отправленных заданий, чтобы Data Lake Analytics, изменив триггеры фабрики данных и параметры параллелизма для действий. Или увеличьте ограничения на Data Lake Analytics.

<br/>

- **Сообщение**: `This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **Причина**: Эта ошибка вызвана регулированием на Data Lake Analytics. 

- **Рекомендация**. Сократите число отправленных заданий, чтобы Data Lake Analytics, изменив триггеры фабрики данных и параметры параллелизма для действий. Или увеличьте ограничения на Data Lake Analytics.


### <a name="error-code--2705"></a>Код ошибки: 2705

- **Сообщение**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **Причина**: субъекту-службе или сертификату не предоставлен доступ к файлу в хранилище.

- **Рекомендация**: Убедитесь, что субъект-служба или сертификат, предоставляемый пользователем для заданий Data Lake Analytics, имеет доступ к учетной записи Data Lake Analytics и экземпляру Data Lake Storage по умолчанию из корневой папки.


### <a name="error-code--2711"></a>Код ошибки: 2711

- **Сообщение**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **Причина**: субъекту-службе или сертификату не предоставлен доступ к файлу в хранилище.

- **Рекомендация**: Убедитесь, что субъект-служба или сертификат, предоставляемый пользователем для заданий Data Lake Analytics, имеет доступ к учетной записи Data Lake Analytics и экземпляру Data Lake Storage по умолчанию из корневой папки.

<br/>

- **Сообщение**: `Cannot find the 'Azure Data Lake Store' file or folder.`

- **Причина**: неверный путь к файлу U-SQL или учетные данные связанной службы не имеют доступа.

- **Рекомендация**: Проверьте путь и учетные данные, указанные в связанной службе.


### <a name="error-code--2704"></a>Код ошибки: 2704

- **Сообщение**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **Причина**: субъекту-службе или сертификату не предоставлен доступ к файлу в хранилище.

- **Рекомендация**: Убедитесь, что субъект-служба или сертификат, предоставляемый пользователем для заданий Data Lake Analytics, имеет доступ к учетной записи Data Lake Analytics и экземпляру Data Lake Storage по умолчанию из корневой папки.


### <a name="error-code--2707"></a>Код ошибки: 2707

- **Сообщение**: `Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and   'DataLakeAnalyticsUri'.`

- **Причина**: неверная учетная запись Data Lake Analytics в связанной службе.

- **Рекомендация**: Убедитесь, что указана правильная учетная запись.


### <a name="error-code--2703"></a>Код ошибки: 2703

- **Сообщение**: `Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error   Id:").`

- **Причина**: ошибка связана с Data Lake Analytics. 

- **Рекомендация**. ошибка, как в примере, означает, что задание было отправлено в Data Lake Analytics и Скрипт завершился неудачно. Исследование в Data Lake Analytics. На портале перейдите к учетной записи Data Lake Analytics и найдите задание с помощью идентификатора запуска действия фабрики данных (не идентификатора запуска конвейера). Задание содержит дополнительные сведения об ошибке и поможет вам устранить неполадки. Если разрешение неясно, обратитесь в службу поддержки Data Lake Analytics и укажите URL-адрес задания, включающий имя учетной записи и идентификатор задания.



## <a name="azure-functions"></a>Функции Azure

### <a name="error-code--3602"></a>Код ошибки: 3602

- **Сообщение**: `Invalid HttpMethod: {method}.`

- **Причина**: метод HTTP, указанный в полезных данных действия, не поддерживается действием функции Azure. 

- **Рекомендация**. поддерживаются методы HTTP: WHERE, POST, Get, DELETE, параметры, Head и Trace.


### <a name="error-code--3603"></a>Код ошибки: 3603

- **Сообщение**: `Response content is not a valid JObject.`

- **Причина**: функция Azure, которая была вызвана, не вернула полезные данные JSON в ответе. Действие функции Azure в фабрике данных поддерживает только содержимое ответа JSON. 

- **Рекомендация**: обновите функцию Azure, чтобы она возвращала допустимые полезные данные JSON. Например, C# функция может возвращать `(ActionResult)new<OkObjectResult("{`\"ID\":\"123\"`}");`.


### <a name="error-code--3606"></a>Код ошибки: 3606

- **Сообщение**: `Azure function activity missing function key.`

- **Причина**: определение действия функции Azure не завершено. 

- **Рекомендация**: Проверьте, имеет ли определение JSON входного действия азурефунктион свойство с именем "функтионкэй".


### <a name="error-code--3607"></a>Код ошибки: 3607

- **Сообщение**: `Azure function activity missing function name.`

- **Причина**: определение действия функции Azure не завершено. 

- **Рекомендация**: Убедитесь, что определение JSON входного азурефунктион действия имеет свойство с именем FunctionName.


### <a name="error-code--3608"></a>Код ошибки: 3608

- **Сообщение**: `Call to provided Azure function '{FunctionName}' failed with status-'{statusCode}' and message - '{message}'.` 

- **Причина**: сведения о функции Azure в определении действия могут быть неправильными. 

- **Рекомендация**: исправьте сведения о функции Azure и повторите попытку.


### <a name="error-code--3609"></a>Код ошибки: 3609

- **Сообщение**: `Azure function activity missing functionAppUrl.` 

- **Причина**: определение действия функции Azure не завершено. 

- **Рекомендация**: Проверьте, имеет ли определение JSON входного действия азурефунктион свойство с именем "функтионаппурл".


### <a name="error-code--3610"></a>Код ошибки: 3610

- **Сообщение**: `There was an error while calling endpoint.`

- **Причина**: возможно, URL-адрес функции неверен.

- **Рекомендация**: Убедитесь, что значение "функтионаппурл" в JSON действия указано правильно, и повторите попытку.


### <a name="error-code--3611"></a>Код ошибки: 3611

- **Сообщение**: `Azure function activity missing Method in JSON.` 

- **Причина**: определение действия функции Azure не завершено.

- **Рекомендация**: Проверьте, имеет ли определение JSON входного действия азурефунктион свойство с именем "метод".


### <a name="error-code--3612"></a>Код ошибки: 3612

- **Сообщение**: `Azure function activity missing LinkedService definition in JSON.`

- **Причина**: определение действия функции Azure может быть неполным.

- **Рекомендация**: Убедитесь, что в определении JSON входного действия азурефунктион имеются связанные сведения о связанной службе.


## <a name="azure-machine-learning"></a>Машинное обучение Azure


### <a name="error-code--4101"></a>Код ошибки: 4101

- **Сообщение**: `AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **Причина**: неправильный формат или отсутствует определение свойства.

- **Рекомендация**: Убедитесь, что действие определено с правильными данными.


### <a name="error-code--4110"></a>Код ошибки: 4110

- **Сообщение**: в действии азуремлексекутепипелине отсутствует определение LINKEDSERVICE в JSON.

- **Причина**: определение действия азуремлексекутепипелине не завершено.

- **Рекомендация**: Проверьте, содержит ли определение JSON для входного действия азуремлексекутепипелине сведения о связанной службе.


### <a name="error-code--4111"></a>Код ошибки: 4111

- **Сообщение**: `AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **Причина**: неправильное определение действия.

- **Рекомендация**: Проверьте, правильно ли указаны сведения о связанной службе в определении JSON входного азуремлексекутепипелине действия.


### <a name="error-code--4112"></a>Код ошибки: 4112

- **Сообщение**: `AzureMLService linked service has invalid value for property '%propertyName;'.`

- **Причина**: неправильный формат или отсутствует определение свойства.

- **Рекомендация**: Проверьте, содержит ли определение связанной службы правильные данные.


### <a name="error-code--4121"></a>Код ошибки: 4121

- **Сообщение**: `Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Причина**: истек срок действия учетных данных, используемых для доступа к службе машинного обучения Azure.

- **Рекомендация**: Проверьте правильность учетных данных и повторите попытку


### <a name="error-code--4122"></a>Код ошибки: 4122

- **Сообщение**: `Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Причина**: учетные данные, указанные в связанной службе службы AzureML, недопустимы или не имеют разрешения на выполнение этой операции.

- **Рекомендация**: Проверьте правильность учетных данных в связанной службе и наличие разрешений на доступ к службе AzureML.


### <a name="error-code--4123"></a>Код ошибки: 4123

- **Сообщение**: `Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Причина**: `Properties of the activity such as pipelineParamters are invalid for the Azure ML pipeline.`

- **Рекомендация**: проверьте значение свойств действия в соответствии с ожидаемыми полезными данными из опубликованного КОНВЕЙЕРа машинного обучения Azure, указанного в связанной службе.


### <a name="error-code--4124"></a>Код ошибки: 4124

- **Сообщение**: `Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Причина**: опубликованная конечная точка КОНВЕЙЕРа машинного обучения Azure не существует.

- **Рекомендация**: Убедитесь, что опубликованная конечная точка КОНВЕЙЕРа машинного обучения Azure, указанная в связанной службе, существует в службе машинного обучения Azure.


### <a name="error-code--4125"></a>Код ошибки: 4125

- **Сообщение**: `Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Причина**: ошибка сервера в службе машинного обучения Azure.

- **Рекомендация**: повторите попытку позже. Обратитесь в службу поддержки машинного обучения Azure, чтобы получить помощь в случае ее возникновения.


### <a name="error-code--4126"></a>Код ошибки: 4126

- **Сообщение**: `AzureML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in AzureMLService for more error loggings.`

- **Причина**: сбой выполнения конвейера AzureML.

- **Рекомендация**: Проверьте азуремлсервице для получения дополнительных сведений об ошибках и исправьте конвейер ml.


## <a name="custom"></a>Пользовательская

Следующая таблица относится к пакетной службе Azure.


### <a name="error-code--2500"></a>Код ошибки: 2500

- **Сообщение**: `Hit unexpected exception and execution failed.`

- **Причина**: `Can't launch command, or the program returned an error code.`

- **Рекомендация**: Убедитесь, что исполняемый файл существует. Если программа запущена, убедитесь, что *stdout. txt* и *stderr. txt* были переданы в учетную запись хранения. Рекомендуется создавать в коде журналы копиаус для отладки.


### <a name="error-code--2501"></a>Код ошибки: 2501

- **Сообщение**: `Cannot access user batch account; please check batch account settings.`

- **Причина**: неверный ключ доступа пакетной службы или имя пула.

- **Рекомендация**: Проверьте имя пула и ключ доступа пакетной службы в связанной службе.


### <a name="error-code--2502"></a>Код ошибки: 2502

- **Сообщение**: `Cannot access user storage account; please check storage account settings.`

- **Причина**: неправильное имя учетной записи хранения или ключ доступа.

- **Рекомендация**: Проверьте имя учетной записи хранения и ключ доступа в связанной службе.


### <a name="error-code--2504"></a>Код ошибки: 2504

- **Сообщение**: `Operation returned an invalid status code 'BadRequest'.` 

- **Причина**: слишком много файлов в FolderPath настраиваемого действия. Общий размер resourceFiles не может превышать 32 768 символов.

- **Рекомендация**: удалите ненужные файлы. Или ZIP, и добавьте команду unzip, чтобы извлечь их. Например, используйте `powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;  $folder\yourProgram.exe`


### <a name="error-code--2505"></a>Код ошибки: 2505

- **Сообщение**: `Cannot create Shared Access Signature unless Account Key credentials are used.`

- **Причина**. пользовательские действия поддерживают только учетные записи хранения, использующие ключ доступа.

- **Рекомендация**: см. описание ошибки.


### <a name="error-code--2507"></a>Код ошибки: 2507

- **Сообщение**: `The folder path does not exist or is empty: ....`

- **Причина**: в учетной записи хранения нет файлов по указанному пути.

- **Рекомендация**: путь к папке должен содержать исполняемые файлы, которые требуется запустить.


### <a name="error-code--2508"></a>Код ошибки: 2508

- **Сообщение**: `There are duplicate files in the resource folder.`

- **Причина**: несколько файлов с одинаковыми именами находятся в разных вложенных папках FolderPath.

- **Рекомендация**: структура папки с пользовательскими действиями в разделе FolderPath.  Если необходимо сохранить структуру папок, ZIP-файлы и извлечь их в пакетной службе Azure с помощью команды unzip. Например, используйте `powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;   $folder\yourProgram.exe`


### <a name="error-code--2509"></a>Код ошибки: 2509

- **Сообщение**: `Batch   url ... is invalid; it must be in Uri format.` 

- **Причина**: URL-адреса пакетов должны быть похожи на `https://mybatchaccount.eastus.batch.azure.com`

- **Рекомендация**: см. описание ошибки.


### <a name="error-code--2510"></a>Код ошибки: 2510

- **Сообщение**: `An   error occurred while sending the request.`

- **Причина**: недопустимый URL-адрес пакета. 

- **Рекомендация**: Проверьте URL-адрес пакета.


## <a name="hdinsight"></a>HDInsight

Следующая таблица применяется к потоковой передаче Spark, Hive, MapReduce, Pig и Hadoop.


### <a name="error-code--2300"></a>Код ошибки: 2300

- **Сообщение**: `Hadoop job submission failed. Error: The remote name could not be resolved. <br/><br/>The cluster is not found.`

- **Причина**: указанный URI кластера недопустим. 

- **Рекомендация**: Убедитесь, что кластер не был удален и предоставлен правильный универсальный код ресурса (URI). При открытии URI в браузере должен отобразиться пользовательский интерфейс Ambari. Если кластер находится в виртуальной сети, URI должен быть частным URI. Чтобы открыть его, используйте виртуальную машину, которая является частью той же виртуальной сети. Дополнительные сведения см. [в разделе прямое подключение к службам Apache Hadoop](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services).

<br/>

- **Сообщение**: `Hadoop job submission failed. Job: …, Cluster: …/. Error: A task was canceled.`

- **Причина**: время ожидания отправки задания истекло. 

- **Рекомендация**. проблема может быть как общим подключением HDInsight, так и сетевым подключением. Сначала убедитесь, что пользовательский интерфейс HDInsight Ambari доступен из любого браузера. Убедитесь, что учетные данные все еще действительны. Если вы используете локальную среду выполнения (IR), убедитесь, что это делается с виртуальной машины или с компьютера, на котором установлено локальное IR-соединение. Затем попробуйте отправить задание из фабрики данных еще раз. Если по-прежнему происходит сбой, обратитесь за поддержкой к группе фабрики данных.


- **Сообщение**: `Unauthorized: Ambari user name or password is incorrect  <br/><br/>Unauthorized: User admin is locked out in Ambari.   <br/><br/>403 - Forbidden: Access is denied.`

- **Причина**: учетные данные HDInsight неверны или истек срок их действия.

- **Рекомендация**: исправьте учетные данные и повторно разверните связанную службу. Сначала убедитесь, что учетные данные работают в HDInsight, открыв универсальный код ресурса (URI) кластера в любом браузере и пытаясь войти в систему. Если учетные данные не работают, их можно сбросить с портал Azure.

<br/>

- **Сообщение**: `502 - Web server received an invalid response while acting as a gateway or proxy server. <br/>Bad gateway.`

- **Причина**: Эта ошибка возникла в HDInsight.

- **Рекомендация**. Эта ошибка связана с кластером HDInsight. Дополнительные сведения см. в разделе [AMBARI UI 502 Error](https://hdinsight.github.io/ambari/ambari-ui-502-error.html), [502 Errors Connect to Spark Thrift Server](https://hdinsight.github.io/spark/spark-thriftserver-errors.html), [502 Errors Connected to Spark Thrift Server](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)и [Устранение ошибок шлюза в шлюзе приложений](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502).

<br/>

- **Сообщение**: `Hadoop job submission failed. Job: …, Cluster: ... Error:   {\"error\":\"Unable to service the submit job request as   templeton service is busy with too many submit job requests. Please wait for some time before retrying the operation. Please refer to the config   templeton.parallellism.job.submit to configure concurrent requests. <br/><br/>Hadoop job submission failed. Job: xx, Cluster: name.   Error: {\"error\":\"java.io.IOException:   org.apache.hadoop.yarn.exceptions.YarnException: Failed to submit   application_1561147195099_3730 to YARN :   org.apache.hadoop.security.AccessControlException: Queue root.joblauncher already has 500 applications, cannot accept submission of application:   application_1561147195099_3730\`

- **Причина**: слишком много заданий отправляются в HDInsight одновременно.

- **Рекомендация**. Попробуйте ограничить количество параллельных заданий, отправляемых в HDInsight. См. сведения о параллелизме действий фабрики данных, если задания отправляются одним действием. Измените триггеры, чтобы параллельные запуски конвейера были распределены по времени. Сведения об изменении `templeton.parallellism.job.submit` см. в документации по HDInsight.


### <a name="error-code--2303"></a>Код ошибки: 2303

- **Сообщение**: `Hadoop job failed with exit code '5'. See   'wasbs://adfjobs@xx.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr' for more details. <br/><br/>Hive execution failed with error code 'UserErrorHiveOdbcCommandExecutionFailure'.   See 'wasbs://adfjobs@xx.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out' for more details.`

- **Причина**: задание было отправлено в hdinsight и завершилось сбоем в hdinsight.

- **Рекомендация**: задание успешно отправлено в HDInsight. Произошел сбой в кластере. Откройте задание и журналы в пользовательском интерфейсе Ambari HDInsight или откройте файл из хранилища, как было предложено в сообщении об ошибке. В файле отображаются сведения об ошибке.


### <a name="error-code--2310"></a>Код ошибки: 2310

- **Сообщение**: `Hadoop job submission failed. Error: The remote name could not be resolved. <br/><br/>The cluster is not found.`

- **Причина**: указанный URI кластера недопустим. 

- **Рекомендация**: Убедитесь, что кластер не был удален и предоставлен правильный универсальный код ресурса (URI). При открытии URI в браузере должен отобразиться пользовательский интерфейс Ambari. Если кластер находится в виртуальной сети, URI должен быть частным URI. Чтобы открыть его, используйте виртуальную машину, которая является частью той же виртуальной сети. Дополнительные сведения см. [в разделе прямое подключение к службам Apache Hadoop](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services).

<br/>

- **Сообщение**: `502 - Web server received an invalid response while acting as a gateway or proxy server. <br/>Bad gateway.`

- **Причина**: Эта ошибка возникла в HDInsight.

- **Рекомендация**. Эта ошибка связана с кластером HDInsight. Дополнительные сведения см. в разделе [AMBARI UI 502 Error](https://hdinsight.github.io/ambari/ambari-ui-502-error.html), [502 Errors Connect to Spark Thrift Server](https://hdinsight.github.io/spark/spark-thriftserver-errors.html), [502 Errors Connected to Spark Thrift Server](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)и [Устранение ошибок шлюза в шлюзе приложений](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502).

<br/>

- **Сообщение**: `java.lang.NullPointerException`

- **Причина**: Эта ошибка возникает при отправке задания в кластер Spark. 

- **Рекомендация**. это исключение поступает из HDInsight. Она скрывает фактическую ошибку. Обратитесь в службу поддержки по HDInsight. Укажите имя кластера и диапазон времени выполнения действия.


### <a name="error-code--2347"></a>Код ошибки: 2347

- **Сообщение**: `Hadoop job failed with exit code '5'. See 'wasbs://adfjobs@xx.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr' for more details. <br/><br/>Hive execution failed with error code 'UserErrorHiveOdbcCommandExecutionFailure'.   See 'wasbs://adfjobs@xx.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out' for more details.`

- **Причина**: задание было отправлено в hdinsight и завершилось сбоем в hdinsight.

- **Рекомендация**: задание успешно отправлено в HDInsight. Произошел сбой в кластере. Откройте задание и журналы в пользовательском интерфейсе Ambari HDInsight или откройте файл из хранилища, как было предложено в сообщении об ошибке. В файле отображаются сведения об ошибке.


### <a name="error-code--2328"></a>Код ошибки: 2328

- **Сообщение**: `Internal server error occurred while processing the request. Please retry the request or contact support. `

- **Причина**: Эта ошибка возникает в HDInsight по запросу.

- **Рекомендация**. Эта ошибка получена из службы HDInsight при сбое подготовки HDInsight. Обратитесь в службу HDInsight и укажите имя кластера по запросу.



## <a name="web-activity"></a>Веб-действие

### <a name="error-code--2108"></a>Код ошибки: 2108

- **Сообщение**: `Invalid HttpMethod: '...'.`

- **Причина**: веб-действие не поддерживает метод HTTP, указанный в полезных данных действия.

- **Рекомендация**. ПОДДЕРЖИВАЕМЫЕ методы HTTP: WHERE, POST, Get и DELETE.

<br/>

- **Сообщение**: `Invalid Server Error 500.`

- **Причина**: Внутренняя ошибка в конечной точке.

- **Рекомендация**: используйте Fiddler или POST, чтобы проверить функциональность URL-адреса.

<br/>

- **Сообщение**: `Unauthorized 401.`

- **Причина**: отсутствует допустимая проверка подлинности для запроса.

- **Рекомендация**: возможно, истек срок действия маркера. Укажите допустимый метод проверки подлинности. Используйте Fiddler или POST для проверки функциональных возможностей URL-адреса.

<br/>

- **Сообщение**: `Forbidden 403.`

- **Причина**: отсутствуют необходимые разрешения.

- **Рекомендация**: Проверьте разрешения пользователя на доступ к ресурсу. Используйте Fiddler или POST для проверки функциональных возможностей URL-адреса.

<br/>

- **Сообщение**: `Bad Request 400.`

- **Причина**: недопустимый HTTP-запрос.

- **Рекомендация**: Проверьте URL-адрес, глагол и текст запроса. Используйте Fiddler или POST для проверки запроса.

<br/>

- **Сообщение**: `Not found 404.` 

- **Причина**: ресурс не найден.   

- **Рекомендация**: для проверки запроса используйте Fiddler или POST.

<br/>

- **Сообщение**: `Service unavailable.`

- **Причина**: служба недоступна.

- **Рекомендация**: для проверки запроса используйте Fiddler или POST.

<br/>

- **Сообщение**: `Unsupported Media Type.`

- **Причина**: тип содержимого не соответствует тексту веб-действия.

- **Рекомендация**: Укажите тип содержимого, соответствующий формату полезных данных. Используйте Fiddler или POST для проверки запроса.

<br/>

- **Сообщение**: `The resource you are looking for has been removed, has had its name changed, or is temporarily unavailable.`

- **Причина**: ресурс недоступен. 

- **Рекомендация**: используйте Fiddler или POST для проверки конечной точки.

<br/>

- **Сообщение**: `The page you are looking for cannot be displayed because an invalid method (HTTP verb) is being used.`

- **Причина**: в запросе был указан неверный метод веб-действия.

- **Рекомендация**: используйте Fiddler или POST для проверки конечной точки.

<br/>

- **Сообщение**: `invalid_payload`

- **Причина**: неверный текст веб-действия.

- **Рекомендация**: используйте Fiddler или POST для проверки конечной точки.


### <a name="error-code--2208"></a>Код ошибки: 2208

- **Сообщение**: `Invoking Web Activity failed with HttpStatusCode - {0}.`

- **Причина**: Целевая служба вернула состояние сбоя.

- **Рекомендация**: для проверки запроса используйте Fiddler/POST.


### <a name="error-code--2308"></a>Код ошибки: 2308

- **Сообщение**: `No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Причина**. возможны несколько причин этой ошибки, например сетевое подключение, сбой DNS, проверка сертификата сервера или превышение времени ожидания.

- **Рекомендация**: для проверки запроса используйте Fiddler/POST.


Чтобы использовать Fiddler для создания сеанса HTTP отслеживаемого веб-приложения, выполните следующие действия.

1. Скачайте, установите и откройте [Fiddler](https://www.telerik.com/download/fiddler).

1. Если веб-приложение использует протокол HTTPS, перейдите в **меню сервис** > **Параметры Fiddler** > **HTTPS**. Выберите **захват HTTPS подключение** и **расшифровка трафика HTTPS**. 
   
   ![Параметры Fiddler](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. Если приложение использует SSL-сертификаты, добавьте сертификат Fiddler на устройство. Последовательно выберите **пункты сервис** > **параметры Fiddler** >  > **действия** **HTTPS** > **экспортировать корневой сертификат на Рабочий стол**.

1. Отключите захват, перейдя в **файловый** > **записывать трафик**. Или нажмите клавишу **F12**.

1. Очистите кэш браузера, чтобы все кэшированные элементы были удалены и их нужно было снова скачать.

1. Создайте запрос: 

   а. Перейдите на вкладку **Composer** .

   b. Задайте метод HTTP и URL-адрес.

   c. При необходимости добавьте заголовки и текст запроса.

   г) Нажмите кнопку **Execute**(Выполнить).

9. Включите повторную запись трафика и завершите проблемную транзакцию на странице.

10. Перейдите в **файл** > **сохранить** > **все сеансы**.

Дополнительные сведения см. в статье [Приступая к работе с Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler).

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительных сведений об устранении неполадок воспользуйтесь следующими ресурсами:

*  [Блог, посвященный службе "Фабрика данных"](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Запросы функций фабрики данных](https://feedback.azure.com/forums/270578-data-factory)
*  [Видеоролики по Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Форум MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Форум Stack Overflow для фабрики данных](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Сведения о фабрике данных в Twitter](https://twitter.com/hashtag/DataFactory)



