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
ms.openlocfilehash: f07cc109b21010df89b105576cb9afcf93df774a
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82744766"
---
# <a name="troubleshoot-azure-data-factory"></a>Устранение неполадок фабрики данных Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

В этой статье рассматриваются распространенные методы устранения неполадок для действий внешнего управления в фабрике данных Azure.

## <a name="connector-and-copy-activity"></a>Действие соединителя и копирования

Сведения о проблемах с соединителем, например об ошибке при копировании, см. в статье [Устранение неполадок соединителей фабрики данных Azure](connector-troubleshoot-guide.md).

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code-3200"></a>Код ошибки: 3200

- **Сообщение**: Ошибка 403.

- **Причина**:`The Databricks access token has expired.`

- **Рекомендация**. по умолчанию маркер доступа Azure Databricks действителен в течение 90 дней. Создайте новый токен и обновите связанную службу.

### <a name="error-code-3201"></a>Код ошибки: 3201

- **Сообщение**:`Missing required field: settings.task.notebook_task.notebook_path.`

- **Причина**:`Bad authoring: Notebook path not specified correctly.`

- **Рекомендация**: укажите путь к записной книжке в действии "кирпичы".

<br/> 

- **Сообщение**:`Cluster... does not exist.`

- **Причина**:`Authoring error: Databricks cluster does not exist or has been deleted.`

- **Рекомендация**: Убедитесь, что кластер кирпичей существует.

<br/> 

- **Сообщение**:`Invalid Python file URI... Please visit Databricks user guide for supported URI schemes.`

- **Причина**:`Bad authoring.`

- **Рекомендация**: укажите абсолютные пути для схем адресации рабочей области или `dbfs:/folder/subfolder/foo.py` для файлов, хранящихся в файловой системе "кирпичи (DFS)".

<br/> 

- **Сообщение**:`{0} LinkedService should have domain and accessToken as required properties.`

- **Причина**:`Bad authoring.`

- **Рекомендация**: Проверьте [определение связанной службы](compute-linked-services.md#azure-databricks-linked-service).

<br/> 

- **Сообщение**:`{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **Причина**:`Bad authoring.`

- **Рекомендация**: Проверьте [определение связанной службы](compute-linked-services.md#azure-databricks-linked-service).

<br/> 

- **Сообщение**:`Node type Standard_D16S_v3 is not supported. Supported node types: Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3, Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2, Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3, Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2, Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2, Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3, Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s, Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2, Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2, Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3, Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2, Standard_L64s_v2, Standard_L80s_v2.`

- **Причина**:`Bad authoring.`

- **Рекомендация**: см. сообщение об ошибке.

<br/>

### <a name="error-code-3202"></a>Код ошибки: 3202

- **Сообщение**:`There were already 1000 jobs created in past 3600 seconds, exceeding rate limit: 1000 job creations per 3600 seconds.`

- **Причина**:`Too many Databricks runs in an hour.`

- **Рекомендация**. Проверьте все конвейеры, использующие эту рабочую область блоков, для их частоты создания заданий. Если конвейеры запустили слишком много блоков обработки в статистическом модуле, перенесите некоторые конвейеры в новую рабочую область.

<br/> 

- **Сообщение**:`Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **Причина**:`Authoring error: No value provided for the parameter.`

- **Рекомендация**: Проверьте JSON конвейера и убедитесь, что все параметры в записной книжке басепараметерс указывают непустое значение.

<br/> 

- **Сообщение**: `User: `симплеусерконтекст {UserID =..., Name =user@company.com, orgId =...}` is not authorized to access cluster.`

- **Причина**: пользователь, создавший маркер доступа, не может получить доступ к кластеру кирпичей данных, указанному в связанной службе.

- **Рекомендация**: Убедитесь, что пользователь имеет необходимые разрешения в рабочей области.

### <a name="error-code-3203"></a>Код ошибки: 3203

- **Сообщение**:`The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **Причина**: работа кластера была завершена. Для интерактивных кластеров эта проблема может быть условием состязания.

- **Рекомендация**. чтобы избежать этой ошибки, используйте кластеры заданий.

### <a name="error-code-3204"></a>Код ошибки: 3204

- **Сообщение**:`Job execution failed.`

- **Причина**: сообщения об ошибках указывают на различные проблемы, например непредвиденное состояние кластера или определенное действие. Часто сообщение об ошибке не отображается.

- **Рекомендация**: н/д
 
## <a name="azure-data-lake-analytics"></a>Аналитика озера данных Azure

Следующая таблица относится к U-SQL.
 
### <a name="error-code-2709"></a>Код ошибки: 2709

- **Сообщение**:`The access token is from the wrong tenant.`

- **Причина**: неверный клиент Azure Active Directory (Azure AD).

- **Рекомендация**: неправильный клиент Azure Active Directory (Azure AD).

<br/>

- **Сообщение**:`We cannot accept your job at this moment. The maximum number of queued jobs for your account is 200. `

- **Причина**: Эта ошибка вызвана регулированием на Data Lake Analytics.

- **Рекомендация**: Сократите число заданий, отправленных в Data Lake Analytics. Измените триггеры фабрики данных и параметры параллелизма для действий или увеличьте ограничения на Data Lake Analytics.

<br/> 

- **Сообщение**:`This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **Причина**: Эта ошибка вызвана регулированием на Data Lake Analytics.

- **Рекомендация**: Сократите число заданий, отправленных в Data Lake Analytics. Измените триггеры фабрики данных и параметры параллелизма для действий или увеличьте ограничения на Data Lake Analytics.

### <a name="error-code-2705"></a>Код ошибки: 2705

- **Сообщение**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/> <br/> User is not able to access Data Lake Store. <br/> <br/> User is not authorized to use Data Lake Analytics.`

- **Причина**: субъекту-службе или сертификату не предоставлен доступ к файлу в хранилище.

- **Рекомендация**: Убедитесь, что субъект-служба или сертификат, предоставляемый пользователем для заданий Data Lake Analytics, имеет доступ к учетной записи Data Lake Analytics и экземпляру Data Lake Storage по умолчанию из корневой папки.

### <a name="error-code-2711"></a>Код ошибки: 2711

- **Сообщение**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/> <br/> User is not able to access Data Lake Store. <br/> <br/> User is not authorized to use Data Lake Analytics.`

- **Причина**: субъекту-службе или сертификату не предоставлен доступ к файлу в хранилище.

- **Рекомендация**: Убедитесь, что субъект-служба или сертификат, предоставляемый пользователем для заданий Data Lake Analytics, имеет доступ к учетной записи Data Lake Analytics и экземпляру Data Lake Storage по умолчанию из корневой папки.

<br/> 

- **Сообщение**:`Cannot find the 'Azure Data Lake Store' file or folder.`

- **Причина**: неверный путь к файлу U-SQL или учетные данные связанной службы не имеют доступа.

- **Рекомендация**: Проверьте путь и учетные данные, указанные в связанной службе.

### <a name="error-code-2704"></a>Код ошибки: 2704

- **Сообщение**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/> <br/> User is not able to access Data Lake Store. <br/> <br/> User is not authorized to use Data Lake Analytics.`

- **Причина**: субъекту-службе или сертификату не предоставлен доступ к файлу в хранилище.

- **Рекомендация**: Убедитесь, что субъект-служба или сертификат, предоставляемый пользователем для заданий Data Lake Analytics, имеет доступ к учетной записи Data Lake Analytics и экземпляру Data Lake Storage по умолчанию из корневой папки.

### <a name="error-code-2707"></a>Код ошибки: 2707

- **Сообщение**:`Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and 'DataLakeAnalyticsUri'.`

- **Причина**: неверная учетная запись Data Lake Analytics в связанной службе.

- **Рекомендация**: Убедитесь, что указана правильная учетная запись.

### <a name="error-code-2703"></a>Код ошибки: 2703

- **Сообщение**:`Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error Id:").`

- **Причина**: ошибка связана с Data Lake Analytics.

- **Рекомендация**: задание было отправлено в Data Lake Analytics, и сценарий в нем завершился неудачно. Исследование в Data Lake Analytics. На портале перейдите к учетной записи Data Lake Analytics и найдите задание с помощью идентификатора запуска действия фабрики данных (не используйте идентификатор выполнения конвейера). Задание содержит дополнительные сведения об ошибке и поможет вам устранить неполадки.

   Если разрешение неясно, обратитесь в службу поддержки Data Lake Analytics и укажите URL-адрес задания, в том числе имя учетной записи и идентификатор задания.
 
## <a name="azure-functions"></a>Функции Azure

### <a name="error-code-3602"></a>Код ошибки: 3602

- **Сообщение**:`Invalid HttpMethod: '%method;'.`

- **Причина**: HttpMethod, указанный в полезных данных действия, не поддерживается действием функции Azure.

- **Рекомендация**. Поддерживаемые ХТТПМЕСОДС: размещение, публикация, получение, удаление, параметры, головной элемент и трассировка.

### <a name="error-code-3603"></a>Код ошибки: 3603

- **Сообщение**:`Response Content is not a valid JObject.`

- **Причина**: функция Azure, которая была вызвана, не вернула полезные данные JSON в ответе. Действие функции Azure фабрики данных Azure (ADF) поддерживает только содержимое ответа JSON.

- **Рекомендация**: обновление функции Azure для возврата допустимых полезных данных JSON, таких как функция C#, может возвращать`(ActionResult)new OkObjectResult("{\"Id\":\"123\"}");`

### <a name="error-code-3606"></a>Код ошибки: 3606

- **Сообщение**: в действии функции Azure отсутствует ключ функции.

- **Причина**: определение действия функции Azure не завершено.

- **Рекомендация**: Убедитесь, что у входного определения JSON действия функции Azure есть свойство `functionKey`с именем.

### <a name="error-code-3607"></a>Код ошибки: 3607

- **Сообщение**:`Azure function activity missing function name.`

- **Причина**: определение действия функции Azure не завершено.

- **Рекомендация**: Убедитесь, что у входного определения JSON действия функции Azure есть свойство `functionName`с именем.

### <a name="error-code-3608"></a>Код ошибки: 3608

- **Сообщение**:`Call to provided Azure function '%FunctionName;' failed with status-'%statusCode;' and message - '%message;'.`

- **Причина**. сведения о функции Azure в определении действия могут быть неправильными.

- **Рекомендация**: исправьте сведения о функции Azure и повторите попытку.

### <a name="error-code-3609"></a>Код ошибки: 3609

- **Сообщение**:`Azure function activity missing functionAppUrl.`

- **Причина**: определение действия функции Azure не завершено.

- **Рекомендация**: Убедитесь, что у входного определения JSON действия функции Azure есть свойство `functionAppUrl`с именем.

### <a name="error-code-3610"></a>Код ошибки: 3610

- **Сообщение**:`There was an error while calling endpoint.`

- **Причина**: возможно, URL-адрес функции неверен.

- **Рекомендация**: Проверьте правильность значения для `functionAppUrl` в JSON действия и повторите попытку.

### <a name="error-code-3611"></a>Код ошибки: 3611

- **Сообщение**:`Azure function activity missing Method in JSON.`

- **Причина**: определение действия функции Azure не завершено.

- **Рекомендация**: Убедитесь, что у входного определения JSON действия функции Azure есть свойство `method`с именем.

### <a name="error-code-3612"></a>Код ошибки: 3612

- **Сообщение**:`Azure function activity missing LinkedService definition in JSON.`

- **Причина**: определение действия функции Azure не завершено.

- **Рекомендация**: Убедитесь, что в определении JSON для входного действия функции Azure имеются связанные сведения о связанной службе.

## <a name="azure-machine-learning"></a>Машинное обучение Azure

### <a name="error-code-4101"></a>Код ошибки: 4101

- **Сообщение**:`AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **Причина**: неправильный формат или отсутствует определение свойства `%propertyName;`.

- **Рекомендация**: Проверьте, имеет ли `%activityName;` действие свойство `%propertyName;` , определенное с правильными данными.

### <a name="error-code-4110"></a>Код ошибки: 4110

- **Сообщение**:`AzureMLExecutePipeline activity missing LinkedService definition in JSON.`

- **Причина**: определение действия азуремлексекутепипелине не завершено.

- **Рекомендация**: Убедитесь, что определение JSON входного азуремлексекутепипелине действия содержит правильные сведения о связанной службе.

### <a name="error-code-4111"></a>Код ошибки: 4111

- **Сообщение**:`AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **Причина**: неправильное определение действия.

- **Рекомендация**: Убедитесь, что определение JSON входного азуремлексекутепипелине действия содержит правильные сведения о связанной службе.

### <a name="error-code-4112"></a>Код ошибки: 4112

- **Сообщение**:`AzureMLService linked service has invalid value for property '%propertyName;'.`

- **Причина**: неправильный формат или отсутствует определение свойства "% PropertyName;".

- **Рекомендация**: Проверьте, определена ли для связанной службы `%propertyName;` свойство с правильными данными.

### <a name="error-code-4121"></a>Код ошибки: 4121

- **Сообщение**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Причина**: истек срок действия учетных данных, используемых для доступа к машинное обучение Azure.

- **Рекомендация**: Убедитесь, что учетные данные действительны, и повторите попытку.

### <a name="error-code-4122"></a>Код ошибки: 4122

- **Сообщение**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Причина**: учетные данные, указанные в связанной службе машинное обучение Azure, являются недопустимыми или не имеют разрешения на выполнение операции.

- **Рекомендация**: Убедитесь, что учетные данные в связанной службе допустимы и имеют разрешение на доступ к машинное обучение Azure.

### <a name="error-code-4123"></a>Код ошибки: 4123

- **Сообщение**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Причина**: свойства действия, такие как `pipelineParameters` , являются недопустимыми для конвейера машинное обучение Azure (машинного обучения).

- **Рекомендация**: Убедитесь, что значения свойств действия соответствуют ожидаемым полезным данным в опубликованном КОНВЕЙЕРе машинного обучения Azure, указанном в связанной службе.

### <a name="error-code-4124"></a>Код ошибки: 4124

- **Сообщение**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Причина**: опубликованная конечная точка КОНВЕЙЕРа машинного обучения Azure не существует.

- **Рекомендация**: Убедитесь, что опубликованная конечная точка конвейера машинное обучение Azure, указанная в связанной службе, существует в машинное обучение Azure.

### <a name="error-code-4125"></a>Код ошибки: 4125

- **Сообщение**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Причина**: на машинное обучение Azure возникла ошибка сервера.

- **Рекомендация**: повторите попытку позже. Если проблема не будет устранена, обратитесь к группе Машинное обучение Azure.

### <a name="error-code-4126"></a>Код ошибки: 4126

- **Сообщение**:`Azure ML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in Azure Machine Learning for more error logs.`

- **Причина**: сбой запуска КОНВЕЙЕРа машинного обучения Azure.

- **Рекомендация**: Проверьте машинное обучение Azure для получения дополнительных журналов ошибок, а затем исправьте конвейер ml.

## <a name="common"></a>Распространенные

### <a name="error-code-2103"></a>Код ошибки: 2103

- **Сообщение**:`Please provide value for the required property '%propertyName;'.`

- **Причина**: не указано обязательное значение для свойства.

- **Рекомендация**: Укажите значение из сообщения и повторите попытку.

### <a name="error-code-2104"></a>Код ошибки: 2104

- **Сообщение**:`The type of the property '%propertyName;' is incorrect.`

- **Причина**: предоставлен неправильный тип свойства.

- **Рекомендация**: Исправьте тип свойства и повторите попытку.

### <a name="error-code-2105"></a>Код ошибки: 2105

- **Сообщение**:`An invalid json is provided for property '%propertyName;'. Encountered an error while trying to parse: '%message;'.`

- **Причина**: значение свойства недопустимо или не находится в ожидаемом формате.

- **Рекомендация**: см. документацию по свойству и убедитесь, что предоставленное значение включает правильный формат и тип.

### <a name="error-code-2106"></a>Код ошибки: 2106

- **Сообщение**:`The storage connection string is invalid. %errorMessage;`

- **Причина**: строка подключения для хранилища недопустима или имеет неправильный формат.

- **Рекомендация**: перейдите в портал Azure и найдите хранилище, скопируйте и вставьте строку подключения в связанную службу и повторите попытку.

### <a name="error-code-2108"></a>Код ошибки: 2108

- **Сообщение**:`Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Причина**: не удалось выполнить запрос из-за базовой проблемы, такой как сетевое подключение, сбой DNS, проверка сертификата сервера или время ожидания.

- **Рекомендация**: для проверки запроса используйте Fiddler/POST.

### <a name="error-code-2110"></a>Код ошибки: 2110

- **Сообщение**:`The linked service type '%linkedServiceType;' is not supported for '%executorType;' activities.`

- **Причина**: связанная служба, указанная в действии, неверна.

- **Рекомендация**: Убедитесь, что тип связанной службы является одним из поддерживаемых типов для действия. Например, тип связанной службы для действий HDI может быть HDInsight или HDInsightOnDemand.

### <a name="error-code-2111"></a>Код ошибки: 2111

- **Сообщение**:`The type of the property '%propertyName;' is incorrect. The expected type is %expectedType;.`

- **Причина**: тип предоставленного свойства неверен.

- **Рекомендация**: Исправьте тип свойства и повторите попытку.

### <a name="error-code-2112"></a>Код ошибки: 2112

- **Сообщение**:`The cloud type is unsupported or could not be determined for storage from the EndpointSuffix '%endpointSuffix;'.`

- **Причина**: тип облака не поддерживается или не может быть определен для хранилища из EndpointSuffix.

- **Рекомендация**: Используйте хранилище в другом облаке и повторите попытку.

### <a name="error-code-2128"></a>Код ошибки: 2128

- **Сообщение**:`No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Причина**: сетевое подключение, сбой DNS, проверка сертификата сервера или превышение времени ожидания.

- **Рекомендация**: Убедитесь, что конечная точка, которую вы пытаетесь навести, отвечает на запросы. Вы можете использовать такие средства, как Fiddler/POST.

## <a name="custom"></a>Особые настройки

Следующая таблица относится к пакетной службе Azure.
 
### <a name="error-code-2500"></a>Код ошибки: 2500

- **Сообщение**:`Hit unexpected exception and execution failed.`

- **Причина**:`Can't launch command, or the program returned an error code.`

- **Рекомендация**: Убедитесь, что исполняемый файл существует. Если программа запущена, убедитесь, что *stdout. txt* и *stderr. txt* были переданы в учетную запись хранения. Рекомендуется включать в код журналы для отладки.

### <a name="error-code-2501"></a>Код ошибки: 2501

- **Сообщение**:`Cannot access user batch account; please check batch account settings.`

- **Причина**: неверный ключ доступа пакетной службы или имя пула.

- **Рекомендация**: Проверьте имя пула и ключ доступа пакетной службы в связанной службе.

### <a name="error-code-2502"></a>Код ошибки: 2502

- **Сообщение**:`Cannot access user storage account; please check storage account settings.`

- **Причина**: неправильное имя учетной записи хранения или ключ доступа.

- **Рекомендация**: Проверьте имя учетной записи хранения и ключ доступа в связанной службе.

### <a name="error-code-2504"></a>Код ошибки: 2504

- **Сообщение**:`Operation returned an invalid status code 'BadRequest'.`

- **Причина**: слишком много файлов в `folderPath` пользовательском действии. Общий размер `resourceFiles` не может превышать 32 768 символов.

- **Рекомендация**: удалите ненужные файлы или заархивируйте их в ZIP-файл и добавьте команду unzip, чтобы извлечь их.
   
   Например, используйте`powershell.exe -nologo -noprofile -command "& { Add-Type -A 'System.IO.Compression.FileSystem'; [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ; $folder\yourProgram.exe`

### <a name="error-code-2505"></a>Код ошибки: 2505

- **Сообщение**:`Cannot create Shared Access Signature unless Account Key credentials are used.`

- **Причина**. пользовательские действия поддерживают только учетные записи хранения, использующие ключ доступа.

- **Рекомендация**: см. описание ошибки.

### <a name="error-code-2507"></a>Код ошибки: 2507

- **Сообщение**:`The folder path does not exist or is empty: ...`

- **Причина**: в учетной записи хранения нет файлов по указанному пути.

- **Рекомендация**: путь к папке должен содержать исполняемые файлы, которые требуется запустить.

### <a name="error-code-2508"></a>Код ошибки: 2508

- **Сообщение**:`There are duplicate files in the resource folder.`

- **Причина**: несколько файлов с одинаковыми именами находятся в разных вложенных папках FolderPath.

- **Рекомендация**: структура папки с пользовательскими действиями в разделе FolderPath. Если необходимо сохранить структуру папок, ZIP-файлы и извлечь их в пакетной службе Azure с помощью команды unzip.
   
   Например, используйте`powershell.exe -nologo -noprofile -command "& { Add-Type -A 'System.IO.Compression.FileSystem'; [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ; $folder\yourProgram.exe`

### <a name="error-code-2509"></a>Код ошибки: 2509

- **Сообщение**:`Batch url ... is invalid; it must be in Uri format.`

- **Причина**: URL-адреса пакетов должны быть похожи на`https://mybatchaccount.eastus.batch.azure.com`

- **Рекомендация**: см. описание ошибки.

### <a name="error-code-2510"></a>Код ошибки: 2510

- **Сообщение**:`An error occurred while sending the request.`

- **Причина**: недопустимый URL-адрес пакета.

- **Рекомендация**: Проверьте URL-адрес пакета.
 
## <a name="hdinsight"></a>HDInsight

### <a name="error-code-200"></a>Код ошибки: 200

- **Сообщение**:`Unexpected error happened: '%error;'.`

- **Причина**: Внутренняя ошибка службы.

- **Рекомендация**: для получения дополнительной помощи обратитесь в службу поддержки ADF.

### <a name="error-code-201"></a>Код ошибки: 201

- **Сообщение**:`JobType %jobType; is not found.`

- **Причина**. Существует новый тип задания, который не поддерживается в ADF.

- **Рекомендация**: для получения дополнительной помощи обратитесь в службу поддержки ADF.

### <a name="error-code-202"></a>Код ошибки: 202

- **Сообщение**:`Failed to create on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **Причина**. сообщение об ошибке содержит сведения о том, что пошло не так.

- **Рекомендация**: сведения о сообщении об ошибке помогут устранить проблему. Если недостаточно сведений, обратитесь за помощью в службу поддержки ADF.

### <a name="error-code-203"></a>Код ошибки: 203

- **Сообщение**:`Failed to delete on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **Причина**. сообщение об ошибке содержит сведения о том, что пошло не так.

- **Рекомендация**: сведения о сообщении об ошибке помогут устранить проблему. Если недостаточно сведений, обратитесь за помощью в службу поддержки ADF.

### <a name="error-code-204"></a>Код ошибки: 204

- **Сообщение**:`The resumption token is missing for runId '%runId;'.`

- **Причина**: Внутренняя ошибка службы.

- **Рекомендация**: для получения дополнительной помощи обратитесь в службу поддержки ADF.

### <a name="error-code-205"></a>Код ошибки: 205

- **Сообщение**:`Failed to prepare cluster for LinkedService '%linkedServiceName;', the current resource status is '%status;'.`

- **Причина**: при создании кластера HDi по запросу произошла ошибка.

- **Рекомендация**: для получения дополнительной помощи обратитесь в службу поддержки ADF.

### <a name="error-code-206"></a>Код ошибки: 206

- **Сообщение**:`The batch ID for Spark job is invalid. Please retry your job, and if the problem persists, contact the ADF support for further assistance.`

- **Причина**: произошла внутренняя проблема со службой, вызвавшей эту ошибку.

- **Рекомендация**. Эта проблема может быть временной. Повторите задание. Если проблема не исчезнет, обратитесь за помощью в службу поддержки ADF.

### <a name="error-code-207"></a>Код ошибки: 207

- **Сообщение**:`Could not determine the region from the provided storage account. Please try using another primary storage account for the on demand HDI or contact ADF support team and provide the activity run ID.`

- **Причина**: произошла внутренняя ошибка при попытке определить регион из основной учетной записи хранения.

- **Рекомендация**: попробуйте использовать другое хранилище. Если этот параметр не является приемлемым, обратитесь в службу поддержки ADF для получения дополнительной помощи.

### <a name="error-code-208"></a>Код ошибки: 208

- **Сообщение**:`Service Principal or the MSI authenticator are not instantiated. Please consider providing a Service Principal in the HDI on demand linked service which has permissions to create an HDInsight cluster in the provided subscription and try again. In case if this is not an acceptable solution, contact ADF support team for further assistance.`

- **Причина**: произошла внутренняя ошибка при попытке чтения субъекта-службы или создания экземпляра проверки подлинности MSI.

- **Рекомендация**. Рассмотрите возможность предоставления субъекта-службы, обладающего разрешениями на создание кластера HDInsight в указанной подписке, и повторите попытку. Убедитесь, что [Управление удостоверениями настроено правильно](https://docs.microsoft.com/azure/hdinsight/hdinsight-managed-identities).

   Если этот параметр не является приемлемым, обратитесь в службу поддержки ADF для получения дополнительной помощи.

### <a name="error-code-2300"></a>Код ошибки: 2300

- **Сообщение**:`Failed to submit the job '%jobId;' to the cluster '%cluster;'. Error: %errorMessage;.`

- **Причина**: сообщение об ошибке содержит сообщение, аналогичное `The remote name could not be resolved.`. Указанный URI кластера может быть недопустимым.

- **Рекомендация**: Убедитесь, что кластер не был удален, а также что указанный универсальный код ресурса (URI) указан правильно. При открытии URI в браузере должен отобразиться пользовательский интерфейс Ambari. Если кластер находится в виртуальной сети, URI должен быть частным URI. Чтобы открыть его, используйте виртуальную машину (ВМ), которая является частью той же виртуальной сети.

   Дополнительные сведения см. [в разделе прямое подключение к службам Apache Hadoop](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#directly-connect-to-apache-hadoop-services).
 
 </br>

- **Причина**: Если сообщение об ошибке содержит сообщение `A task was canceled.`, аналогичное, время ожидания отправки задания истекло.

- **Рекомендация**. проблема может быть как общим подключением HDInsight, так и сетевым подключением. Сначала убедитесь, что пользовательский интерфейс HDInsight Ambari доступен из любого браузера. Затем убедитесь, что учетные данные все еще действительны.
   
   Если вы используете автономную интегрированную среду выполнения (IR), выполните этот шаг с виртуальной машины или компьютера, на котором установлена локальная среда IR. Затем попробуйте отправить задание из фабрики данных еще раз. Если по-прежнему происходит сбой, обратитесь за поддержкой к группе фабрики данных.

   Дополнительные сведения см. в статье [веб-интерфейс Ambari](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-manage-ambari#ambari-web-ui).

 </br>

- **Причина**: Если сообщение об ошибке содержит сообщение, `User admin is locked out in Ambari` аналогичное `Unauthorized: Ambari user name or password is incorrect`или, то учетные данные для HDInsight неверны или истек срок их действия.

- **Рекомендация**: исправьте учетные данные и повторно разверните связанную службу. Сначала убедитесь, что учетные данные работают в HDInsight, открыв универсальный код ресурса (URI) кластера в любом браузере и пытаясь войти в систему. Если учетные данные не работают, их можно сбросить с портал Azure.

   Для кластера ESP сбросьте пароль с помощью [самостоятельного сброса пароля](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-passwords-update-your-own-password).

 </br>

- **Причина**: Если сообщение об ошибке содержит сообщение `502 - Web server received an invalid response while acting as a gateway or proxy server`, аналогичное, эта ошибка возвращается службой HDInsight.

- **Рекомендация**: ошибка 502 часто возникает при завершении работы процесса сервера Ambari. Вы можете перезапустить службы Ambari, перезагрузив головной узел.

    1. Подключитесь к одному из узлов в HDInsight с помощью SSH.
    1. Найдите узел активного головного узла, выполнив `ping headnodehost`.
    1. Подключитесь к активному головному узлу, так как сервер Ambari находится на активном головном узле с помощью SSH. 
    1. Перезагрузите активный головной узел.

       Дополнительные сведения см. в документации по устранению неполадок Azure HDInsight. Пример:

       * [Ambari UI 502 error](https://hdinsight.github.io/ambari/ambari-ui-502-error.html) (Ошибка 502 интерфейса Ambari)
       * [Рпктимеаутексцептион для сервера Apache Spark Thrift](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-troubleshoot-rpctimeoutexception)
       * [Устранение ошибок шлюза в шлюзе приложений](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502).

 </br>

- **Причина**: Если сообщение об ошибке содержит сообщение, `Unable to service the submit job request as templeton service is busy with too many submit job requests` аналогичное `Queue root.joblauncher already has 500 applications, cannot accept submission of application`или, слишком много заданий отправляются в HDInsight одновременно.

- **Рекомендация**: Ограничьте число параллельных заданий, отправляемых в HDInsight. См. сведения о параллелизме действий фабрики данных, если задания отправляются одним действием. Измените триггеры, чтобы параллельные запуски конвейера были распределены по времени.

   Сведения об [HDInsight documentation](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-templeton-webhcat-debug-errors) ошибке см. `templeton.parallellism.job.submit` в документации по HDInsight.

### <a name="error-code-2301"></a>Код ошибки: 2301

- **Сообщение**:`Could not get the status of the application '%physicalJobId;' from the HDInsight service. Received the following error: %message;. Please refer to HDInsight troubleshooting documentation or contact their support for further assistance.`

- **Причина**: в кластере HDInsight или службе возникли проблемы.

- **Рекомендация**. Эта ошибка возникает, когда ADF не получает ответ от кластера HDInsight при попытке запросить состояние выполняемого задания. Эта проблема может быть связана с самим кластером, или служба HDInsight может быть непростой.

   Обратитесь к документации по устранению https://docs.microsoft.com/azure/hdinsight/hdinsight-troubleshoot-guideнеполадок HDInsight по адресу или обратитесь в службу поддержки за дальнейшей помощью.

### <a name="error-code-2302"></a>Код ошибки: 2302

- **Сообщение**:`Hadoop job failed with exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Contact HDInsight team for further support.`

- **Причина**: задание было отправлено в кластер HDi и завершилось с ошибкой.

- **Совет**. 

 1. Проверьте пользовательский интерфейс Ambari:
    1. Убедитесь, что все службы все еще работают.
    1. В пользовательском интерфейсе Ambari просмотрите раздел оповещение на панели мониторинга.
       1. Дополнительные сведения о предупреждениях и разрешениях для оповещений см. в статье [Управление кластером и его мониторинг](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html).
    1. Проверьте память YARN. Если объем памяти YARN высок, обработка заданий может быть отложена. Если у вас недостаточно ресурсов для размещения приложения или задания Spark, увеличьте масштаб кластера, чтобы убедиться, что в кластере достаточно памяти и ядер. 
 1. Запустите пример тестового задания.
    1. Если вы запустите это же задание в серверной части HDInsight, убедитесь, что оно выполнено. Примеры выполнения примеров см. в статье [Запуск примеров MapReduce, входящих в состав HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-run-samples-linux) . 
 1. Если задание по-прежнему не удалось выполнить в HDInsight, проверьте журналы приложений и сведения, которые предоставляются для поддержки:
    1. Проверьте, было ли задание отправлено в YARN. Если задание не было отправлено в Yarn, `--master yarn`используйте.
    1. Если приложение завершило выполнение, собирайте время начала и окончания приложения YARN. Если приложение не завершило выполнение, собирайте время начала и время запуска.
    1. Проверьте и собирайте журнал приложений `yarn logs -applicationId <Insert_Your_Application_ID>`с помощью.
    1. Проверьте и собирайте журналы диспетчер ресурсов Yarn в `/var/log/hadoop-yarn/yarn` каталоге.
    1. Если эти действия недостаточно для устранения проблемы, обратитесь в службу поддержки Azure HDInsight и укажите указанные выше журналы и метки времени.

### <a name="error-code-2303"></a>Код ошибки: 2303

- **Сообщение**:`Hadoop job failed with transient exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Try again or contact HDInsight team for further support.`

- **Причина**: задание было отправлено в кластер HDi и завершилось с ошибкой.

- **Совет**. 

 1. Проверьте пользовательский интерфейс Ambari:
    1. Убедитесь, что все службы все еще работают.
    1. В пользовательском интерфейсе Ambari просмотрите раздел оповещение на панели мониторинга.
       1. Дополнительные сведения о предупреждениях и разрешениях для оповещений см. в статье [Управление кластером и его мониторинг](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html).
    1. Проверьте память YARN. Если объем памяти YARN высок, обработка заданий может быть отложена. Если у вас недостаточно ресурсов для размещения приложения или задания Spark, увеличьте масштаб кластера, чтобы убедиться, что в кластере достаточно памяти и ядер. 
 1. Запустите пример тестового задания.
    1. Если вы запустите это же задание в серверной части HDInsight, убедитесь, что оно выполнено. Примеры выполнения примеров см. в статье [Запуск примеров MapReduce, входящих в состав HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-run-samples-linux) . 
 1. Если задание по-прежнему не удалось выполнить в HDInsight, проверьте журналы приложений и сведения, которые предоставляются для поддержки:
    1. Проверьте, было ли задание отправлено в YARN. Если задание не было отправлено в Yarn, `--master yarn`используйте.
    1. Если приложение завершило выполнение, собирайте время начала и окончания приложения YARN. Если приложение не завершило выполнение, собирайте время начала и время запуска.
    1. Проверьте и собирайте журнал приложений `yarn logs -applicationId <Insert_Your_Application_ID>`с помощью.
    1. Проверьте и собирайте журналы диспетчер ресурсов Yarn в `/var/log/hadoop-yarn/yarn` каталоге.
    1. Если эти действия недостаточно для устранения проблемы, обратитесь в службу поддержки Azure HDInsight и укажите указанные выше журналы и метки времени.

### <a name="error-code-2304"></a>Код ошибки: 2304

- **Сообщение**:`MSI authentication is not supported on storages for HDI activities.`

- **Причина**. связанные службы хранилища, используемые в действии связанной службы HDINSIGHT (HDI) или HDi, настроены с проверкой подлинности MSI, которая не поддерживается.

- **Рекомендация**: укажите полные строки подключения для учетных записей хранения, используемых в HDi связанной службе или операции HDi.

### <a name="error-code-2305"></a>Код ошибки: 2305

- **Сообщение**:`Failed to initialize the HDInsight client for the cluster '%cluster;'. Error: '%message;'`

- **Причина**: сведения о подключении для кластера HDi неверны, указанный пользователь не имеет разрешений на выполнение требуемого действия, или служба HDInsight отвечает на запросы от ADF.

- **Рекомендация**: Проверьте правильность сведений о пользователе и убедитесь, что пользовательский интерфейс Ambari для кластера HDi можно открыть в браузере с виртуальной машины, на которой установлен IR-сервер (для локальной среды IR), или можно открыть с любого компьютера (для Azure IR).

### <a name="error-code-2306"></a>Код ошибки: 2306

- **Сообщение**:`An invalid json is provided for script action '%scriptActionName;'. Error: '%message;'`

- **Причина**: для действия скрипта указан недопустимый формат JSON.

- **Рекомендация**: сообщение об ошибке должно помочь определить причину проблемы. Исправьте конфигурацию JSON и повторите попытку.

   Дополнительные сведения см. [в связанной службе по запросу Azure HDInsight](https://docs.microsoft.com/azure/data-factory/compute-linked-services#azure-hdinsight-on-demand-linked-service) .

### <a name="error-code-2310"></a>Код ошибки: 2310

- **Сообщение**:`Failed to submit Spark job. Error: '%message;'`

- **Причина**: ADF попытался создать пакет в кластере Spark с помощью API Livy (Livy/Batch), но получено сообщение об ошибке.

- **Рекомендация**: чтобы устранить проблему, следуйте указаниям в сообщении об ошибке. Если недостаточно сведений для разрешения проблемы, обратитесь к группе HDI и предоставьте ей идентификатор и идентификатор задания, которые можно найти в выходных данных выполнения действия на странице мониторинга ADF. Чтобы устранить неполадки, собирайте полный журнал пакетного задания.

   Дополнительные сведения о том, как получить полный журнал, см. в разделе [Получение полного журнала пакетного задания](https://docs.microsoft.com/rest/api/hdinsightspark/hdinsight-spark-batch-job#get-the-full-log-of-a-batch-job).

### <a name="error-code-2312"></a>Код ошибки: 2312

- **Сообщение**:`Spark job failed, batch id:%batchId;. Please follow the links in the activity run Output from ADF Monitoring page to troubleshoot the run on HDInsight Spark cluster. Please contact HDInsight support team for further assistance.`

- **Причина**: сбой задания в кластере HDInsight Spark.

- **Рекомендация**. чтобы устранить неполадки в кластере HDInsight Spark, воспользуйтесь ссылками на странице "действие запуска выходных данных" в "МОНИТОРИНГе ADF". Для получения дополнительной помощи обратитесь в службу поддержки HDInsight.

   Дополнительные сведения о том, как получить полный журнал, см. в разделе [Получение полного журнала пакетного задания](https://docs.microsoft.com/rest/api/hdinsightspark/hdinsight-spark-batch-job#get-the-full-log-of-a-batch-job).

### <a name="error-code-2313"></a>Код ошибки: 2313

- **Сообщение**:`The batch with ID '%batchId;' was not found on Spark cluster. Open the Spark History UI and try to find it there. Contact HDInsight support for further assistance.`

- **Причина**: пакет был удален в кластере HDInsight Spark.

- **Рекомендация**: Устранение неполадок в пакетах в кластере HDInsight Spark. Обратитесь за помощью в службу поддержки HDInsight. 

   Дополнительные сведения о том, как получить полный журнал, см. в статье [Получение полного журнала пакетного задания](https://docs.microsoft.com/rest/api/hdinsightspark/hdinsight-spark-batch-job#get-the-full-log-of-a-batch-job)и предоставление доступа к полному журналу с поддержкой HDInsight для получения дополнительной помощи.

### <a name="error-code-2328"></a>Код ошибки: 2328

- **Сообщение**:`Failed to create the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Причина**: в сообщении об ошибке должны отображаться сведения о том, что пошло не так.

- **Рекомендация**: сообщение об ошибке должно помочь устранить проблему.

### <a name="error-code-2329"></a>Код ошибки: 2329

- **Сообщение**:`Failed to delete the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Причина**: в сообщении об ошибке должны отображаться сведения о том, что пошло не так.

- **Рекомендация**: сообщение об ошибке должно помочь устранить проблему.

### <a name="error-code-2331"></a>Код ошибки: 2331

- **Сообщение**:`The file path should not be null or empty.`

- **Причина**: указанный путь к файлу пуст.

- **Рекомендация**: укажите путь к существующему файлу.

### <a name="error-code-2340"></a>Код ошибки: 2340

- **Сообщение**:`HDInsightOnDemand linked service does not support execution via SelfHosted IR. Your IR name is '%IRName;'. Please select an Azure IR instead.`

- **Причина**: связанная служба HDInsightOnDemand не поддерживает выполнение через селфхостед IR.

- **Рекомендация**: выберите Azure IR и повторите попытку.

### <a name="error-code-2341"></a>Код ошибки: 2341

- **Сообщение**:`HDInsight cluster URL '%clusterUrl;' is incorrect, it must be in URI format and the scheme must be 'https'.`

- **Причина**: указанный URL-адрес имеет неправильный формат.

- **Рекомендация**: Исправьте URL-адрес кластера и повторите попытку.

### <a name="error-code-2342"></a>Код ошибки: 2342

- **Сообщение**:`Failed to connect to HDInsight cluster: '%errorMessage;'.`

- **Причина**: либо указанные учетные данные неверны для кластера, либо возникла проблема с конфигурацией сети или подключением или возникли проблемы с подключением к кластеру.

- **Совет**. 
    1. Проверьте правильность учетных данных, открыв пользовательский интерфейс Ambari кластера HDInsight в браузере.
    1. Если кластер находится в виртуальной сети (VNet) и используется локальная среда IR, URL-адрес HDI должен быть частным URL-адресом в виртуальных сетей, а после имени кластера должен быть указан "-int".
    
       Например, измените `https://mycluster.azurehdinsight.net/` на `https://mycluster-int.azurehdinsight.net/`. Обратите `-int` внимание `mycluster`на After, но до`.azurehdinsight.net`
    1. Если кластер находится в виртуальной сети, используется локальная IR и используется частный URL-адрес, но подключение по-прежнему завершилось сбоем, а на виртуальной машине, где установлена среда IR, возникли проблемы с подключением к HDI. 
    
       Подключитесь к виртуальной машине, где установлена среда IR, и откройте пользовательский интерфейс Ambari в браузере. Используйте частный URL-адрес кластера. Это подключение должно работать из браузера. Если это не так, обратитесь в службу поддержки HDInsight за помощью.
    1. Если локальная среда IR не используется, кластер HDI должен быть общедоступным. Откройте пользовательский интерфейс Ambari в браузере и убедитесь, что он открыт. Если возникли проблемы с кластером или службами на нем, обратитесь в службу поддержки HDInsight за помощью.

       URL-адрес кластера HDI, используемый в связанной службе ADF, должен быть доступен для службы ADF IR (с локальным размещением или Azure) для передачи тестового подключения, а также для работы. Это состояние можно проверить, открыв URL-адрес из браузера либо из виртуальной машины, либо с любой общедоступной машины.

### <a name="error-code-2343"></a>Код ошибки: 2343

- **Сообщение**:`User name and password cannot be null or empty to connect to the HDInsight cluster.`

- **Причина**: имя пользователя или пароль пусты.

- **Рекомендация**: укажите правильные учетные данные для подключения к HDi и повторите попытку.

### <a name="error-code-2345"></a>Код ошибки: 2345

- **Сообщение**:`Failed to read the content of the hive script. Error: '%message;'`

- **Причина**: файл скрипта не существует, или ADF не удалось подключиться к расположению скрипта.

- **Рекомендация**: Убедитесь, что скрипт существует и связанная служба имеет правильные учетные данные для соединения.

### <a name="error-code-2346"></a>Код ошибки: 2346

- **Сообщение**:`Failed to create ODBC connection to the HDI cluster with error message '%message;'.`

- **Причина**: ADF попытался установить подключение к КЛАСТЕРу HDi с открытым подключением к базе данных (ODBC) и выполнить его с ошибкой.

- **Совет**. 

   1. Убедитесь, что вы правильно настроили подключение ODBC/Java Database Connectivity (JDBC).
      1. Для JDBC, если вы используете ту же виртуальную сеть, вы можете получить это подключение из:<br>
        `Hive -> Summary -> HIVESERVER2 JDBC URL`
      1. Чтобы убедиться, что настроен правильный JDBC, см. статью [Apache Hive запросов через драйвер JDBC в HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-connect-hive-jdbc-driver).
      1. Сведения об открытой базе данных (ODB) см. в разделе [учебник. запрос Apache Hive с помощью ODBC и PowerShell](https://docs.microsoft.com/azure/hdinsight/interactive-query/apache-hive-query-odbc-driver-powershell) для обеспечения правильной настройки. 
   1. Убедитесь, что Hiveserver2, Hive хранилище метаданных и Hiveserver2 Interactive активны и работают. 
   1. Проверьте пользовательский интерфейс Ambari:
      1. Убедитесь, что все службы все еще работают.
      1. В пользовательском интерфейсе Ambari просмотрите раздел оповещение на панели мониторинга.
         1. Дополнительные сведения о предупреждениях и разрешениях для оповещений см. в статье [Управление кластером и его мониторинг ](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html).
   1. Если эти действия недостаточно для устранения проблемы, обратитесь к команде Azure HDInsight.

### <a name="error-code-2347"></a>Код ошибки: 2347

- **Сообщение**:`Hive execution through ODBC failed with error message '%message;'.`

- **Причина**: файл ADF передал скрипт Hive для выполнения в кластере HDi через подключение ODBC, и на HDi произошел сбой скрипта.

- **Совет**. 

   1. Убедитесь, что вы правильно настроили подключение ODBC/Java Database Connectivity (JDBC).
      1. Для JDBC, если вы используете ту же виртуальную сеть, вы можете получить это подключение из:<br>
        `Hive -> Summary -> HIVESERVER2 JDBC URL`
      1. Чтобы убедиться, что настроен правильный JDBC, см. статью [Apache Hive запросов через драйвер JDBC в HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-connect-hive-jdbc-driver).
      1. Сведения об открытой базе данных (ODB) см. в разделе [учебник. запрос Apache Hive с помощью ODBC и PowerShell](https://docs.microsoft.com/azure/hdinsight/interactive-query/apache-hive-query-odbc-driver-powershell) для обеспечения правильной настройки. 
   1. Убедитесь, что Hiveserver2, Hive хранилище метаданных и Hiveserver2 Interactive активны и работают. 
   1. Проверьте пользовательский интерфейс Ambari:
      1. Убедитесь, что все службы все еще работают.
      1. В пользовательском интерфейсе Ambari просмотрите раздел оповещение на панели мониторинга.
         1. Дополнительные сведения о предупреждениях и разрешениях для оповещений см. в статье [Управление кластером и его мониторинг ](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html).
   1. Если эти действия недостаточно для устранения проблемы, обратитесь к команде Azure HDInsight.

### <a name="error-code-2348"></a>Код ошибки: 2348

- **Сообщение**:`The main storage has not been initialized. Please check the properties of the storage linked service in the HDI linked service.`

- **Причина**: Свойства связанной службы хранилища заданы неправильно.

- **Рекомендация**: в главной связанной службе хранилища для действий HDi поддерживаются только полные строки подключения. Убедитесь, что вы не используете авторизацию MSI или приложения.

### <a name="error-code-2350"></a>Код ошибки: 2350

- **Сообщение**:`Failed to prepare the files for the run '%jobId;'. HDI cluster: '%cluster;', Error: '%errorMessage;'`

- **Причина**: учетные данные, предоставленные для подключения к хранилищу, где должны быть расположены файлы, неверны, или файлы не существуют.

- **Рекомендация**. Эта ошибка возникает, когда ADF готовится к действиям HDi и пытается скопировать файлы в основное хранилище перед отправкой задания в HDi. Убедитесь, что файлы существуют в указанном расположении и что подключение к хранилищу указано правильно. Поскольку действия ADF HDI не поддерживают проверку подлинности MSI для учетных записей хранения, связанных с действиями HDI, убедитесь, что эти связанные службы имеют полные ключи или используют Azure Key Vault.

### <a name="error-code-2351"></a>Код ошибки: 2351

- **Сообщение**:`Could not open the file '%filePath;' in container/fileSystem '%container;'.`

- **Причина**: файл не существует по указанному пути.

- **Рекомендация**: Проверьте факт существования файла, а также убедитесь, что связанная служба со сведениями о соединении, указывающими на этот файл, имеет правильные учетные данные.

### <a name="error-code-2352"></a>Код ошибки: 2352

- **Сообщение**:`The file storage has not been initialized. Please check the properties of the file storage linked service in the HDI activity.`

- **Причина**: Свойства связанной службы хранилища файлов заданы неправильно.

- **Рекомендация**: Убедитесь, что свойства связанной службы хранилища файлов настроены правильно.

### <a name="error-code-2353"></a>Код ошибки: 2353

- **Сообщение**:`The script storage has not been initialized. Please check the properties of the script storage linked service in the HDI activity.`

- **Причина**: Свойства связанной службы хранилища скриптов заданы неправильно.

- **Рекомендация**: Убедитесь, что свойства связанной службы хранилища скриптов настроены правильно.

### <a name="error-code-2354"></a>Код ошибки: 2354

- **Сообщение**:`The storage linked service type '%linkedServiceType;' is not supported for '%executorType;' activities for property '%linkedServicePropertyName;'.`

- **Причина**: тип связанной службы хранилища не поддерживается этим действием.

- **Рекомендация**: Убедитесь, что выбранная связанная служба имеет один из поддерживаемых типов для действия. Действия HDI поддерживают связанные службы AzureBlobStorage и Азуреблобфсстораже.

   Дополнительные сведения см. в статье [Сравнение вариантов хранения для использования с кластерами Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options) .

### <a name="error-code-2355"></a>Код ошибки: 2355

- **Сообщение**:`The '%value' provided for commandEnvironment is incorrect. The expected value should be an array of strings where each string has the format CmdEnvVarName=CmdEnvVarValue.`

- **Причина**: указано неверное `commandEnvironment` значение для.

- **Рекомендация**: Убедитесь, что указанное значение аналогично следующему:
 
    ``` \"commandEnvironment\": [
    \"variableName=variableValue\"
    ]
    ```

    Также убедитесь, что каждая переменная присутствует в списке только один раз.

### <a name="error-code-2356"></a>Код ошибки: 2356

- **Сообщение**:`The commandEnvironment already contains a variable named '%variableName;'.`

- **Причина**: указано неверное `commandEnvironment` значение для.

- **Рекомендация**: Убедитесь, что указанное значение аналогично следующему:
 
    ``` \"commandEnvironment\": [
    \"variableName=variableValue\"
    ]
    ```

    Также убедитесь, что каждая переменная присутствует в списке только один раз.

### <a name="error-code-2357"></a>Код ошибки: 2357

- **Сообщение**:`The certificate or password is wrong for ADLS Gen 1 storage.`

- **Причина**: указанные учетные данные неверны.

- **Рекомендация**: Убедитесь, что сведения о подключении в ADLS Gen 1 связаны со службой, и убедитесь, что проверка соединения выполнена.

### <a name="error-code-2358"></a>Код ошибки: 2358

- **Сообщение**:`The value '%value;' for the required property 'TimeToLive' in the on demand HDInsight linked service '%linkedServiceName;' has invalid format. It should be a timespan between '00:05:00' and '24:00:00'.`

- **Причина**: указанное значение для обязательного свойства `TimeToLive` имеет недопустимый формат. 

- **Рекомендация**: обновите значение до предложенного диапазона и повторите попытку.

### <a name="error-code-2359"></a>Код ошибки: 2359

- **Сообщение**:`The value '%value;' for the property 'roles' is invalid. Expected types are 'zookeeper', 'headnode', and 'workernode'.`

- **Причина**: для свойства `roles` указано недопустимое значение.

- **Рекомендация**: обновите значение, чтобы оно было одним из предложений, и повторите попытку.

### <a name="error-code-2360"></a>Код ошибки: 2360

- **Сообщение**:`The connection string in HCatalogLinkedService is invalid. Encountered an error while trying to parse: '%message;'.`

- **Причина**: указанная строка подключения для `HCatalogLinkedService` недопустима.

- **Рекомендация**: измените значение на правильную строку подключения SQL Azure и повторите попытку.

### <a name="error-code-2361"></a>Код ошибки: 2361

- **Сообщение**:`Failed to create on demand HDI cluster. Cluster name is '%clusterName;'.`

- **Причина**: сбой создания кластера, и ADF не получил ошибку из службы HDInsight.

- **Рекомендация**: откройте портал Azure и попробуйте найти ресурс HDi с указанным именем, а затем проверьте состояние подготовки. Для получения дополнительной помощи обратитесь в службу поддержки HDInsight.

### <a name="error-code-2362"></a>Код ошибки: 2362

- **Сообщение**:`Only Azure Blob storage accounts are supported as additional storages for HDInsight on demand linked service.`

- **Причина**: предоставленное дополнительное хранилище не было хранилищем BLOB-объектов Azure.

- **Рекомендация**. Укажите учетную запись хранилища BLOB-объектов Azure в качестве дополнительного хранилища для связанной службы HDInsight по запросу.

## <a name="web-activity"></a>Веб-действие

### <a name="error-code-2128"></a>Код ошибки: 2128

- **Сообщение**:`No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Причина**. Эта проблема связана с подключением к сети, ошибкой DNS, проверкой сертификата сервера или истечением времени ожидания.

- **Рекомендация**: Убедитесь, что конечная точка, которую вы пытаетесь навести, отвечает на запросы. Вы можете использовать такие средства, как **Fiddler/POST**.

### <a name="error-code-2108"></a>Код ошибки: 2108

- **Сообщение**:`Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Причина**: не удалось выполнить запрос из-за базовой проблемы, например сетевого подключения, сбоя DNS, проверки сертификата сервера или истечения времени ожидания.

- **Рекомендация**: для проверки запроса используйте Fiddler/POST.

#### <a name="more-details"></a>Дополнительные сведения
Чтобы использовать **Fiddler** для создания сеанса HTTP отслеживаемого веб-приложения, выполните следующие действия.

1. Скачайте, установите и откройте [Fiddler](https://www.telerik.com/download/fiddler).

1. Если веб-приложение использует протокол HTTPS, перейдите в **меню Сервис** > **Fiddler параметры** > **HTTPS**.

   1. На вкладке HTTPS выберите **запись HTTPS подключение** и **расшифровка трафика HTTPS**.

      ![Параметры Fiddler](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. Если приложение использует сертификаты TLS/SSL, добавьте сертификат Fiddler на устройство.

   Выберите " **Сервис** > **Fiddler параметры** > **HTTPS** > **действия** > **экспорт корневого сертификата на Рабочий стол**".

1. Отключите захват, перейдя к**трафику записи** **файлов** > . Или нажмите клавишу **F12**.

1. Очистите кэш браузера, чтобы все кэшированные элементы были удалены и их нужно было снова скачать.

1. Создайте запрос:

1. Перейдите на вкладку **Composer** .

   1. Задайте метод HTTP и URL-адрес.
 
   1. При необходимости добавьте заголовки и текст запроса.

   1. Выберите **выполнить**.

1. Включите повторную запись трафика и завершите проблемную транзакцию на странице.

1. Перейдите к: **файл** > **сохранить** > **все сеансы**.

Дополнительные сведения см. в статье [Приступая к работе с Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler).

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительных сведений об устранении неполадок воспользуйтесь следующими ресурсами:

* [Блог фабрики данных](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [Запросы функций фабрики данных](https://feedback.azure.com/forums/270578-data-factory)
* [Форум Stack Overflow для фабрики данных](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [Сведения о фабрике данных в Twitter](https://twitter.com/hashtag/DataFactory)
* [Видео по Azure](https://azure.microsoft.com/resources/videos/index/)
* [Форум MSDN](https://social.msdn.microsoft.com/Forums/home)
