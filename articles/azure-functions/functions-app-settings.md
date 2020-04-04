---
title: Справочник по параметрам приложений для Функций Azure
description: Справочная документация по параметрам приложений и переменным среды для Функций Azure.
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: e2d168d8828d17e13f875e3b2555c7db0d4ba32d
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656790"
---
# <a name="app-settings-reference-for-azure-functions"></a>Справочник по параметрам приложений для Функций Azure

Параметры приложения в приложении-функции содержат параметры глобальной конфигурации, влияющие на все функции данного приложения-функции. При локальном запуске эти параметры доступны как [переменные локальной среды](functions-run-local.md#local-settings-file). В этой статье перечислены параметры приложений, которые доступны в приложениях-функциях.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

В файле [host.json](functions-host-json.md) и в файле [local.settings.json](functions-run-local.md#local-settings-file) содержатся другие параметры глобальной конфигурации.

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

Ключ приборов для исследования приложений. Используйте только `APPINSIGHTS_INSTRUMENTATIONKEY` `APPLICATIONINSIGHTS_CONNECTIONSTRING`один из или . Дополнительные сведения см. в разделе [Мониторинг функций Azure](functions-monitoring.md). 

|Клавиши|Образец значения|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|5555555-af77-484b-9032-64f83bb83bb|

## <a name="applicationinsights_connectionstring"></a>APPLICATIONINSIGHTS_CONNECTIONSTRING

Строка подключения для Исследования приложений. Используйте `APPLICATIONINSIGHTS_CONNECTIONSTRING` `APPINSIGHTS_INSTRUMENTATIONKEY` вместо того, когда приложение функции требует дополнительных настроек, поддерживаемых с помощью строки соединения. Для получения дополнительной [информации см.](../azure-monitor/app/sdk-connection-string.md) 

|Клавиши|Образец значения|
|---|------------|
|APPLICATIONINSIGHTS_CONNECTIONSTRING|ИнструментацияКейке »ключ»;IngestionEndpoint »url»; LiveEndpoint 'url'; ПрофильЭндпойнт зюрл; SnapshotEndpoint 'url';|

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

В версии 2.x и более поздних версиях времени выполнения функций настраивается поведение приложения на основе среды времени выполнения. Это значение [читается во время инициализации.](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/Program.cs#L43) Вы можете `AZURE_FUNCTIONS_ENVIRONMENT` установить на любое значение, но [три значения](/dotnet/api/microsoft.aspnetcore.hosting.environmentname) поддерживаются: [развитие](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development), [постановка,](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging)и [производство](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production). Когда `AZURE_FUNCTIONS_ENVIRONMENT` он не установлен, он `Development` по умолчанию `Production` по умолчанию влияет на локальную среду и на Azure. Этот параметр следует `ASPNETCORE_ENVIRONMENT` использовать вместо того, чтобы установить среду выполнения. 

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Необязательная строка подключения учетной записи для хранения журналов и их отображения на вкладке **Монитор** на портале. Эта настройка действительна только для приложений, нацеленных на версию 1.x времени выполнения функций Azure. Учетная запись хранения должна быть учетной записью общего назначения, поддерживающей большие двоичные объекты, очереди и таблицы. Чтобы узнать [Storage account requirements](storage-considerations.md#storage-account-requirements)больше, см.

|Клавиши|Образец значения|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>|

> [!NOTE]
> Для повышения производительности и опыта, версия 2.x и более поздние `AzureWebJobsDashboard`версии используют APPINSIGHTS_INSTRUMENTATIONKEY и App Insights для мониторинга вместо .

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true` означает отключение целевой страницы по умолчанию, которая отображается для корневого URL-адреса приложения-функции. Значение по умолчанию — `false`.

|Клавиши|Образец значения|
|---|------------|
|AzureWebJobsDisableHomepage|true|

Если пропустить этот параметр приложения или задать для него значение `false`, то в ответ на URL-адрес `<functionappname>.azurewebsites.net` отобразится страница, аналогичная приведенной ниже.

![Целевая страница приложения-функции](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true` означает использование режима выпуска при компиляции кода .NET; `false` означает использование режима отладки. Значение по умолчанию — `true`.

|Клавиши|Образец значения|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|true|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

Разделенный запятыми список бета-функций, которые необходимо включить. Бета-функции, которые здесь отмечаются флагами, еще не готовы для рабочей среды, но их можно включить для использования в экспериментальных целях.

|Клавиши|Образец значения|
|---|------------|
|AzureWebJobsFeatureFlags|feature1,feature2|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

Указывает репозиторий или поставщик, используемый для хранения ключей. Сейчас поддерживаемыми репозиториями являются большой двоичный объект ("Blob") и локальная файловая система ("Files"). По умолчанию в версии 2 используется большой двоичный объект, а в версии 1 — файловая система.

|Клавиши|Образец значения|
|---|------------|
|AzureWebJobsSecretStorageType|Файлы|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

Среда выполнения Функций Azure использует эту строку подключения учетной записи хранения для всех функций, кроме функций, активируемых протоколом HTTP. Учетная запись хранения должна быть учетной записью общего назначения, поддерживающей большие двоичные объекты, очереди и таблицы. Ознакомьтесь с разделами [Учетная запись хранения](functions-infrastructure-as-code.md#storage-account) и [Требования к учетной записи хранения](storage-considerations.md#storage-account-requirements).

|Клавиши|Образец значения|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

Путь к компилятору, который используется для TypeScript. Позволяет при необходимости переопределить значение по умолчанию.

|Клавиши|Образец значения|
|---|------------|
|AzureWebJobs_TypeScriptPath|%HOME%\typescript|

## <a name="function_app_edit_mode"></a>FUNCTION\_APP\_EDIT\_MODE

Диктует, включена ли редактирование на портале Azure. Допустимые значения — "readwrite" и "readonly".

|Клавиши|Образец значения|
|---|------------|
|FUNCTION\_APP\_EDIT\_MODE|readonly|

## <a name="functions_extension_version"></a>FUNCTIONS\_EXTENSION\_VERSION

Версия среды выполнения Функций, которая используется в этом приложении-функции. Тильда с основным номером версии означает использование последней версии этого основного номера версии (например, "~2"). Когда доступны новые версии для того же основного номера версии, они устанавливаются в приложении-функции автоматически. Чтобы закрепить для приложения определенную версию, используйте полный номер версии (например, "2.0.12345"). Значение по умолчанию: "~2". Значение `~1` позволяет закрепить для приложения версию 1.x среды выполнения.

|Клавиши|Образец значения|
|---|------------|
|FUNCTIONS\_EXTENSION\_VERSION|~2|

## <a name="functions_v2_compatibility_mode"></a>ФУНКЦИЯ\_\_V2\_COMPATIBILITY MODE

Эта настройка позволяет приложению функции работать в совместимом режиме версии 2.x в версии 3.x времени выполнения. Используйте эту настройку только в том случае, если возникают проблемы при [обновлении приложения функции с версии 2.x до 3.x времени выполнения.](functions-versions.md#migrating-from-2x-to-3x) 

>[!IMPORTANT]
> Эта настройка предназначена только в качестве краткосрочного обхода во время обновления приложения для правильного выполнения версии 3.x. Эта настройка поддерживается до тех пор, пока [поддерживается время выполнения 2.x.](functions-versions.md) Если вы столкнулись с проблемами, которые мешают приложению работать в версии 3.x без использования этой настройки, сообщите [о своей проблеме.](https://github.com/Azure/azure-functions-host/issues/new?template=Bug_report.md)

Требует, чтобы [ЭКСТЕАС\_\_ЭКСТЕНС ВЕРСИЯ](functions-app-settings.md#functions_extension_version) была установлена на `~3`.

|Клавиши|Образец значения|
|---|------------|
|ФУНКЦИЯ\_\_V2\_COMPATIBILITY MODE|true|

## <a name="functions_worker_process_count"></a>КОЛИЧЕСТВО\_\_РАБОЧИХ\_ПРОЦЕССОВ ФУНКЦИЙ

Определяет максимальное количество процессов работы языковых работников `1`со значением по умолчанию. Максимальное допустимое `10`значение . Вызовы функций равномерно распределены между процессами языковых работников. Процессы языковых работников появляются каждые 10\_\_секунд\_до тех пор, пока не будет достигнут подсчет, установленный FUNCTIONS WORKER PROCESS COUNT. Использование нескольких процессов работы языка не то же самое, что [масштабирование.](functions-scale.md) Рассмотрите возможность использования этого параметра, когда рабочая нагрузка имеет сочетание вызовов, связанных с процессором и ввоза/ввоза. Эта настройка применима ко всем non-.NET языкам.

|Клавиши|Образец значения|
|---|------------|
|КОЛИЧЕСТВО\_\_РАБОЧИХ\_ПРОЦЕССОВ ФУНКЦИЙ|2|


## <a name="functions_worker_runtime"></a>FUNCTIONS\_WORKER\_RUNTIME

Среда выполнения языка рабочей роли для загрузки в приложении-функции.  Она будет соответствовать языку, используемому в приложении (например, "dotnet"). Функции на нескольких языках потребуется опубликовать в нескольких приложениях с соответствующим значением среды выполнения рабочей роли.  Допустимые `dotnet` значения: (КЗ/ФЗ), `node` (JavaScript/TypeScript), `java` (Java), `powershell` `python` (PowerShell) и (Python).

|Клавиши|Образец значения|
|---|------------|
|FUNCTIONS\_WORKER\_RUNTIME|dotnet|

## <a name="website_contentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

Для потребления & Premium планы только. Строка подключения для учетной записи хранения, где хранятся код и конфигурация приложения-функции. Ознакомьтесь с разделом [Создание приложения-функции](functions-infrastructure-as-code.md#create-a-function-app).

|Клавиши|Образец значения|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

## <a name="website_contentshare"></a>WEBSITE\_CONTENTSHARE

Для потребления & Premium планы только. Путь к файлам c кодом и конфигурацией приложения-функции. Используется с WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. Значение по умолчанию — уникальная строка, которая начинается с имени приложения-функции. Ознакомьтесь с разделом [Создание приложения-функции](functions-infrastructure-as-code.md#create-a-function-app).

|Клавиши|Образец значения|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## <a name="website_max_dynamic_application_scale_out"></a>WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT

Максимальное число экземпляров, до которого можно развернуть приложение-функцию. По умолчанию ограничение не установлено.

> [!NOTE]
> Этот параметр доступен в качестве предварительной версии функции. Надежная работа обеспечивается только при его значении не больше 5.

|Клавиши|Образец значения|
|---|------------|
|WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT|5|

## <a name="website_node_default_version"></a>WEBSITE\_NODE\_DEFAULT_VERSION

_Только для Windows._  
Устанавливает версию Node.js для использования при запуске приложения функции windows. Вы должны использовать tilde (я), чтобы иметь время выполнения использовать последнюю доступную версию целевой основной версии. Например, при `~10`установке на, последняя версия Node.js 10 используется. Когда основная версия ориентирована с tilde, вам не придется вручную обновлять второстепенную версию. 

|Клавиши|Образец значения|
|---|------------|
|WEBSITE\_NODE\_DEFAULT_VERSION|10 евро|

## <a name="website_run_from_package"></a>WEBSITE\_RUN\_FROM\_PACKAGE

Позволяет приложению-функции запуск из файла подключенного пакета.

|Клавиши|Образец значения|
|---|------------|
|WEBSITE\_RUN\_FROM\_PACKAGE|1|

Допустимые значения: или URL-адрес, который разрешается в путь файла пакета развертывания, или `1`. Если задано значение `1`, пакет должен быть в папке `d:\home\data\SitePackages`. При использовании развертывания из ZIP-файла с этим параметром пакет автоматически передается в это расположение. В предварительной версии этот параметр называется `WEBSITE_RUN_FROM_ZIP`. Дополнительные сведения см. в статье [Запуск Функций Azure из файла пакета](run-functions-from-deployment-package.md).

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

По умолчанию прокси-серверы Функций будут использовать команду для отправки вызовов API из них самих непосредственно в функции в одном приложении-функции, а не создавать новый HTTP-запрос. Этот параметр позволяет отключить такую реакцию на событие.

|Клавиши|Значение|Описание|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|true|Звонки с бэкэндURL, указывающий на функцию в локальном приложении function, больше не будут отправляться непосредственно в функцию, а вместо этого будут направлены обратно в переднюю часть HTTP для приложения функции|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|false|Это значение по умолчанию. Звонки с URL-адресом бэкэнда, указывающим на функцию в локальном приложении function, будут перенаправлены непосредственно в эту функцию|


## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

Этот параметр контролирует, декодируется ли сочетание знаков %2F как косая черта в параметрах маршрута, если оно вставлено в URL-адрес внутреннего сервера. 

|Клавиши|Значение|Описание|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|true|Параметры маршрута с закодированными косыми чертами будут его декодировать. `example.com/api%2ftest` станет `example.com/api/test`|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|false|Это поведение установлено по умолчанию. Все параметры маршрута будут передаваться без изменений|

### <a name="example"></a>Пример

Ниже приведен пример файла proxies.json в приложении-функции, расположенному по URL-адресу myfunction.com

```JSON
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "root": {
            "matchCondition": {
                "route": "/{*all}"
            },
            "backendUri": "example.com/{all}"
        }
    }
}
```
|Декодирование URL-адреса|Входные данные|Выходные данные|
|-|-|-|
|true|myfunction.com/test%2fapi|example.com/test/api
|false|myfunction.com/test%2fapi|example.com/test%2fapi|


## <a name="next-steps"></a>Дальнейшие действия

[Узнайте, как обновлять параметры приложения](functions-how-to-use-azure-function-app-settings.md#settings)

[Ознакомьтесь с глобальными параметрами в файле host.json](functions-host-json.md)

[См. другие параметры приложения для приложений Службы приложений Azure](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
