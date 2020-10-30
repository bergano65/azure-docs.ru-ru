---
title: Справочник по параметрам приложений для Функций Azure
description: Справочная документация по параметрам приложений и переменным среды для Функций Azure.
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: 3d3def7057eeb022b3e207cbecf06ee3074a91af
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93043235"
---
# <a name="app-settings-reference-for-azure-functions"></a>Справочник по параметрам приложений для Функций Azure

Параметры приложения в приложении-функции содержат параметры глобальной конфигурации, влияющие на все функции данного приложения-функции. При локальном запуске эти параметры доступны как [переменные локальной среды](functions-run-local.md#local-settings-file). В этой статье перечислены параметры приложений, которые доступны в приложениях-функциях.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

В файле [host.json](functions-host-json.md) и в файле [local.settings.json](functions-run-local.md#local-settings-file) содержатся другие параметры глобальной конфигурации.

> [!NOTE]  
> Параметры приложения можно использовать для переопределения host.jsзначений параметров без необходимости изменения host.jsдля самого файла. Это полезно в сценариях, где необходимо настроить или изменить конкретные host.jsпараметров для конкретной среды. Это также позволяет изменить host.jsпараметров без повторной публикации проекта. Дополнительные сведения см. в разделе [host.jsв справочной статье](functions-host-json.md#override-hostjson-values).  

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

Ключ инструментирования для Application Insights. Используйте только один из них `APPINSIGHTS_INSTRUMENTATIONKEY` или `APPLICATIONINSIGHTS_CONNECTION_STRING` . Если Application Insights работает в облаке независимых, используйте `APPLICATIONINSIGHTS_CONNECTION_STRING` . Дополнительные сведения см. [в статье Настройка мониторинга для функций Azure](configure-monitoring.md). 

|Ключ|Образец значения|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|55555555-af77-484b-9032-64f83bb83bb|

## <a name="applicationinsights_connection_string"></a>APPLICATIONINSIGHTS_CONNECTION_STRING

Строка подключения для Application Insights. Используйте `APPLICATIONINSIGHTS_CONNECTION_STRING` вместо `APPINSIGHTS_INSTRUMENTATIONKEY` в следующих случаях:

+ Если приложению функции требуются добавленные настройки, поддерживаемые с помощью строки подключения. 
+ Когда экземпляр Application Insights работает в облаке независимых, для которого требуется Пользовательская конечная точка.

Дополнительные сведения см. в разделе [строки подключения](../azure-monitor/app/sdk-connection-string.md). 

|Ключ|Образец значения|
|---|------------|
|APPLICATIONINSIGHTS_CONNECTION_STRING|InstrumentationKey = [ключ]; Инжестионендпоинт = [URL]; Ливиндпоинт = [URL]; Профилерендпоинт = [URL]; Снапшотендпоинт = [URL];|

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

По умолчанию [прокси-серверы функций](functions-proxies.md) используют ярлыки для отправки вызовов API из прокси непосредственно в функции в том же приложении-функции. Это сочетание клавиш используется вместо создания нового HTTP-запроса. Этот параметр позволяет отключить поведение ярлыка.

|Ключ|Значение|Описание|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|true|Вызовы с URL-адресом внутреннего сервера, указывающим на функцию в локальном приложении-функции, не будут отправляться непосредственно в функцию. Вместо этого запросы перенаправляются обратно на интерфейс HTTP для приложения-функции.|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|false|Вызовы с URL-адресом внутреннего сервера, указывающим на функцию в локальном приложении-функции, перенаправляются непосредственно в функцию. Это значение по умолчанию. |

## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

Этот параметр определяет `%2F` , будут ли символы декодированы как косые черты в параметрах маршрута при их вставке в внутренний URL-адрес. 

|Ключ|Значение|Описание|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|true|Параметры маршрутов с закодированными косыми чертами декодированы. |
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|false|Все параметры маршрута передаются без изменений, что является поведением по умолчанию. |

Например, рассмотрим proxies.jsфайла для приложения-функции в `myfunction.com` домене.

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

Если параметр `AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES` имеет значение `true` , URL-адрес `example.com/api%2ftest` разрешается в `example.com/api/test` . По умолчанию URL-адрес остается неизменным `example.com/test%2fapi` . Дополнительные сведения см. в разделе [функции прокси-сервера функций](functions-proxies.md).

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

В версии 2. x и более поздних версиях среды выполнения функций настраивает поведение приложения на основе среды выполнения. Это значение [считывается во время инициализации](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/Program.cs#L43). Можно задать `AZURE_FUNCTIONS_ENVIRONMENT` любое значение, но поддерживаются [три значения](/dotnet/api/microsoft.aspnetcore.hosting.environmentname) : Разработка, [промежуточное](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging) [развертывание](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development)и [Рабочая среда](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production). Если `AZURE_FUNCTIONS_ENVIRONMENT` параметр не задан, по умолчанию используется значение `Development` в локальной среде и `Production` в Azure. Этот параметр следует использовать вместо `ASPNETCORE_ENVIRONMENT` установки среды выполнения. 

## <a name="azurefunctionsjobhost__"></a>AzureFunctionsJobHost__\*

В версии 2. x и более поздних версиях среды выполнения функций параметры приложения могут переопределять [host.js](functions-host-json.md) параметров в текущей среде. Эти переопределения выражаются в виде параметров приложения с именем `AzureFunctionsJobHost__path__to__setting` . Дополнительные сведения см. [в разделе Override host.json Values](functions-host-json.md#override-hostjson-values).

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Необязательная строка подключения учетной записи для хранения журналов и их отображения на вкладке **Монитор** на портале. Этот параметр допустим только для приложений, предназначенных для версии 1. x среды выполнения функций Azure. Учетная запись хранения должна быть учетной записью общего назначения, поддерживающей большие двоичные объекты, очереди и таблицы. Дополнительную информацию см. в статье [Требования учетных записей хранения](storage-considerations.md#storage-account-requirements).

|Ключ|Образец значения|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>|

> [!NOTE]
> Для повышения производительности и эффективности среда выполнения версии 2. x и более поздних версий использует APPINSIGHTS_INSTRUMENTATIONKEY и App Insights для мониторинга, а не `AzureWebJobsDashboard` .

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true` означает отключение целевой страницы по умолчанию, которая отображается для корневого URL-адреса приложения-функции. Значение по умолчанию — `false`.

|Ключ|Образец значения|
|---|------------|
|AzureWebJobsDisableHomepage|true|

Если пропустить этот параметр приложения или задать для него значение `false`, то в ответ на URL-адрес `<functionappname>.azurewebsites.net` отобразится страница, аналогичная приведенной ниже.

![Целевая страница приложения-функции](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true` означает использование режима выпуска при компиляции кода .NET; `false` означает использование режима отладки. Значение по умолчанию — `true`.

|Ключ|Образец значения|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|true|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

Разделенный запятыми список бета-функций, которые необходимо включить. Бета-функции, которые здесь отмечаются флагами, еще не готовы для рабочей среды, но их можно включить для использования в экспериментальных целях.

|Ключ|Образец значения|
|---|------------|
|AzureWebJobsFeatureFlags|feature1,feature2|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

Указывает репозиторий или поставщик, используемый для хранения ключей. Сейчас поддерживаемыми репозиториями являются большой двоичный объект ("Blob") и локальная файловая система ("Files"). По умолчанию в версии 2 используется большой двоичный объект, а в версии 1 — файловая система.

|Ключ|Образец значения|
|---|------------|
|AzureWebJobsSecretStorageType|Файлы|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

Среда выполнения Функций Azure использует эту строку подключения учетной записи хранения для всех функций, кроме функций, активируемых протоколом HTTP. Учетная запись хранения должна быть учетной записью общего назначения, поддерживающей большие двоичные объекты, очереди и таблицы. Ознакомьтесь с разделами [Учетная запись хранения](functions-infrastructure-as-code.md#storage-account) и [Требования к учетной записи хранения](storage-considerations.md#storage-account-requirements).

|Ключ|Образец значения|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

Путь к компилятору, который используется для TypeScript. Позволяет при необходимости переопределить значение по умолчанию.

|Ключ|Образец значения|
|---|------------|
|AzureWebJobs_TypeScriptPath|%HOME%\typescript|

## <a name="function_app_edit_mode"></a>FUNCTION\_APP\_EDIT\_MODE

Определяет, включен ли режим редактирования в портал Azure. Допустимые значения — "readwrite" и "readonly".

|Ключ|Образец значения|
|---|------------|
|FUNCTION\_APP\_EDIT\_MODE|readonly|

## <a name="functions_extension_version"></a>FUNCTIONS\_EXTENSION\_VERSION

Версия среды выполнения Функций, которая используется в этом приложении-функции. Тильда с основным номером версии означает использование последней версии этого основного номера версии (например, "~2"). Когда доступны новые версии для того же основного номера версии, они устанавливаются в приложении-функции автоматически. Чтобы закрепить для приложения определенную версию, используйте полный номер версии (например, "2.0.12345"). Значение по умолчанию: "~2". Значение `~1` позволяет закрепить для приложения версию 1.x среды выполнения.

|Ключ|Образец значения|
|---|------------|
|FUNCTIONS\_EXTENSION\_VERSION|~2|

## <a name="functions_v2_compatibility_mode"></a>\_ \_ Режим совместимости функций \_ v2

Этот параметр позволяет приложению-функции работать в режиме совместимости с версией 2. x в среде выполнения версии 3. x. Используйте этот параметр, только если возникли проблемы при [обновлении приложения функции с версии 2. x до 3. x среды выполнения](functions-versions.md#migrating-from-2x-to-3x). 

>[!IMPORTANT]
> Этот параметр предназначен только для краткосрочного решения при обновлении приложения для правильной работы в версии 3. x. Этот параметр поддерживается до тех пор, пока [поддерживается среда выполнения 2. x](functions-versions.md). Если возникли проблемы, препятствующие запуску приложения в версии 3. x без использования этого параметра, [сообщите об ошибке](https://github.com/Azure/azure-functions-host/issues/new?template=Bug_report.md).

Требует, [чтобы \_ \_ версия расширения функций](functions-app-settings.md#functions_extension_version) была установлена в значение `~3` .

|Ключ|Образец значения|
|---|------------|
|\_ \_ Режим совместимости функций \_ v2|true|

## <a name="functions_worker_process_count"></a>\_число рабочих \_ процессов \_ функций

Указывает максимальное количество рабочих процессов на языке и значение по умолчанию `1` . Максимально допустимое значение — `10` . Вызовы функций равномерно распределяются между рабочими процессами языка. Рабочие процессы языка порождаются каждые 10 секунд, пока не будет достигнут счетчик, заданный счетчиком \_ рабочих \_ процессов функций \_ . Использование нескольких языковых рабочих процессов отличается от [масштабирования](functions-scale.md). Рекомендуется использовать этот параметр, если в рабочей нагрузке есть сочетание вызовов, привязанных к ЦП, и операций ввода-вывода, связанных с вводом-выводом. Этот параметр применяется ко всем non-.NET языкам.

|Ключ|Образец значения|
|---|------------|
|\_число рабочих \_ процессов \_ функций|2|


## <a name="functions_worker_runtime"></a>FUNCTIONS\_WORKER\_RUNTIME

Среда выполнения языка рабочей роли для загрузки в приложении-функции.  Она будет соответствовать языку, используемому в приложении (например, "dotnet"). Функции на нескольких языках потребуется опубликовать в нескольких приложениях с соответствующим значением среды выполнения рабочей роли.  Допустимые значения: `dotnet` (C#/f #), ( `node` JavaScript/TypeScript), `java` (Java), `powershell` (PowerShell) и `python` (Python).

|Ключ|Образец значения|
|---|------------|
|FUNCTIONS\_WORKER\_RUNTIME|dotnet|

## <a name="pip_extra_index_url"></a>\_ \_ \_ URL-адрес дополнительного индекса PIP

Значение этого параметра указывает URL-адрес пользовательского индекса пакета для приложений Python. Используйте этот параметр, если необходимо запустить удаленное построение с использованием пользовательских зависимостей, которые находятся в дополнительных индексах пакетов.   

|Ключ|Образец значения|
|---|------------|
|\_ \_ \_ URL-адрес дополнительного индекса PIP|http://my.custom.package.repo/simple |

Дополнительные сведения см. в разделе [пользовательские зависимости](functions-reference-python.md#remote-build-with-extra-index-url) в справочнике разработчика Python.

## <a name="scale_controller_logging_enable"></a>\_ \_ Включение ведения журнала контроллера масштабирования \_

_Этот параметр в настоящее время находится в режиме предварительной версии._  

Этот параметр управляет ведением журнала с помощью контроллера масштабирования функций Azure. Дополнительные сведения см. в разделе [журналы контроллера масштабирования](functions-monitoring.md#scale-controller-logs).

|Ключ|Образец значения|
|-|-|
|SCALE_CONTROLLER_LOGGING_ENABLE|AppInsights: verbose|

Значение для этого ключа указывается в формате `<DESTINATION>:<VERBOSITY>` , который определяется следующим образом:

[!INCLUDE [functions-scale-controller-logging](../../includes/functions-scale-controller-logging.md)]

## <a name="website_contentazurefileconnectionstring"></a>ВЕБ-сайт \_ контентазурефилеконнектионстринг

Только для планов использования & Premium. Строка подключения для учетной записи хранения, где хранятся код и конфигурация приложения-функции. Ознакомьтесь с разделом [Создание приложения-функции](functions-infrastructure-as-code.md#create-a-function-app).

|Ключ|Образец значения|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

## <a name="website_contentovervnet"></a>ВЕБ-сайт \_ контентовервнет

Только для планов Premium. Значение `1` позволяет масштабировать приложение-функцию, если учетная запись хранения ограничена виртуальной сетью. Этот параметр следует включать при ограничении вашей учетной записи хранения виртуальной сетью. Дополнительные сведения см. в статье [ограничение учетной записи хранения виртуальной сети](functions-networking-options.md#restrict-your-storage-account-to-a-virtual-network-preview). 

|Ключ|Образец значения|
|---|------------|
|WEBSITE_CONTENTOVERVNET|1|

## <a name="website_contentshare"></a>WEBSITE\_CONTENTSHARE

Только для планов использования & Premium. Путь к файлам c кодом и конфигурацией приложения-функции. Используется с WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. Значение по умолчанию — уникальная строка, которая начинается с имени приложения-функции. Ознакомьтесь с разделом [Создание приложения-функции](functions-infrastructure-as-code.md#create-a-function-app).

|Ключ|Образец значения|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## <a name="website_max_dynamic_application_scale_out"></a>WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT

Максимальное число экземпляров, до которого можно развернуть приложение-функцию. По умолчанию ограничение не установлено.

> [!IMPORTANT]
> Этот параметр находится в режиме предварительной версии.  Добавлено [свойство приложения для функции Max Scale out](./functions-scale.md#limit-scale-out) , которое рекомендуется использовать для ограничения масштабирования.

|Ключ|Образец значения|
|---|------------|
|WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT|5|

## <a name="website_node_default_version"></a>WEBSITE\_NODE\_DEFAULT_VERSION

_Только Windows._  
Задает версию Node.js, используемую при запуске приложения функции в Windows. Чтобы среда выполнения использовала последнюю доступную версию целевой основной версии, следует использовать символ тильды (~). Например, если задано значение `~10` , используется последняя версия Node.js 10. Если для основной версии используется тильда, не нужно вручную обновлять дополнительный номер версии. 

|Ключ|Образец значения|
|---|------------|
|WEBSITE\_NODE\_DEFAULT_VERSION|~ 10|

## <a name="website_run_from_package"></a>WEBSITE\_RUN\_FROM\_PACKAGE

Позволяет приложению-функции запуск из файла подключенного пакета.

|Ключ|Образец значения|
|---|------------|
|WEBSITE\_RUN\_FROM\_PACKAGE|1|

Допустимые значения: или URL-адрес, который разрешается в путь файла пакета развертывания, или `1`. Если задано значение `1`, пакет должен быть в папке `d:\home\data\SitePackages`. При использовании развертывания из ZIP-файла с этим параметром пакет автоматически передается в это расположение. В предварительной версии этот параметр называется `WEBSITE_RUN_FROM_ZIP`. Дополнительные сведения см. в статье [Запуск Функций Azure из файла пакета](run-functions-from-deployment-package.md).

## <a name="website_time_zone"></a>\_часовой \_ пояс веб-сайта

Позволяет задать часовой пояс для приложения функции. 

|Ключ|ОС|Образец значения|
|---|--|------------|
|\_часовой \_ пояс веб-сайта|Windows|Восточное время (зима)|
|\_часовой \_ пояс веб-сайта|Linux|America/New_York|

[!INCLUDE [functions-timezone](../../includes/functions-timezone.md)]

## <a name="next-steps"></a>Дальнейшие действия

[Узнайте, как обновлять параметры приложения](functions-how-to-use-azure-function-app-settings.md#settings)

[Ознакомьтесь с глобальными параметрами в файле host.json](functions-host-json.md)

[См. другие параметры приложения для приложений Службы приложений Azure](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
