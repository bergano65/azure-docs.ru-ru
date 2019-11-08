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
ms.openlocfilehash: 0026aa377a58f6b766a400860692a35440deb962
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748362"
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

- **Сообщение**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **Причина**: субъекту-службе или сертификату не предоставлен доступ к файлу в хранилище.

- **Рекомендация**: Убедитесь, что субъект-служба или сертификат, предоставляемый пользователем для заданий Data Lake Analytics, имеет доступ к учетной записи Data Lake Analytics и экземпляру Data Lake Storage по умолчанию из корневой папки.


### <a name="error-code--2711"></a>Код ошибки: 2711

- **Сообщение**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **Причина**: субъекту-службе или сертификату не предоставлен доступ к файлу в хранилище.

- **Рекомендация**: Убедитесь, что субъект-служба или сертификат, предоставляемый пользователем для заданий Data Lake Analytics, имеет доступ к учетной записи Data Lake Analytics и экземпляру Data Lake Storage по умолчанию из корневой папки.

<br/>  

- **Сообщение**: `Cannot find the 'Azure Data Lake Store' file or folder.`

- **Причина**: неверный путь к файлу U-SQL или учетные данные связанной службы не имеют доступа.

- **Рекомендация**: Проверьте путь и учетные данные, указанные в связанной службе.


### <a name="error-code--2704"></a>Код ошибки: 2704

- **Сообщение**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

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

- **Сообщение**: `Invalid HttpMethod: '%method;'.`

- **Причина**: метод HTTP, указанный в полезных данных действия, не поддерживается действием функции Azure.

- **Рекомендация**. поддерживаются методы HTTP: WHERE, POST, Get, DELETE, параметры, Head и Trace.


### <a name="error-code--3603"></a>Код ошибки: 3603

- **Сообщение**: `Response Content is not a valid JObject.`

- **Причина**: функция Azure, которая была вызвана, не вернула полезные данные JSON в ответе. Действие функции Azure ADF поддерживает только содержимое ответа JSON.

- **Рекомендация**: обновите функцию Azure, чтобы вернуть допустимые полезные данные JSON C# , например, функция может возвращать (ActionResult) New окобжектресулт ("{\"Id\":\"123\"}");


### <a name="error-code--3606"></a>Код ошибки: 3606

- **Сообщение**: в действии функции Azure отсутствует ключ функции.

- **Причина**: определение действия функции Azure не завершено.

- **Рекомендация**: Проверьте, имеет ли определение JSON входного действия азурефунктион свойство с именем "функтионкэй".


### <a name="error-code--3607"></a>Код ошибки: 3607

- **Сообщение**: `Azure function activity missing function name.`

- **Причина**: определение действия функции Azure не завершено.

- **Рекомендация**: Убедитесь, что определение JSON входного азурефунктион действия имеет свойство с именем FunctionName.


### <a name="error-code--3608"></a>Код ошибки: 3608

- **Сообщение**: `Call to provided Azure function '%FunctionName;' failed with status-'%statusCode;' and message - '%message;'.`

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

- **Рекомендация**: Убедитесь, что определение JSON входного действия азурефунктион имеет свойство с именем "метод".


### <a name="error-code--3612"></a>Код ошибки: 3612

- **Сообщение**: `Azure function activity missing LinkedService definition in JSON.`

- **Причина**: определение действия функции Azure не завершено.

- **Рекомендация**: Убедитесь, что в определении JSON входного действия азурефунктион имеются связанные сведения о связанной службе.



## <a name="azure-machine-learning"></a>Машинное обучение Azure

### <a name="error-code--4101"></a>Код ошибки: 4101

- **Сообщение**: `AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **Причина**: неправильный формат или отсутствует определение свойства "% PropertyName;".

- **Рекомендация**: Проверьте, имеет ли действие "% activityName;" свойство "% PropertyName;", определенное с правильными данными.


### <a name="error-code--4110"></a>Код ошибки: 4110

- **Сообщение**: `AzureMLExecutePipeline activity missing LinkedService definition in JSON.`

- **Причина**: определение действия азуремлексекутепипелине не завершено.

- **Рекомендация**: Проверьте, содержит ли определение JSON для входного действия азуремлексекутепипелине сведения о связанной службе.


### <a name="error-code--4111"></a>Код ошибки: 4111

- **Сообщение**: `AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **Причина**: неправильное определение действия.

- **Рекомендация**: Проверьте, правильно ли указаны сведения о связанной службе в определении JSON входного азуремлексекутепипелине действия.


### <a name="error-code--4112"></a>Код ошибки: 4112

- **Сообщение**: `AzureMLService linked service has invalid value for property '%propertyName;'.`

- **Причина**: неправильный формат или отсутствует определение свойства "% PropertyName;".

- **Рекомендация**: Проверьте, имеет ли связанная служба свойство "% PropertyName;", определенное с правильными данными.


### <a name="error-code--4121"></a>Код ошибки: 4121

- **Сообщение**: `Request sent to Azure ML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure ML Service: '%externalMessage;'.`

- **Причина**: истек срок действия учетных данных, используемых для доступа к службе машинного обучения Azure.

- **Рекомендация**: Проверьте правильность учетных данных и повторите попытку


### <a name="error-code--4122"></a>Код ошибки: 4122

- **Сообщение**: `Request sent to Azure ML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure ML Service: '%externalMessage;'.`

- **Причина**: учетные данные, указанные в связанной службе службы машинного обучения Azure, недопустимы или не имеют разрешения на выполнение операции.

- **Рекомендация**: Убедитесь, что учетные данные в связанной службе действительны и имеют разрешение на доступ к службе машинного обучения Azure.


### <a name="error-code--4123"></a>Код ошибки: 4123

- **Сообщение**: `Request sent to Azure ML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure ML Service: '%externalMessage;'.`

- **Причина**: `Properties of the activity such as pipelineParamters are invalid for the Azure ML pipeline.`

- **Рекомендация**: проверьте значение свойств действия в соответствии с ожидаемыми полезными данными из опубликованного КОНВЕЙЕРа машинного обучения Azure, указанного в связанной службе.


### <a name="error-code--4124"></a>Код ошибки: 4124

- **Сообщение**: `Request sent to Azure ML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure ML Service: '%externalMessage;'.`

- **Причина**: опубликованная конечная точка КОНВЕЙЕРа машинного обучения Azure не существует.

- **Рекомендация**: Убедитесь, что опубликованная конечная точка КОНВЕЙЕРа машинного обучения Azure, указанная в связанной службе, существует в службе машинного обучения Azure.


### <a name="error-code--4125"></a>Код ошибки: 4125

- **Сообщение**: `Request sent to Azure ML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure ML Service: '%externalMessage;'.`

- **Причина**: ошибка сервера в службе машинного обучения Azure.

- **Рекомендация**: повторите попытку позже. Обратитесь в службу поддержки машинного обучения Azure, чтобы получить помощь в случае ее возникновения.


### <a name="error-code--4126"></a>Код ошибки: 4126

- **Сообщение**: `Azure ML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in Azure ML Service for more error logs.`

- **Причина**: сбой запуска КОНВЕЙЕРа машинного обучения Azure.

- **Рекомендация**: проверьте наличие дополнительных журналов ошибок в службе машинного обучения Azure и исправьте конвейер ml.



## <a name="common"></a>Common

### <a name="error-code--2103"></a>Код ошибки: 2103

- **Сообщение**: `Please provide value for the required property '%propertyName;'.`

- **Причина**: значение свойства не было предоставлено, однако оно требуется в сценарии.

- **Рекомендация**: Укажите значение из сообщения и повторите попытку.


### <a name="error-code--2104"></a>Код ошибки: 2104

- **Сообщение**: `The type of the property '%propertyName;' is incorrect.`

- **Причина**: тип предоставленного свойства не соответствует ожидаемому.

- **Рекомендация**: Исправьте тип свойства и повторите попытку.


### <a name="error-code--2105"></a>Код ошибки: 2105

- **Сообщение**: `An invalid json is provided for property '%propertyName;'. Encountered an error while trying to parse: '%message;'.`

- **Причина**: значение свойства недопустимо или не имеет ожидаемого формата.

- **Рекомендация**: выполните поиск в документации по свойству и убедитесь, что указанное значение имеет ожидаемый формат и тип.


### <a name="error-code--2106"></a>Код ошибки: 2106

- **Сообщение**: `The storage connection string is invalid. %errorMessage;`

- **Причина**: строка подключения для хранилища недопустима или имеет неправильный формат.

- **Рекомендация**: перейдите на портал Azure, найдите хранилище, скопируйте строку подключения и вставьте в нее связанную службу и повторите попытку.


### <a name="error-code--2108"></a>Код ошибки: 2108

- **Сообщение**: `Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Причина**: не удалось выполнить запрос из-за базовой проблемы, такой как сетевое подключение, сбой DNS, проверка сертификата сервера или превышение времени ожидания.

- **Рекомендация**: для проверки запроса используйте Fiddler/POST.


### <a name="error-code--2110"></a>Код ошибки: 2110

- **Сообщение**: `The linked service type '%linkedServiceType;' is not supported for '%executorType;' activities.`

- **Причина**: связанная служба, указанная в действии, неверна.

- **Рекомендация**. Убедитесь, что тип связанной службы — один из поддерживаемых типов для действия. Например, для действий HDI тип связанной службы может быть HDInsight или HDInsightOnDemand.


### <a name="error-code--2111"></a>Код ошибки: 2111

- **Сообщение**: `The type of the property '%propertyName;' is incorrect. The expected type is %expectedType;.`

- **Причина**: тип предоставленного свойства не соответствует ожидаемому.

- **Рекомендация**: Исправьте тип свойства и повторите попытку.


### <a name="error-code--2112"></a>Код ошибки: 2112

- **Сообщение**: `The cloud type is unsupported or could not be determined for storage from the EndpointSuffix '%endpointSuffix;'.`

- **Причина**: тип облака не поддерживается или не может быть определен для хранилища из EndpointSuffix.

- **Рекомендация**. Используйте хранилище в другом облаке и повторите попытку.


### <a name="error-code--2128"></a>Код ошибки: 2128

- **Сообщение**: `No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Причина**: сетевое подключение, сбой DNS, проверка сертификата сервера или превышение времени ожидания.

- **Рекомендация**: Убедитесь, что конечная точка, которую вы пытаетесь навести, отвечает на запросы. Вы можете использовать такие средства, как Fiddler/POST.



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

### <a name="error-code--200"></a>Код ошибки: 200

- **Сообщение**: `Unexpected error happened: '%error;'.`

- **Причина**: Внутренняя ошибка службы.

- **Рекомендация**: обратитесь в службу поддержки ADF для получения дополнительной помощи.


### <a name="error-code--201"></a>Код ошибки: 201

- **Сообщение**: `JobType %jobType; is not found.`

- **Причина**. Существует новый тип задания, который не поддерживается в ADF.

- **Рекомендация**: обратитесь в службу поддержки ADF для получения дополнительной помощи.


### <a name="error-code--202"></a>Код ошибки: 202

- **Сообщение**: `Failed to create on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **Причина**: в сообщении об ошибке должны отображаться сведения о том, что пошло не так.

- **Рекомендация**: сообщение об ошибке должно помочь устранить проблему. Если недостаточно данных, обратитесь за помощью в службу поддержки ADF.


### <a name="error-code--203"></a>Код ошибки: 203

- **Сообщение**: `Failed to delete on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **Причина**: в сообщении об ошибке должны отображаться сведения о том, что пошло не так.

- **Рекомендация**: сообщение об ошибке должно помочь устранить проблему. Если недостаточно данных, обратитесь за помощью в службу поддержки ADF.


### <a name="error-code--204"></a>Код ошибки: 204

- **Сообщение**: `The resumption token is missing for runId '%runId;'.`

- **Причина**: Внутренняя ошибка службы.

- **Рекомендация**: обратитесь в службу поддержки ADF для получения дополнительной помощи.


### <a name="error-code--205"></a>Код ошибки: 205

- **Сообщение**: `Failed to prepare cluster for LinkedService '%linkedServiceName;', the current resource status is '%status;'.`

- **Причина**: при создании кластера HDi по запросу произошла ошибка.

- **Рекомендация**: обратитесь в службу поддержки ADF для получения дополнительной помощи.


### <a name="error-code--206"></a>Код ошибки: 206

- **Сообщение**: `The batch ID for Spark job is invalid. Please retry your job, and if the problem persists, contact the ADF support for further assistance.`

- **Причина**: произошла внутренняя проблема со службой, которая вызвала это.

- **Рекомендация**. это может быть временной проблемой. Повторите задание. Если проблема не исчезнет, обратитесь за помощью в службу поддержки ADF.


### <a name="error-code--207"></a>Код ошибки: 207

- **Сообщение**: `Could not determine the region from the provided storage account. Please try using another primary storage account for the on demand HDI or contact ADF support team and provide the activity run ID.`

- **Причина**: произошла внутренняя ошибка при попытке определить регион из основной учетной записи хранения.

- **Рекомендация**: попробуйте использовать другое хранилище. Если это решение не является приемлемым, обратитесь в службу поддержки ADF для получения дополнительной помощи.


### <a name="error-code--208"></a>Код ошибки: 208

- **Сообщение**: `Service Principal or the MSI authenticator are not instantiated. Please consider providing a Service Principal in the HDI on demand linked service which has permissions to create an HDInsight cluster in the provided subscription and try again. In case if this is not an acceptable solution, contact ADF support team for further assistance.`

- **Причина**: произошла внутренняя ошибка при попытке чтения субъекта-службы или создания экземпляра проверки подлинности MSI.

- **Рекомендация**. Попробуйте предоставить субъект-службу с разрешениями на создание кластера HDInsight в указанной подписке и повторите попытку. Если это решение не является приемлемым, обратитесь в службу поддержки ADF для получения дополнительной помощи.


### <a name="error-code--2300"></a>Код ошибки: 2300

- **Сообщение**: `Failed to submit the job '%jobId;' to the cluster '%cluster;'. Error: %errorMessage;.`

<br>

- **Причина**: Если сообщение об ошибке содержит сообщение, аналогичное "удаленное имя не удалось разрешить", это может означать, что указанный URI кластера недопустим.


- **Рекомендация**: Убедитесь, что кластер не был удален и предоставлен правильный универсальный код ресурса (URI). При открытии URI в браузере должен отобразиться пользовательский интерфейс Ambari. Если кластер находится в виртуальной сети, URI должен быть частным URI. Чтобы открыть его, используйте виртуальную машину, которая является частью той же виртуальной сети. Дополнительные сведения см. [здесь](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#directly-connect-to-apache-hadoop-services).
                  

<br>

- **Причина**: Если сообщение об ошибке содержит сообщение, аналогичное "отменена задача", это означает, что время ожидания отправки задания истекло.

- **Рекомендация**. проблема может быть как общим подключением HDInsight, так и сетевым подключением. Сначала убедитесь, что пользовательский интерфейс HDInsight Ambari доступен из любого браузера. Убедитесь, что учетные данные все еще действительны. Если вы используете локальную среду выполнения (IR), убедитесь, что это делается с виртуальной машины или с компьютера, на котором установлено локальное IR-соединение. Затем попробуйте отправить задание из фабрики данных еще раз. Если по-прежнему происходит сбой, обратитесь за поддержкой к группе фабрики данных.

<br>

- **Причина**: Если сообщение об ошибке содержит сообщение о том, что "администратор пользователя заблокирован в Ambari" или "не авторизовано: Ambari имя пользователя или пароль неверен", это означает, что учетные данные HDInsight неправильные или истек срок их действия.

- **Рекомендация**: исправьте учетные данные и повторно разверните связанную службу. Сначала убедитесь, что учетные данные работают в HDInsight, открыв универсальный код ресурса (URI) кластера в любом браузере и пытаясь войти в систему. Если учетные данные не работают, их можно сбросить с портал Azure.

<br>

- **Причина**: Если сообщение об ошибке содержит сообщение, похожее на "502 — веб-сервер получил недопустимый ответ при работе в качестве шлюза или прокси-сервера", служба HDInsight возвращает эту ошибку.


- **Рекомендации**. Просмотрите документацию по устранению неполадок Azure HDInsight, например https://hdinsight.github.io/ambari/ambari-ui-502-error.html, https://hdinsight.github.io/spark/spark-thriftserver-errors.htmlhttps://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502.
                  

<br>

- **Причина**: Если сообщение об ошибке содержит сообщение, похожее на "не удалось выполнить обслуживание запроса на отправку, так как служба Templeton занята слишком большим количеством запросов на задание отправки" или "Корневая папка очереди. joblauncher уже содержит приложения 500, не может принимать отправку Приложение ", это означает, что в HDInsight одновременно отправляется слишком много заданий.

- **Рекомендация**. Попробуйте ограничить количество параллельных заданий, отправляемых в HDInsight. См. сведения о параллелизме действий фабрики данных, если задания отправляются одним действием. Измените триггеры, чтобы параллельные запуски конвейера были распределены по времени. Сведения об ошибке см. в документации по HDInsight для настройки Templeton. параллеллисм. job. Submit.


### <a name="error-code--2301"></a>Код ошибки: 2301

- **Сообщение**: `Could not get the status of the application '%physicalJobId;' from the HDInsight service. Received the following error: %message;. Please refer to HDInsight troubleshooting documentation or contact their support for further assistance.`

- **Причина**: в кластере HDInsight или службе возникли проблемы.


- **Рекомендация**. Эта ошибка возникает, когда ADF не получает ответ от кластера HDInsight при попытке получить состояние выполняющегося задания. Это может быть вызвано проблемами в самом кластере или в случае сбоя службы HDInsight. Обратитесь к документации по устранению неполадок HDInsight по адресу https://docs.microsoft.com/azure/hdinsight/hdinsight-troubleshoot-guideили обратитесь в службу поддержки за дальнейшей помощью.
                


### <a name="error-code--2302"></a>Код ошибки: 2302

- **Сообщение**: `Hadoop job failed with exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Contact HDInsight team for further support.`

- **Причина**: задание было отправлено в кластер HDi и завершилось с ошибкой.

- **Рекомендация**. Перейдите по ссылке "журналы Yarn" в выходных данных выполнения действия и найдите ошибки в выходных данных HDi. При необходимости обратитесь в службу HDInsight.


### <a name="error-code--2303"></a>Код ошибки: 2303

- **Сообщение**: `Hadoop job failed with transient exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Try again or contact HDInsight team for further support.`

- **Причина**: задание было отправлено в кластер HDi и завершилось с ошибкой.

- **Рекомендация**. Перейдите по ссылке "журналы Yarn" в выходных данных выполнения действия и найдите ошибки в выходных данных HDi. Повторите попытку или обратитесь в службу поддержки HDInsight, если это необходимо.


### <a name="error-code--2304"></a>Код ошибки: 2304

- **Сообщение**: `MSI authentication is not supported on storages for HDI activities.`

- **Причина**: связанные службы хранилища, используемые в действии связанной службы HDI или HDi, настроены с проверкой подлинности MSI, что не поддерживается.

- **Рекомендация**. Укажите полные строки подключения для учетных записей хранения, используемых в HDi связанной службе или операции HDi.


### <a name="error-code--2305"></a>Код ошибки: 2305

- **Сообщение**: `Failed to initialize the HDInsight client for the cluster '%cluster;'. Error: '%message;'`

- **Причина**: сведения о подключении для кластера HDi неверны, указанный пользователь не имеет разрешений на выполнение требуемого действия, или служба HDInsight столкнулась с проблемами, связанными с запросами от ADF.

- **Рекомендация**: Убедитесь в правильности сведений о пользователе. Также убедитесь, что пользовательский интерфейс Ambari для кластера HDI можно открыть в браузере с виртуальной машины, где в случае с локальным IR-компьютером установлен IR, или можно открыть с любого компьютера в случае Azure IR.


### <a name="error-code--2306"></a>Код ошибки: 2306

- **Сообщение**: `An invalid json is provided for script action '%scriptActionName;'. Error: '%message;'`

- **Причина**: для действия скрипта указан недопустимый формат JSON.


- **Рекомендация**: сообщение об ошибке должно помочь определить причину проблемы. Исправьте конфигурацию JSON и повторите попытку. Дополнительные сведения см. в https://docs.microsoft.com/azure/data-factory/compute-linked-services#azure-hdinsight-on-demand-linked-service.
                


### <a name="error-code--2310"></a>Код ошибки: 2310

- **Сообщение**: `Failed to submit Spark job. Error: '%message;'`

- **Причина**: ADF попытался создать пакет в кластере Spark с помощью API Livy (Livy/Batch), но получено сообщение об ошибке.

- **Рекомендация**: чтобы устранить проблему, следуйте указаниям в сообщении об ошибке. Если нет достаточных сведений для разрешения проблемы, обратитесь к группе HDI и предоставьте ей идентификатор и идентификатор задания, которые можно найти в выходных данных выполнения действия на странице мониторинга ADF.


### <a name="error-code--2312"></a>Код ошибки: 2312

- **Сообщение**: `Spark job failed, batch id:%batchId;. Please follow the links in the activity run Output from ADF Monitoring page to troubleshoot the run on HDInsight Spark cluster. Please contact HDInsight support team for further assistance.`

- **Причина**: сбой задания в кластере HDInsight Spark.

- **Рекомендация**. чтобы устранить неполадки в кластере HDInsight Spark, воспользуйтесь ссылками на странице "действие запуска выходных данных" в "МОНИТОРИНГе ADF". Для получения дополнительной помощи обратитесь в службу поддержки HDInsight.


### <a name="error-code--2313"></a>Код ошибки: 2313

- **Сообщение**: `The batch with ID '%batchId;' was not found on Spark cluster. Open the Spark History UI and try to find it there. Contact HDInsight support for further assistance.`

- **Причина**: пакет был удален в кластере HDInsight Spark.

- **Рекомендация**: Устранение неполадок в пакетах в кластере HDInsight Spark. Обратитесь за помощью в службу поддержки HDInsight. 


### <a name="error-code--2328"></a>Код ошибки: 2328

- **Сообщение**: `Failed to create the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Причина**: `The error message should show the details of what went wrong.`

- **Рекомендация**: сообщение об ошибке должно помочь устранить проблему.


### <a name="error-code--2329"></a>Код ошибки: 2329

- **Сообщение**: `Failed to delete the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Причина**: в сообщении об ошибке должны отображаться сведения о том, что пошло не так.

- **Рекомендация**: сообщение об ошибке должно помочь устранить проблему.


### <a name="error-code--2331"></a>Код ошибки: 2331

- **Сообщение**: `The file path should not be null or empty.`

- **Причина**: указанный путь к файлу пуст.

- **Рекомендация**: укажите путь к существующему файлу.


### <a name="error-code--2340"></a>Код ошибки: 2340

- **Сообщение**: `HDInsightOnDemand linked service does not support execution via SelfHosted IR. Your IR name is '%IRName;'. Please select an Azure IR instead.`

- **Причина**: связанная служба HDInsightOnDemand не поддерживает выполнение через селфхостед IR.

- **Рекомендация**: выберите Azure IR и повторите попытку.


### <a name="error-code--2341"></a>Код ошибки: 2341

- **Сообщение**: `HDInsight cluster URL '%clusterUrl;' is incorrect, it must be in URI format and the scheme must be 'https'.`

- **Причина**: указанный URL-адрес имеет неправильный формат.

- **Рекомендация**: Исправьте URL-адрес кластера и повторите попытку.


### <a name="error-code--2342"></a>Код ошибки: 2342

- **Сообщение**: `Failed to connect to HDInsight cluster: '%errorMessage;'.`

- **Причина**: указанные учетные данные неверны для кластера, или возникла проблема с конфигурацией сети или подключением или возникли проблемы с подключением к кластеру.

- **Рекомендация**.  
      1. Проверьте правильность учетных данных, открыв пользовательский интерфейс Ambari кластера HDInsight в браузере.
      2. Если кластер находится в виртуальной сети и используется локальная среда IR, URL-адрес HDI должен быть частным URL-адресом в виртуальных сетей. Это означает, что после имени кластера должно быть задано "-int". Например, "https://mycluster.azurehdinsight.net/" следует изменить на "https://mycluster-int.azurehdinsight.net/".
      2. Если кластер находится в виртуальной сети, используется локальная IR и используется частный URL-адрес, а подключение по-прежнему завершилось сбоем, а на виртуальной машине, где установлена среда IR, возникли проблемы с подключением к HDI. Подключитесь к виртуальной машине, где установлена среда IR, и откройте пользовательский интерфейс Ambari в браузере. Используйте частный URL-адрес кластера. Это подключение должно работать из браузера. Если это не так, обратитесь в службу поддержки HDInsight за помощью.
      3. Если локальная среда IR не используется, кластер HDI должен быть общедоступным. Откройте пользовательский интерфейс Ambari в браузере и убедитесь, что он открыт. Если возникли проблемы с кластером или службами на нем, обратитесь в службу поддержки HDInsight за помощью.
      Поэтому, как правило, URL-адрес кластера HDI, используемый в связанной службе ADF, должен быть доступен для службы ADF IR (с локальным размещением или Azure), чтобы проверить подключение, а также для выполнения работы. Это можно легко проверить, открыв этот URL-адрес из браузера либо с виртуальной машины, либо с общедоступного компьютера.
    


### <a name="error-code--2343"></a>Код ошибки: 2343

- **Сообщение**: `User name and password cannot be null or empty to connect to the HDInsight cluster.`

- **Причина**: имя пользователя или пароль пусты.

- **Рекомендация**: укажите правильные учетные данные для подключения к HDi и повторите попытку.


### <a name="error-code--2345"></a>Код ошибки: 2345

- **Сообщение**: `Failed to read the content of the hive script. Error: '%message;'`

- **Причина**: файл скрипта не существует, или ADF не удалось подключиться к расположению скрипта.

- **Рекомендация**: Убедитесь, что скрипт существует, а связанная служба имеет правильные учетные данные для подключения.


### <a name="error-code--2346"></a>Код ошибки: 2346

- **Сообщение**: `Failed to create ODBC connection to the HDI cluster with error message '%message;'.`

- **Причина**: ADF попытался установить подключение ODBC к кластеру HDi и выполнить его с ошибкой.

- **Рекомендация**: сообщение об ошибке и код ошибки могут помочь при устранении ошибок подключения ODBC. Если у вас недостаточно места для устранения проблемы, обратитесь в службу поддержки Azure HDInsight.


### <a name="error-code--2347"></a>Код ошибки: 2347

- **Сообщение**: `Hive execution through ODBC failed with error message '%message;'.`

- **Причина**: файл ADF передал скрипт Hive для выполнения в кластере HDi через соединение ODBC, и сценарий завершился СБОЕМ на HDi.

- **Рекомендация**: сбой выполнения скрипта Hive в кластере HDi, сообщение об ошибке и код ошибки, которые могут помочь при устранении неполадок. Если у вас недостаточно места для устранения проблемы, обратитесь в службу поддержки Azure HDInsight.


### <a name="error-code--2348"></a>Код ошибки: 2348

- **Сообщение**: `The main storage has not been initialized. Please check the properties of the storage linked service in the HDI linked service.`

- **Причина**: Свойства связанной службы хранилища заданы неправильно.

- **Рекомендация**: в главной связанной службе хранилища для действий HDi поддерживаются только полные строки подключения. Убедитесь, что вы не используете проверку подлинности MSI или приложения.


### <a name="error-code--2350"></a>Код ошибки: 2350

- **Сообщение**: `Failed to prepare the files for the run '%jobId;'. HDI cluster: '%cluster;', Error: '%errorMessage;'`

- **Причина**: учетные данные, предоставленные для подключения к хранилищу, где должны быть расположены файлы, неверны, или файлы не существуют.

- **Рекомендация**. Эта ошибка возникает, когда ADF выполняет шаги подготовки для действий HDi. Он пытается скопировать файлы в основное хранилище перед отправкой задания в HDI. Убедитесь, что файлы существуют в указанном расположении. подключение к хранилищу указано правильно. Операции ADF HDI не поддерживают проверку подлинности MSI для учетных записей хранения, связанных с действиями HDI, поэтому убедитесь, что эти связанные службы имеют полные ключи или используют Azure Key Vault.


### <a name="error-code--2351"></a>Код ошибки: 2351

- **Сообщение**: `Could not open the file '%filePath;' in container/fileSystem '%container;'.`

- **Причина**: файл не существует по указанному пути.

- **Рекомендация**: Убедитесь, что файл существует, а связанная служба со сведениями о соединении, указывающими на этот файл, имеет правильные учетные данные.


### <a name="error-code--2352"></a>Код ошибки: 2352

- **Сообщение**: `The file storage has not been initialized. Please check the properties of the file storage linked service in the HDI activity.`

- **Причина**: Свойства связанной службы хранилища файлов заданы неправильно.

- **Рекомендация**: Убедитесь, что свойства связанной службы хранилища файлов настроены правильно.


### <a name="error-code--2353"></a>Код ошибки: 2353

- **Сообщение**: `The script storage has not been initialized. Please check the properties of the script storage linked service in the HDI activity.`

- **Причина**: Свойства связанной службы хранилища скриптов заданы неправильно.

- **Рекомендация**: Убедитесь, что свойства связанной службы хранилища скриптов настроены правильно.


### <a name="error-code--2354"></a>Код ошибки: 2354

- **Сообщение**: `The storage linked service type '%linkedServiceType;' is not supported for '%executorType;' activities for property '%linkedServicePropertyName;'.`

- **Причина**: тип связанной службы хранилища не поддерживается действием.

- **Рекомендация**. Убедитесь, что выбранная связанная служба имеет один из поддерживаемых типов для этого действия. Действия HDI поддерживают связанные службы AzureBlobStorage и Азуреблобфсстораже.


### <a name="error-code--2355"></a>Код ошибки: 2355

- **Сообщение**: `The '%value' provided for commandEnvironment is incorrect. The expected value should be an array of strings where each string has the format CmdEnvVarName=CmdEnvVarValue.`

- **Причина**: предоставлено неверное значение для комманденвиронмент.

- **Рекомендация**.  
      Убедитесь, что указанное значение похоже на: \"Комманденвиронмент\": [\"variableName = Вариаблевалуе\"], и каждая переменная присутствует в списке только один раз.
    


### <a name="error-code--2356"></a>Код ошибки: 2356

- **Сообщение**: `The commandEnvironment already contains a variable named '%variableName;'.`

- **Причина**: переменная была предоставлена дважды в комманденвиронмент.

- **Рекомендация**.  
      Убедитесь, что указанное значение похоже на: \"Комманденвиронмент\": [\"variableName = Вариаблевалуе\"], и каждая переменная присутствует в списке только один раз.
    


### <a name="error-code--2357"></a>Код ошибки: 2357

- **Сообщение**: `The certificate or password is wrong for ADLS Gen 1 storage.`

- **Причина**: указанные учетные данные неверны.

- **Рекомендация**: Проверьте сведения о подключении в связанной Службе ADLS Gen 1 и убедитесь, что проверка соединения выполнена.


### <a name="error-code--2358"></a>Код ошибки: 2358

- **Сообщение**: `The value '%value;' for the required property 'TimeToLive' in the on demand HDInsight linked service '%linkedServiceName;' has invalid format. It should be a timespan between '00:05:00' and '24:00:00'.`

- **Причина**: указанное значение для обязательного свойства "TimeToLive" имеет недопустимый формат. 

- **Рекомендация**: обновите значение, чтобы оно было в предложенном диапазоне, и повторите попытку.


### <a name="error-code--2359"></a>Код ошибки: 2359

- **Сообщение**: `The value '%value;' for the property 'roles' is invalid. Expected types are 'zookeeper', 'headnode', and 'workernode'.`

- **Причина**: указанное значение свойства Roles недопустимо.

- **Рекомендация**: обновите значение, чтобы оно было одним из предложений, и повторите попытку.


### <a name="error-code--2360"></a>Код ошибки: 2360

- **Сообщение**: `The connection string in HCatalogLinkedService is invalid. Encountered an error while trying to parse: '%message;'.`

- **Причина**: указанная строка подключения для хкаталоглинкедсервице недопустима.

- **Рекомендация**: измените значение на правильную строку подключения SQL Azure и повторите попытку.


### <a name="error-code--2361"></a>Код ошибки: 2361

- **Сообщение**: `Failed to create on demand HDI cluster. Cluster name is '%clusterName;'.`

- **Причина**: сбой создания кластера, и ADF не получил ошибку из службы HDInsight.

- **Рекомендация**: откройте портал Azure и попытайтесь найти ресурс HDi с указанным именем и проверьте состояние подготовки. Для получения дополнительной помощи обратитесь в службу поддержки HDInsight.


### <a name="error-code--2362"></a>Код ошибки: 2362

- **Сообщение**: `Only Azure Blob storage accounts are supported as additional storages for HDInsight on demand linked service.`

- **Причина**: предоставленное дополнительное хранилище не было хранилищем BLOB-объектов Azure.

- **Рекомендация**: предоставьте учетную запись хранилища BLOB-объектов Azure в качестве дополнительного хранилища для связанной службы HDInsight по запросу.



## <a name="web-activity"></a>Веб-действие

### <a name="error-code--2128"></a>Код ошибки: 2128

- **Сообщение**: `No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Причина**: сетевое подключение, сбой DNS, проверка сертификата сервера или превышение времени ожидания.

- **Рекомендация**: Убедитесь, что конечная точка, которую вы пытаетесь навести, отвечает на запросы. Вы можете использовать такие средства, как Fiddler/POST.


### <a name="error-code--2108"></a>Код ошибки: 2108

- **Сообщение**: `Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Причина**: не удалось выполнить запрос из-за базовой проблемы, такой как сетевое подключение, сбой DNS, проверка сертификата сервера или превышение времени ожидания.

- **Рекомендация**: для проверки запроса используйте Fiddler/POST.
<br>


#### <a name="more-details"></a>Дополнительные сведения
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


            
