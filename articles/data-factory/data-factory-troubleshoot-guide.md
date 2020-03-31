---
title: Устранение проблем в Фабрике данных Azure (ru) Документы Майкрософт
description: Узнайте, как устранить неполадки внешних действий управления на Фабрике данных Azure.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: e284060893e00ed7459edd0d1a03075c813dc5b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065380"
---
# <a name="troubleshoot-azure-data-factory"></a>Фабрика данных по устранению неполадок Azure

В этой статье рассматриваются общие методы устранения неполадок для деятельности внешнего управления на фабрике данных Azure.

## <a name="connector-and-copy-activity"></a>Активность соединения и копирования

Для проблем с разъемом, например, с ошибкой, при меняйкой, используя активность копирования, обратитесь к [Connectors.](connector-troubleshoot-guide.md)
  

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code--3200"></a>Код ошибки: 3200

- **Сообщение**: Ошибка 403.

- **Причина**:`The Databricks access token has expired.`

- **Рекомендация**: По умолчанию токен доступа Azure Databricks действителен в течение 90 дней. Создайте новый маркер и обновите связанную службу.


### <a name="error-code--3201"></a>Код ошибки: 3201

- **Сообщение**:`Missing required field: settings.task.notebook_task.notebook_path.`

- **Причина**:`Bad authoring: Notebook path not specified correctly.`

- **Рекомендация**: Укажите путь записного книжки в деятельности Databricks.

<br/>  

- **Сообщение**:`Cluster... does not exist.`

- **Причина**:`Authoring error: Databricks cluster does not exist or has been deleted.`

- **Рекомендация**: Убедитесь, что кластер Databricks существует.

<br/>  

- **Сообщение**:`Invalid Python file URI... Please visit Databricks user guide for supported URI schemes.`

- **Причина**:`Bad authoring.`

- **Рекомендация**: Укажите либо абсолютные пути `dbfs:/folder/subfolder/foo.py` для схем адресации рабочего пространства, либо для файлов, хранящихся в файловой системе Databricks.

<br/>  

- **Сообщение**:`{0} LinkedService should have domain and accessToken as required properties.`

- **Причина**:`Bad authoring.`

- **Рекомендация**: Проверьте [определение связанного сервиса.](compute-linked-services.md#azure-databricks-linked-service)

<br/>  

- **Сообщение**:`{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **Причина**:`Bad authoring.`

- **Рекомендация**: Проверьте [определение связанного сервиса.](compute-linked-services.md#azure-databricks-linked-service)

<br/>  

- **Сообщение**:`Node type Standard_D16S_v3 is not supported. Supported node types:   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3,   Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2,   Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3,   Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2,   Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2,   Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3,   Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s,   Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2,   Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2,   Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3,   Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2,   Standard_L64s_v2, Standard_L80s_v2.`

- **Причина**:`Bad authoring.`

- **Рекомендация**: Обратитесь к сообщению об ошибке.

<br/>

### <a name="error-code--3202"></a>Код ошибки: 3202

- **Сообщение**:`There were already 1000 jobs created in past 3600 seconds, exceeding rate limit:   1000 job creations per 3600 seconds.`

- **Причина**:`Too many Databricks runs in an hour.`

- **Рекомендация**: Проверьте все конвейеры, которые используют это рабочее пространство Databricks, для получения рабочих мест.  Если конвейеры запускали слишком много Databricks в совокупности, перенос некоторых конвейеров в новое рабочее пространство.

<br/>  

- **Сообщение**:`Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **Причина**:`Authoring error: No value provided for the parameter.`

- **Рекомендация**: Осмотрите конвейер JSON и убедитесь, что все параметры в блокноте baseParameters указывают непустое значение.

<br/>  

- **Сообщение** `User: `: SimpleUserКонтекст (userId)..., имя ,user@company.comorgId...»` is not   authorized to access cluster.`

- **Причина**: Пользователь, сгенеравший токен доступа, не имеет доступа к кластеру Databricks, указанному в связанной службе.

- **Рекомендация**: Убедитесь, что пользователь имеет необходимые разрешения в рабочей области.


### <a name="error-code--3203"></a>Код ошибки: 3203

- **Сообщение**:`The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **Причина**: Кластер был завершен. Для интерактивных кластеров это может быть условием гонки.

- **Рекомендация**: Лучший способ избежать этой ошибки — использовать кластеры вакансий.


### <a name="error-code--3204"></a>Код ошибки: 3204

- **Сообщение**:`Job execution failed.`

- **Причиной**: Сообщения об ошибках указывают на различные проблемы, такие как неожиданное состояние кластера или определенное действие. Чаще всего сообщение об ошибке не отображается вообще.

- **Рекомендация**: N/A
            

## <a name="azure-data-lake-analytics"></a>Аналитика озера данных Azure

Следующая таблица применяется к U-S'L.
      
### <a name="error-code--2709"></a>Код ошибки: 2709

- **Сообщение**:`The access token is from the wrong tenant.`

- **Причина**: Неверный активный каталог Azure (Azure AD) арендатор.

- **Рекомендация**: Неверный активный каталог Azure (Azure AD) арендатор.

<br/>

- **Сообщение**:`We cannot accept your job at this moment. The maximum number of queued jobs for   your account is 200. `

- **Причина**: Эта ошибка вызвана регулированием на Data Lake Analytics.

- **Рекомендация**: Сократите количество представленных заданий в Data Lake Analytics путем изменения триггеров Data Factory и параметров параллелизма в действиях. Или увеличить лимиты на Data Lake Analytics.

<br/>  

- **Сообщение**:`This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **Причина**: Эта ошибка вызвана регулированием на Data Lake Analytics.

- **Рекомендация**: Сократите количество представленных заданий в Data Lake Analytics путем изменения триггеров Data Factory и параметров параллелизма в действиях. Или увеличить лимиты на Data Lake Analytics.


### <a name="error-code--2705"></a>Код ошибки: 2705

- **Сообщение**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **Причина**: Основной сервис или сертификат не имеют доступа к файлу в хранилище.

- **Рекомендация**: Убедитесь, что основной сервис или сертификат, который пользователь предоставляет для заданий Data Lake Analytics, имеет доступ к учетной записи Data Lake Analytics и экземпляру хранилища данных по умолчанию из корневой папки.


### <a name="error-code--2711"></a>Код ошибки: 2711

- **Сообщение**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **Причина**: Основной сервис или сертификат не имеют доступа к файлу в хранилище.

- **Рекомендация**: Убедитесь, что основной сервис или сертификат, который пользователь предоставляет для заданий Data Lake Analytics, имеет доступ к учетной записи Data Lake Analytics и экземпляру хранилища данных по умолчанию из корневой папки.

<br/>  

- **Сообщение**:`Cannot find the 'Azure Data Lake Store' file or folder.`

- **Причина**: Путь к файлу U-S'L неправильный, или связанные учетные данные службы не имеют доступа.

- **Рекомендация**: Проверьте путь и учетные данные, предоставленные в связанном сервисе.


### <a name="error-code--2704"></a>Код ошибки: 2704

- **Сообщение**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **Причина**: Основной сервис или сертификат не имеют доступа к файлу в хранилище.

- **Рекомендация**: Убедитесь, что основной сервис или сертификат, который пользователь предоставляет для заданий Data Lake Analytics, имеет доступ к учетной записи Data Lake Analytics и экземпляру хранилища данных по умолчанию из корневой папки.


### <a name="error-code--2707"></a>Код ошибки: 2707

- **Сообщение**:`Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and   'DataLakeAnalyticsUri'.`

- **Причина**: Учетная запись Data Lake Analytics в связанной службе неверна.

- **Рекомендация**: Убедитесь, что предоставляется правильная учетная запись.


### <a name="error-code--2703"></a>Код ошибки: 2703

- **Сообщение**:`Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error   Id:").`

- **Причина**: Ошибка от анализа озера данных.

- **Рекомендация**: Ошибка, как пример, означает, что задание было отправлено в Data Lake Analytics, и скрипт там не удалось. Исследуйте в данных лейк-аналитики. На портале перейдите на учетную запись Data Lake Analytics и ищите работу, используя идентификатор активности Data Factory (не идентификатор запуска конвейера). Задание там предоставляет больше информации об ошибке и поможет вам устранить неполадки. Если разрешение не ясно, свяжитесь с группой поддержки Data Lake Analytics и предоставьте URL-адрес задания, который включает имя учетной записи и идентификатор задания.
          

## <a name="azure-functions"></a>Функции Azure

### <a name="error-code--3602"></a>Код ошибки: 3602

- **Сообщение**:`Invalid HttpMethod: '%method;'.`

- **Причина**: Метод http, указанный в полезной нагрузке активности, не поддерживается функцией Функции Azure.

- **Рекомендация**: Методы http, которые поддерживаются PUT, POST, GET, DELETE, OPTIONS, HEAD, и TRACE.


### <a name="error-code--3603"></a>Код ошибки: 3603

- **Сообщение**:`Response Content is not a valid JObject.`

- **Функция**Azure, которая была вызвана, не вернула полезную нагрузку JSON в ответ. Функция функции ADF Azure поддерживает только содержимое ответа JSON.

- **Рекомендация**: Обновление функции Azure для возврата действительной полезной нагрузки JSON, например, функция\"\"C-версии может вернуться (ActionResult)new OkObjectResult ("Идентификатор:\"123\"");


### <a name="error-code--3606"></a>Код ошибки: 3606

- **Сообщение:** Функции Azure отсутствует ключ функции функции.

- **Определение**функции Azure — не завершено.

- **Рекомендация**: Пожалуйста, проверьте входной функции AzureFunction JSON определение имеет свойство под названием 'functionKey'.


### <a name="error-code--3607"></a>Код ошибки: 3607

- **Сообщение**:`Azure function activity missing function name.`

- **Определение**функции Azure — не завершено.

- **Рекомендация**: Пожалуйста, проверьте входной функции AzureFunction JSON определение имеет свойство под названием 'functionName'.


### <a name="error-code--3608"></a>Код ошибки: 3608

- **Сообщение**:`Call to provided Azure function '%FunctionName;' failed with status-'%statusCode;' and message - '%message;'.`

- **Причина**: Детали функции Azure в определении активности могут быть неверными.

- **Рекомендация**: Исправьте детали функции лазури и повторите попытку.


### <a name="error-code--3609"></a>Код ошибки: 3609

- **Сообщение**:`Azure function activity missing functionAppUrl.`

- **Определение**функции Azure — не завершено.

- **Рекомендация**: Пожалуйста, проверьте входной деятельности AzureFunction JSON определение имеет свойство под названием 'functionAppUrl'.


### <a name="error-code--3610"></a>Код ошибки: 3610

- **Сообщение**:`There was an error while calling endpoint.`

- **Причина**: Функция URL может быть неправильным.

- **Рекомендация**: Пожалуйста, убедитесь, что значение для 'functionAppUrl' в деятельности JSON является правильным и попробуйте еще раз.


### <a name="error-code--3611"></a>Код ошибки: 3611

- **Сообщение**:`Azure function activity missing Method in JSON.`

- **Определение**функции Azure — не завершено.

- **Рекомендация**: Пожалуйста, проверьте входной функции AzureFunction JSON определение имеет свойство под названием "метод".


### <a name="error-code--3612"></a>Код ошибки: 3612

- **Сообщение**:`Azure function activity missing LinkedService definition in JSON.`

- **Определение**функции Azure — не завершено.

- **Рекомендация**: Пожалуйста, проверьте входной функции AzureFunction JSON определение связали детали службы.



## <a name="azure-machine-learning"></a>Машинное обучение Azure

### <a name="error-code--4101"></a>Код ошибки: 4101

- **Сообщение**:`AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **Причина**: Плохой формат или отсутствует определение свойства '%propertyName;'.

- **Рекомендация**: Пожалуйста, проверьте, имеет ли действие '%activityName;' свойство '%propertyName;', определенное с правильными данными.


### <a name="error-code--4110"></a>Код ошибки: 4110

- **Сообщение**:`AzureMLExecutePipeline activity missing LinkedService definition in JSON.`

- **Причина**: Определение активности AzureMLExecutePipeline не завершено.

- **Рекомендация**: Пожалуйста, проверьте, связано ли определение ввода AzureMLExecutePipeline, что связано с деталями службы.


### <a name="error-code--4111"></a>Код ошибки: 4111

- **Сообщение**:`AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **Причина**: Неправильное определение активности.

- **Рекомендация**: Пожалуйста, проверьте, правильно ли ввод azureMLExecutePipeline действия JSON определение исправлено связанных деталей службы.


### <a name="error-code--4112"></a>Код ошибки: 4112

- **Сообщение**:`AzureMLService linked service has invalid value for property '%propertyName;'.`

- **Причина**: Плохой формат или отсутствует определение свойства '%propertyName;'.

- **Рекомендация**: Пожалуйста, проверьте, есть ли у связанной службы свойство '%propertyName;', определяемое с правильными данными.


### <a name="error-code--4121"></a>Код ошибки: 4121

- **Сообщение**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Причина:** Срок действия учетных данных, используемых для доступа к машинному обучению Azure, истек.

- **Рекомендация**: Пожалуйста, убедитесь, что учетные данные действительны и повторно


### <a name="error-code--4122"></a>Код ошибки: 4122

- **Сообщение**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Причина**: Учетные данные, предоставленные в службе связанного с машинным обучением Azure, недействительны или не имеют разрешения на операцию.

- **Рекомендация**: Пожалуйста, проверьте учетные данные в Службе связи является действительным и имеет разрешение на доступ к машинному обучению Azure.


### <a name="error-code--4123"></a>Код ошибки: 4123

- **Сообщение**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Причина**: Свойства деятельности, такие как параметры конвейера, недействительны для конвейера Azure ML.

- **Рекомендация**: Пожалуйста, проверьте значение свойств активности в соответствии с ожидаемой полезной нагрузкой опубликованного конвейера Azure ML, указанного в Linked Service.


### <a name="error-code--4124"></a>Код ошибки: 4124

- **Сообщение**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Причина**: Опубликованная конечная точка конвейера Azure ML не существует.

- **Рекомендация**: Пожалуйста, проверьте опубликованную конечную точку конвейера Azure Machine Learning, указанную в Linked Service, существует в Azure Machine Learning.


### <a name="error-code--4125"></a>Код ошибки: 4125

- **Сообщение**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Причина**: Ошибка сервера в машинном обучении Azure.

- **Рекомендация**: Пожалуйста, повторите позже. Если проблема остается, обратитесь за помощью к группе машинного обучения Azure.


### <a name="error-code--4126"></a>Код ошибки: 4126

- **Сообщение**:`Azure ML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in Azure Machine Learning for more error logs.`

- **Причина:** запуск конвейера Azure ML не удался.

- **Рекомендация**: Пожалуйста, зарегистрируйтесь в Azure Machine Learning для получения дополнительных журналов ошибок и исправления конвейера ML.



## <a name="common"></a>Распространенные

### <a name="error-code--2103"></a>Код ошибки: 2103

- **Сообщение**:`Please provide value for the required property '%propertyName;'.`

- **Причина**: Значение для свойства не было предоставлено, однако это требуется в сценарии.

- **Рекомендация**: Предоставьте значение из сообщения и повторите попытку.


### <a name="error-code--2104"></a>Код ошибки: 2104

- **Сообщение**:`The type of the property '%propertyName;' is incorrect.`

- **Причина**: Тип предоставленного свойства не так, как ожидалось.

- **Рекомендация**: Пожалуйста, исправьте тип свойства и повторите попытку.


### <a name="error-code--2105"></a>Код ошибки: 2105

- **Сообщение**:`An invalid json is provided for property '%propertyName;'. Encountered an error while trying to parse: '%message;'.`

- **Причина**: Значение для свойства является недействительным или не имеет ожидаемого формата.

- **Рекомендация**: Пожалуйста, просмотрите документацию для свойства и убедитесь, что при условии, что при условии, значение ожидаемого формата и типа.


### <a name="error-code--2106"></a>Код ошибки: 2106

- **Сообщение**:`The storage connection string is invalid. %errorMessage;`

- **Причина**: Строка соединения для хранилища недействительна или имеет неправильный формат.

- **Рекомендация**: Пожалуйста, перейдите на портал Azure, найдите хранилище, скопируйте строку соединения и вставьте в связанную службу и повторите попытку.


### <a name="error-code--2108"></a>Код ошибки: 2108

- **Сообщение**:`Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Причина**: Запрос не удалось из-за основной проблемы, такие как подключение к сети, сбой DNS, проверка сертификата сервера или тайм-аут.

- **Рекомендация**: Используйте Скрипача/Почтальона для проверки запроса.


### <a name="error-code--2110"></a>Код ошибки: 2110

- **Сообщение**:`The linked service type '%linkedServiceType;' is not supported for '%executorType;' activities.`

- **Причина**: Связанная служба, указанная в действии, была неправильной.

- **Рекомендация**: Пожалуйста, убедитесь, что связанный тип службы является одним из поддерживаемых типов для деятельности. Например, для деятельности ИРЧП связанный тип службы может быть HDInsight или HDInsightOnDemand.


### <a name="error-code--2111"></a>Код ошибки: 2111

- **Сообщение**:`The type of the property '%propertyName;' is incorrect. The expected type is %expectedType;.`

- **Причина**: Тип предоставленного свойства не так, как ожидалось.

- **Рекомендация**: Пожалуйста, исправьте тип свойства и повторите попытку.


### <a name="error-code--2112"></a>Код ошибки: 2112

- **Сообщение**:`The cloud type is unsupported or could not be determined for storage from the EndpointSuffix '%endpointSuffix;'.`

- **Причина**: Тип облака не поддерживается или не может быть определен для хранения из EndpointSuffix.

- **Рекомендация**: Пожалуйста, используйте хранилище в другом облаке и повторите попытку.


### <a name="error-code--2128"></a>Код ошибки: 2128

- **Сообщение**:`No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Причина**: Подключение к сети, сбой DNS, проверка сертификата сервера или тайм-аут.

- **Рекомендация**: Проверка того, что конечная точка, которую вы пытаетесь попасть, отвечает на запросы. Вы можете использовать такие инструменты, как Скрипач / Почтальон.



## <a name="custom"></a>Другой

Следующая таблица относится к пакету Azure.
      
### <a name="error-code--2500"></a>Код ошибки: 2500

- **Сообщение**:`Hit unexpected exception and execution failed.`

- **Причина**:`Can't launch command, or the program returned an error code.`

- **Рекомендация**: Убедитесь, что исполняемый файл существует. Если программа запущена, убедитесь, что *stdout.txt* и *stderr.txt* были загружены на учетную запись хранения. Это хорошая практика, чтобы излучать обильные журналы в коде для отладки.


### <a name="error-code--2501"></a>Код ошибки: 2501

- **Сообщение**:`Cannot access user batch account; please check batch account settings.`

- **Причина**: Неправильный ключ доступа к пакету или имя пула.

- **Рекомендация**: Проверьте имя пула и ключ доступа к пакету в связанной службе.


### <a name="error-code--2502"></a>Код ошибки: 2502

- **Сообщение**:`Cannot access user storage account; please check storage account settings.`

- **Причина**: Неправильное имя учетной записи хранения или ключ доступа.

- **Рекомендация**: Проверить имя учетной записи хранилища и ключ доступа в связанной службе.


### <a name="error-code--2504"></a>Код ошибки: 2504

- **Сообщение**:`Operation returned an invalid status code 'BadRequest'.`

- **Причина**: Слишком много файлов в папкеPath пользовательской деятельности. Общий размер ресурсаФайлы не могут быть больше 32 768 символов.

- **Рекомендация**: Удалите ненужные файлы. Или застегните их и добавьте команду unzip, чтобы извлечь их. Например, использование`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;  $folder\yourProgram.exe`


### <a name="error-code--2505"></a>Код ошибки: 2505

- **Сообщение**:`Cannot create Shared Access Signature unless Account Key credentials are used.`

- **Причина**: Пользовательские действия поддерживают только учетные записи хранения, которые используют ключ доступа.

- **Рекомендация**: Обратитесь к описанию ошибки.


### <a name="error-code--2507"></a>Код ошибки: 2507

- **Сообщение**:`The folder path does not exist or is empty: ...`

- **Причина**: Файлы не находятся в учетной записи хранилища на указанном пути.

- **Рекомендация**: Путь папки должен содержать исполняемые файлы, которые вы хотите запустить.


### <a name="error-code--2508"></a>Код ошибки: 2508

- **Сообщение**:`There are duplicate files in the resource folder.`

- **Причина**: Несколько файлов с тем же именем находятся в разных субфайдерах папокPath.

- **Рекомендация**: Пользовательские действия сгладить структуру папки под папкуPath.  Если вам необходимо сохранить структуру папки, застегните файлы и извлеките их в Azure Batch с помощью команды unzip. Например, использование`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;   $folder\yourProgram.exe`


### <a name="error-code--2509"></a>Код ошибки: 2509

- **Сообщение**:`Batch   url ... is invalid; it must be in Uri format.`

- **Причина**: ПакетURLы должны быть похожи на`https://mybatchaccount.eastus.batch.azure.com`

- **Рекомендация**: Обратитесь к описанию ошибки.


### <a name="error-code--2510"></a>Код ошибки: 2510

- **Сообщение**:`An   error occurred while sending the request.`

- **Причина**: URL-адрес пакета недействителен.

- **Рекомендация**: Проверить URL-адрес партии.
            

## <a name="hdinsight"></a>HDInsight

### <a name="error-code--200"></a>Код ошибки: 200

- **Сообщение**:`Unexpected error happened: '%error;'.`

- **Причина**: Существует внутренняя проблема службы.

- **Рекомендация**: Пожалуйста, свяжитесь со поддержкой ADF для получения дополнительной помощи.


### <a name="error-code--201"></a>Код ошибки: 201

- **Сообщение**:`JobType %jobType; is not found.`

- **Причина**: Существует новый тип задания, который не поддерживается ADF.

- **Рекомендация**: Пожалуйста, свяжитесь с группой поддержки ADF для получения дополнительной помощи.


### <a name="error-code--202"></a>Код ошибки: 202

- **Сообщение**:`Failed to create on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **Причина**: Сообщение об ошибке должно отображать детали того, что пошло не так.

- **Рекомендация**: Сообщение об ошибке должно помочь устранить проблему. Если нет достаточной информации, пожалуйста, свяжитесь со поддержкой ADF для получения дополнительной помощи.


### <a name="error-code--203"></a>Код ошибки: 203

- **Сообщение**:`Failed to delete on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **Причина**: Сообщение об ошибке должно отображать детали того, что пошло не так.

- **Рекомендация**: Сообщение об ошибке должно помочь устранить проблему. Если нет достаточной информации, пожалуйста, свяжитесь со поддержкой ADF для получения дополнительной помощи.


### <a name="error-code--204"></a>Код ошибки: 204

- **Сообщение**:`The resumption token is missing for runId '%runId;'.`

- **Причина**: Существует внутренняя проблема службы.

- **Рекомендация**: Пожалуйста, свяжитесь со поддержкой ADF для получения дополнительной помощи.


### <a name="error-code--205"></a>Код ошибки: 205

- **Сообщение**:`Failed to prepare cluster for LinkedService '%linkedServiceName;', the current resource status is '%status;'.`

- **Причина**: Была ошибка при создании иБП по кластеру спроса.

- **Рекомендация**: Пожалуйста, свяжитесь со поддержкой ADF для получения дополнительной помощи.


### <a name="error-code--206"></a>Код ошибки: 206

- **Сообщение**:`The batch ID for Spark job is invalid. Please retry your job, and if the problem persists, contact the ADF support for further assistance.`

- **Причина**: Была внутренняя проблема с службой, которая вызвала это.

- **Рекомендация**: Это может быть переходной проблемой. Пожалуйста, повторите свою работу, и если проблема сохраняется, обратитесь в службу поддержки ADF для получения дополнительной помощи.


### <a name="error-code--207"></a>Код ошибки: 207

- **Сообщение**:`Could not determine the region from the provided storage account. Please try using another primary storage account for the on demand HDI or contact ADF support team and provide the activity run ID.`

- **Причина**: При попытке определить область из основного счета хранения возникла внутренняя ошибка.

- **Рекомендация**: Попробуйте другое хранилище. В случае, если это неприемлемое решение, обратитесь в группу поддержки ADF для получения дополнительной помощи.


### <a name="error-code--208"></a>Код ошибки: 208

- **Сообщение**:`Service Principal or the MSI authenticator are not instantiated. Please consider providing a Service Principal in the HDI on demand linked service which has permissions to create an HDInsight cluster in the provided subscription and try again. In case if this is not an acceptable solution, contact ADF support team for further assistance.`

- **Причина**: Была внутренняя ошибка при попытке прочитать директор службы или мгновенной проверки подлинности MSI.

- **Рекомендация**: Пожалуйста, рассмотрите возможность предоставления директора службы, который имеет разрешения на создание кластера HDInsight в предоставленной подписке, и повторите попытку. В случае, если это не является приемлемым решением, обратитесь в группу поддержки ADF для получения дополнительной помощи.


### <a name="error-code--2300"></a>Код ошибки: 2300

- **Сообщение**:`Failed to submit the job '%jobId;' to the cluster '%cluster;'. Error: %errorMessage;.`

<br>

- **Причина**: Когда сообщение об ошибке содержит сообщение, похожее на "Удаленное имя не может быть решено.", это может означать, что предоставленный кластер URI недействителен.


- **Рекомендация**: Убедитесь, что кластер не был удален и что предоставленный URI является правильным. При открытии URI в браузере вы должны увидеть интерфейс Ambari. Если кластер находится в виртуальной сети, URI должен быть частным URI. Чтобы открыть его, используйте VM, который является частью той же виртуальной сети. Дополнительные сведения см. в разделе [this](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#directly-connect-to-apache-hadoop-services).
                  

<br>

- **Причина**: Когда сообщение об ошибке содержит сообщение, похожее на 'Задача была отменена.', это означает, что отправка задания приурочена.

- **Рекомендация**: Проблема может быть либо общее подключение HDInsight или подключение к сети. Сначала подтвердите, что интерфейс HDInsight Ambari доступен в любом браузере. Подтвердите, что ваши учетные данные по-прежнему действительны. Если вы используете самостоятельно ездовые интегрированные время выполнения (ИК), убедитесь, что сделать это с VM или машины, где самостоятельно размещена ИК установлен. Затем попробуйте отправить задание с фабрики данных еще раз. Если он все еще не удается, обратитесь в группу data Factory для поддержки.

<br>

- **Причина**: Когда сообщение об ошибке содержит сообщение, похожее на "Пользователь-админ заблокирован в Ambari" или "Несанкционированный: имя пользователя Ambari или пароль неверны", это означает, что учетные данные для HDInsight неверны или истек.

- **Рекомендация**: Исправьте учетные данные и передислоцировать связанную службу. Сначала убедитесь, что учетные данные работают на HDInsight, открыв кластер URI в любом браузере и пытаясь войти в систему. Если учетные данные не работают, их можно сбросить с портала Azure.

<br>

- **Причина**: Когда сообщение об ошибке содержит сообщение, похожее на '502 - Веб-сервер получил недействительный ответ, действуя в качестве шлюза или прокси-сервера', эта ошибка возвращается службой HDInsight.


- **Рекомендация**: Просмотрите документацию по устранению https://hdinsight.github.io/ambari/ambari-ui-502-error.html https://hdinsight.github.io/spark/spark-thriftserver-errors.htmlнеполадок Azure HDInsight, например, https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502, .
                  

<br>

- **Причина**: Сообщение об ошибке содержит сообщение, похожее на "Не в состоянии обслуживать запрос на работу, так как служба Templeton занята слишком большим количеством запросов на работу" или "Очередь root.joblauncher уже имеет 500 приложений, не может принять представление приложения", это означает, что слишком много заданий в настоящее время представлены HDInsight в то же время.

- **Рекомендация**: Рассмотрите возможность ограничения числа одновременных заданий, представленных в HDInsight. Ссылайтесь на параллелизм активности Data Factory, если задания представляются тем же действием. Измените триггеры таким образом, чтобы одновременно ежевременные запуски трубопроводов были распределены с течением времени. Обратитесь к документации HDInsight, чтобы настроить Templeton.parallellism.job.submit, как следует из ошибки.


### <a name="error-code--2301"></a>Код ошибки: 2301

- **Сообщение**:`Could not get the status of the application '%physicalJobId;' from the HDInsight service. Received the following error: %message;. Please refer to HDInsight troubleshooting documentation or contact their support for further assistance.`

- **Причина**: кластер HDInsight или служба имеет проблемы.


- **Рекомендация**: Эта ошибка происходит, когда ADF не получает ответа от кластера HDInsight при попытке получить статус работа. Это может быть причиной проблем на самом кластере, или служба HDInsight может иметь сбой. Пожалуйста, обратитесь к https://docs.microsoft.com/azure/hdinsight/hdinsight-troubleshoot-guideДОКУМЕНТАЦИи HDInsight устранения неполадок в , или связаться с их поддержкой для дальнейшей помощи.
                


### <a name="error-code--2302"></a>Код ошибки: 2302

- **Сообщение**:`Hadoop job failed with exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Contact HDInsight team for further support.`

- **Причина**: Задание было представлено кластеру ИРЧП и не удалось там.

- **Рекомендация**: Следуйте ссылке журналов Yarn в выходе выполнения действия и ищите ошибки в выходе ИРЧП. При необходимости обратитесь за поддержкой к команде HDInsight.


### <a name="error-code--2303"></a>Код ошибки: 2303

- **Сообщение**:`Hadoop job failed with transient exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Try again or contact HDInsight team for further support.`

- **Причина**: Задание было представлено кластеру ИРЧП и не удалось там.

- **Рекомендация**: Следуйте ссылке журналов Yarn в выходе выполнения действия и ищите ошибки в выходе ИРЧП. Повторите попытку или при необходимости обратитесь в группу ПОДДЕРЖКи HDInsight.


### <a name="error-code--2304"></a>Код ошибки: 2304

- **Сообщение**:`MSI authentication is not supported on storages for HDI activities.`

- **Причина**: Служба, связанная с хранением (s), используемая в службе, связанной с ИРЧП, или деятельности ИРЧП настроена с проверкой подлинности MSI, которая не поддерживается.

- **Рекомендация**: Пожалуйста, предоставьте полные строки подключения для учетных записей хранения, используемых в службе, связанной с ИРЧП, или деятельности ИПЧП.


### <a name="error-code--2305"></a>Код ошибки: 2305

- **Сообщение**:`Failed to initialize the HDInsight client for the cluster '%cluster;'. Error: '%message;'`

- **Причина**: Информация о подключении для кластера ИРЧП неверна, при условии, что пользователь не имеет разрешений на выполнение требуемых действий, или служба HDInsight несколько вопросов, отвечая на запросы от ADF.

- **Рекомендация**: Пожалуйста, убедитесь, что информация о пользователе является правильной. Также убедитесь, что интерфейс Ambari для кластера ИР-класса может быть открыт в браузере из VM, где ИК установлен на случай самостоятельного ИК, или может быть открыт с любой машины в случае Azure IR.


### <a name="error-code--2306"></a>Код ошибки: 2306

- **Сообщение**:`An invalid json is provided for script action '%scriptActionName;'. Error: '%message;'`

- **Причина**: Json, предусмотренный для действия скрипта, недействителен.


- **Рекомендация**: Сообщение об ошибке должно помочь определить проблему. Пожалуйста, исправьте конфигурацию json и повторите попытку. Проверьте https://docs.microsoft.com/azure/data-factory/compute-linked-services#azure-hdinsight-on-demand-linked-service для получения дополнительной информации.
                


### <a name="error-code--2310"></a>Код ошибки: 2310

- **Сообщение**:`Failed to submit Spark job. Error: '%message;'`

- **Причина**: ADF пытался создать пакет на кластере Spark с помощью Livy API (livy/batch), но получил ошибку.

- **Рекомендация**: Пожалуйста, следуйте сообщению об ошибке, чтобы исправить проблему. Если нет достаточной информации, чтобы решить ее, свяжитесь с командой ИРЧП и предоставьте им идентификатор пакетов и идентификатор работы, который можно найти на странице вывода aDF Мониторинга.


### <a name="error-code--2312"></a>Код ошибки: 2312

- **Сообщение**:`Spark job failed, batch id:%batchId;. Please follow the links in the activity run Output from ADF Monitoring page to troubleshoot the run on HDInsight Spark cluster. Please contact HDInsight support team for further assistance.`

- **Причина**: Задание не удалось на HDInsight Spark кластера.

- **Рекомендация**: Пожалуйста, следуйте ссылкам на выход на страницу мониторинга ADF, чтобы устранить неполадки в кластере HDInsight Spark. Пожалуйста, свяжитесь с группой поддержки HDInsight для получения дополнительной помощи.


### <a name="error-code--2313"></a>Код ошибки: 2313

- **Сообщение**:`The batch with ID '%batchId;' was not found on Spark cluster. Open the Spark History UI and try to find it there. Contact HDInsight support for further assistance.`

- **Причина**: Пакет был удален в кластере HDInsight Spark.

- **Рекомендация**: Пакеты Troubleshoot в кластере HDInsight Spark. Для получения дополнительной помощи обратитесь в службу поддержки HDInsight. 


### <a name="error-code--2328"></a>Код ошибки: 2328

- **Сообщение**:`Failed to create the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Причина**:`The error message should show the details of what went wrong.`

- **Рекомендация**: Сообщение об ошибке должно помочь устранить проблему.


### <a name="error-code--2329"></a>Код ошибки: 2329

- **Сообщение**:`Failed to delete the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Причина**: Сообщение об ошибке должно отображать детали того, что пошло не так.

- **Рекомендация**: Сообщение об ошибке должно помочь устранить проблему.


### <a name="error-code--2331"></a>Код ошибки: 2331

- **Сообщение**:`The file path should not be null or empty.`

- **Причина**: Предоставленный путь файла пуст.

- **Рекомендация**: Пожалуйста, предоставьте путь для файла, который существует.


### <a name="error-code--2340"></a>Код ошибки: 2340

- **Сообщение**:`HDInsightOnDemand linked service does not support execution via SelfHosted IR. Your IR name is '%IRName;'. Please select an Azure IR instead.`

- **Причина**: HDInsightOnDemand связанная услуга не поддерживает выполнение через SelfHosted IR.

- **Рекомендация**: Пожалуйста, выберите ИК Azure и повторите попытку.


### <a name="error-code--2341"></a>Код ошибки: 2341

- **Сообщение**:`HDInsight cluster URL '%clusterUrl;' is incorrect, it must be in URI format and the scheme must be 'https'.`

- **Причина**: Предоставленный URL не находится в правильном формате.

- **Рекомендация**: Пожалуйста, исправьте URL кластера и повторите попытку.


### <a name="error-code--2342"></a>Код ошибки: 2342

- **Сообщение**:`Failed to connect to HDInsight cluster: '%errorMessage;'.`

- **Причина**: Предоставленные учетные данные неправильны для кластера или возникла сетевая конфигурация или проблема подключения или ИК-фактура испытывает проблемы с подключением к кластеру.

- **Совет**.  
      1. Убедитесь, что учетные данные верны, открыв аяизию Ambari кластера HDInsight в браузере.
      2. Если кластер находится в VNet и используется самостоятельно размещенный ИК, URL ИР ИР ЧП должен быть личным URL в VNets, это означает, что он должен иметь '-int' после названия кластера. Напримерhttps://mycluster.azurehdinsight.net/, «должны бытьhttps://mycluster-int.azurehdinsight.net/изменены к «.
      2. Если кластер находится в VNet, используется самостоятельно размещенный ИК, и используется личный URL, и соединение все еще не удалось, то У VM, где установлен ИК, возникли проблемы с подключением к ИР. Подключитесь к VM, где установлен ИК, и откройте uI Ambari в браузере. Используйте личный URL для кластера. Это соединение должно работать из браузера. Если это не так, обратитесь в службу поддержки HDInsight для получения дополнительной помощи.
      3. Если самостоятельно размещенные ИК не используются, то кластер ИР ЧП должен быть доступен публично. Откройте интерфейс Ambari в браузере и убедитесь, что он открывается. Если есть какие-либо проблемы с кластером или услуги на нем, обратитесь за помощью в службу поддержки HDInsight.
      Таким образом, в целом, URL-адрес кластера ИР ИР ИР ИР ИР ИР ИР ИР и для работы тестового соединения и для выполнения для работы должен быть доступен для ADF IR (самоходной или Azure). Это можно легко проверить, открыв этот URL из браузера либо от VM или любой общедоступной машины.
    


### <a name="error-code--2343"></a>Код ошибки: 2343

- **Сообщение**:`User name and password cannot be null or empty to connect to the HDInsight cluster.`

- **Причина**: Либо имя пользователя или пароль пусты.

- **Рекомендация**: Предоставьте правильные учетные данные для подключения к ИРЧП и повторите попытку.


### <a name="error-code--2345"></a>Код ошибки: 2345

- **Сообщение**:`Failed to read the content of the hive script. Error: '%message;'`

- **Причина**: Файл скрипта не существует или ADF не может подключиться к местоположению скрипта.

- **Рекомендация**: Пожалуйста, убедитесь, что скрипт существует, и связанная служба имеет надлежащие учетные данные для подключения.


### <a name="error-code--2346"></a>Код ошибки: 2346

- **Сообщение**:`Failed to create ODBC connection to the HDI cluster with error message '%message;'.`

- **Причина**: ADF пытался установить соединение ODBC с кластером ИРЧП, и он потерпел неудачу с ошибкой.

- **Рекомендация**: Сообщение об ошибке и код ошибки должны помочь в устранении ошибок в подключении ODBC. В случае, если их недостаточно для решения проблемы, обратитесь за поддержкой в группу Azure HDInsight.


### <a name="error-code--2347"></a>Код ошибки: 2347

- **Сообщение**:`Hive execution through ODBC failed with error message '%message;'.`

- **Причина**: ADF представила улей сценарий для выполнения кластера ИРЧП через подключение ODBC и сценарий не удалось на ИРЧП.

- **Рекомендация**: Выполнение скрипта улья не удалось на кластере ИПЧП и сообщение об ошибке и код ошибки должны помочь в устранении неполадок. В случае, если их недостаточно для решения проблемы, обратитесь за поддержкой в группу Azure HDInsight.


### <a name="error-code--2348"></a>Код ошибки: 2348

- **Сообщение**:`The main storage has not been initialized. Please check the properties of the storage linked service in the HDI linked service.`

- **Причина**: Свойства службы хранения, связанные с хранением, настроены неправильно.

- **Рекомендация**: Только полные строки подключения поддерживаются в основном службе хранения, связанной с деятельностью ИРЧП. Пожалуйста, убедитесь, что вы не используете MSI auth или приложений.


### <a name="error-code--2350"></a>Код ошибки: 2350

- **Сообщение**:`Failed to prepare the files for the run '%jobId;'. HDI cluster: '%cluster;', Error: '%errorMessage;'`

- **Причина**: Учетные данные, предоставленные для подключения к хранилищу, где должны быть расположены файлы, неверны, или файлы там не существуют.

- **Рекомендация**: Эта ошибка происходит, когда ADF делает шаги подготовки к действиям ИРЧП. Он пытается скопировать файлы в основное хранилище, прежде чем отправить задание в ИРЧП. Убедитесь, что файлы существуют в предоставленном месте, подключение к хранилищу является правильным. Действия ADF HDI не поддерживают проверку подлинности MSI на учетных записях хранения, связанных с действиями ИРЧП, поэтому убедитесь, что эти связанные службы имеют полные ключи или используют Azure Key Vault.


### <a name="error-code--2351"></a>Код ошибки: 2351

- **Сообщение**:`Could not open the file '%filePath;' in container/fileSystem '%container;'.`

- **Причина**: Файл не существует на указанном пути.

- **Рекомендация**: Пожалуйста, проверьте, существует ли файл на самом деле, и связанная служба с информацией о подключении, указывающей на этот файл, имеет правильные учетные данные.


### <a name="error-code--2352"></a>Код ошибки: 2352

- **Сообщение**:`The file storage has not been initialized. Please check the properties of the file storage linked service in the HDI activity.`

- **Причина**: Свойства службы хранения файлов, связанные с файлами, настроены неправильно.

- **Рекомендация**: Пожалуйста, убедитесь, что свойства службы хранения файлов настроены должным образом.


### <a name="error-code--2353"></a>Код ошибки: 2353

- **Сообщение**:`The script storage has not been initialized. Please check the properties of the script storage linked service in the HDI activity.`

- **Причина**: Свойства службы хранения скриптов настроены неправильно.

- **Рекомендация**: Пожалуйста, убедитесь, что свойства службы хранения скриптов настроены должным образом.


### <a name="error-code--2354"></a>Код ошибки: 2354

- **Сообщение**:`The storage linked service type '%linkedServiceType;' is not supported for '%executorType;' activities for property '%linkedServicePropertyName;'.`

- **Причина**: Связанный с хранилищем тип службы не поддерживается действием.

- **Рекомендация**: Пожалуйста, убедитесь, что выбранная связанная служба имеет один из поддерживаемых типов для деятельности. Службы поддержки служб AzureBlobStorage и AzureBlobFSStorage поддерживаются службами Поддержки Безопасности ИР.


### <a name="error-code--2355"></a>Код ошибки: 2355

- **Сообщение**:`The '%value' provided for commandEnvironment is incorrect. The expected value should be an array of strings where each string has the format CmdEnvVarName=CmdEnvVarValue.`

- **Причина**: Предоставленная для commandEnvironment неверна.

- **Совет**.  
      Пожалуйста, убедитесь, что \"предоставленное значение аналогично: commandEnvironment\": - variableName-variableValue \"\" - и каждая переменная появляется в списке только один раз.
    


### <a name="error-code--2356"></a>Код ошибки: 2356

- **Сообщение**:`The commandEnvironment already contains a variable named '%variableName;'.`

- **Причина**: Переменная была предоставлена дважды в commandEnvironment .

- **Совет**.  
      Пожалуйста, убедитесь, что \"предоставленное значение аналогично: commandEnvironment\": - variableName-variableValue \"\" - и каждая переменная появляется в списке только один раз.
    


### <a name="error-code--2357"></a>Код ошибки: 2357

- **Сообщение**:`The certificate or password is wrong for ADLS Gen 1 storage.`

- **Причина**: Предоставленные учетные данные неверны.

- **Рекомендация**: Пожалуйста, проверьте информацию о подключении в aDLS Gen 1 связаны службы и убедитесь, что тест подключение успешно.


### <a name="error-code--2358"></a>Код ошибки: 2358

- **Сообщение**:`The value '%value;' for the required property 'TimeToLive' in the on demand HDInsight linked service '%linkedServiceName;' has invalid format. It should be a timespan between '00:05:00' and '24:00:00'.`

- **Причина**: Предоставленное значение для требуемого свойства 'TimeToLive' имеет недействительный формат. 

- **Рекомендация**: Пожалуйста, обновите значение, чтобы быть в предлагаемом диапазоне и попробуйте еще раз.


### <a name="error-code--2359"></a>Код ошибки: 2359

- **Сообщение**:`The value '%value;' for the property 'roles' is invalid. Expected types are 'zookeeper', 'headnode', and 'workernode'.`

- **Причина**: Предоставленное значение для "ролей" свойства является недействительным.

- **Рекомендация**: Пожалуйста, обновите значение, чтобы быть одним из предложений и попробуйте еще раз.


### <a name="error-code--2360"></a>Код ошибки: 2360

- **Сообщение**:`The connection string in HCatalogLinkedService is invalid. Encountered an error while trying to parse: '%message;'.`

- **Причина**: Предоставленная строка подключения для HCatalogLinkedService недействительна.

- **Рекомендация**: Пожалуйста, обновите значение для правильной строки подключения Azure S'L и повторите попытку.


### <a name="error-code--2361"></a>Код ошибки: 2361

- **Сообщение**:`Failed to create on demand HDI cluster. Cluster name is '%clusterName;'.`

- **Причина**: Создание кластера не удалось, и ADF не получил ошибку обратно из службы HDInsight.

- **Рекомендация**: Откройте портал Azure и попытайтесь найти ресурс ИРЧП с предоставленным именем и проверьте статус подготовки. Для получения дополнительной помощи обратитесь в службу поддержки HDInsight.


### <a name="error-code--2362"></a>Код ошибки: 2362

- **Сообщение**:`Only Azure Blob storage accounts are supported as additional storages for HDInsight on demand linked service.`

- **Причина**: Предоставленное дополнительное хранилище не было хранилищем Azure Blob.

- **Рекомендация**: Предоставите учетную запись хранения Azure Blob в качестве дополнительного хранилища для службы HDInsight по запросу.



## <a name="web-activity"></a>Веб-действие

### <a name="error-code--2128"></a>Код ошибки: 2128

- **Сообщение**:`No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Причина**: Подключение к сети, сбой DNS, проверка сертификата сервера или тайм-аут.

- **Рекомендация**: Проверка того, что конечная точка, которую вы пытаетесь попасть, отвечает на запросы. Вы можете использовать такие инструменты, как Скрипач / Почтальон.


### <a name="error-code--2108"></a>Код ошибки: 2108

- **Сообщение**:`Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Причина**: Запрос не удалось из-за основной проблемы, такие как подключение к сети, сбой DNS, проверка сертификата сервера или тайм-аут.

- **Рекомендация**: Используйте Скрипача/Почтальона для проверки запроса.
<br>


#### <a name="more-details"></a>Дополнительные сведения
Использовать Fiddler для создания сеанса HTTP контролируемого веб-приложения:

1. Скачать, установить и открыть [Скрипач](https://www.telerik.com/download/fiddler).

1. Если ваше веб-приложение использует HTTPS, перейдите на **инструменты** > **Fiddler Options** > **HTTPS.** Выберите **захват HTTPS CONNECTS** и **расшифровка трафика HTTPS.**

   ![Варианты скрипача](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. Если приложение использует сертификаты TLS/SSL, добавьте сертификат Fiddler на устройство. Перейти к **инструменты** > **Скрипач Параметры** > **HTTPS** > **Действия** > Экспорт**Корневой сертификат на рабочий стол**.

1. Выключите захват, перейдя в **файл** > **захвата трафика.** Или нажмите **F12**.

1. Очистите кэш браузера так, чтобы все кэшированные элементы были удалены и должны быть загружены снова.

1. Создать запрос:

   1. Выберите вкладку **«Композитор».**

   1. Установите метод HTTP и URL.
   
   1. Добавьте заголовки и тело запроса, если это необходимо.

   1. Нажмите кнопку **Выполнить**.

1. Снова включите захват трафика и завершите проблемную транзакцию на вашей странице.

1. Перейти к **файлу** > **Сохранить** > **все сессии**.

Для получения дополнительной информации, [см. Начало работы с Скрипач](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler).

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительной помощи для устранения неполадок попробуйте следующие ресурсы:

*  [Блог Фабрики данных](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Запросы функций Фабрики данных](https://feedback.azure.com/forums/270578-data-factory)
*  [Видео по Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Форум MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Форум переполнения стеков для фабрики данных](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Информация в Твиттере о фабрике данных](https://twitter.com/hashtag/DataFactory)


            
