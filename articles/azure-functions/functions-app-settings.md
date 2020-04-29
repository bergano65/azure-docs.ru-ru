---
title: Справочник по параметрам приложений для Функций Azure
description: Справочная документация по параметрам приложений и переменным среды для Функций Azure.
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: e2d168d8828d17e13f875e3b2555c7db0d4ba32d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80656790"
---
# <a name="app-settings-reference-for-azure-functions"></a>Справочник по параметрам приложений для Функций Azure

Параметры приложения в приложении-функции содержат параметры глобальной конфигурации, влияющие на все функции данного приложения-функции. При локальном запуске эти параметры доступны как [переменные локальной среды](functions-run-local.md#local-settings-file). В этой статье перечислены параметры приложений, которые доступны в приложениях-функциях.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

В файле [host.json](functions-host-json.md) и в файле [local.settings.json](functions-run-local.md#local-settings-file) содержатся другие параметры глобальной конфигурации.

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

Ключ инструментирования для Application Insights. Используйте только один из `APPINSIGHTS_INSTRUMENTATIONKEY` них `APPLICATIONINSIGHTS_CONNECTIONSTRING`или. Дополнительные сведения см. в разделе [Мониторинг функций Azure](functions-monitoring.md). 

|Клавиши|Образец значения|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|55555555-af77-484b-9032-64f83bb83bb|

## <a name="applicationinsights_connectionstring"></a>APPLICATIONINSIGHTS_CONNECTIONSTRING

Строка подключения для Application Insights. Используйте `APPLICATIONINSIGHTS_CONNECTIONSTRING` вместо, `APPINSIGHTS_INSTRUMENTATIONKEY` если приложению функции требуются добавленные настройки, поддерживаемые с помощью строки подключения. Дополнительные сведения см. в разделе [строки подключения](../azure-monitor/app/sdk-connection-string.md). 

|Клавиши|Образец значения|
|---|------------|
|APPLICATIONINSIGHTS_CONNECTIONSTRING|InstrumentationKey = [ключ]; Инжестионендпоинт = [URL]; Ливиндпоинт = [URL]; Профилерендпоинт = [URL]; Снапшотендпоинт = [URL];|

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

В версии 2. x и более поздних версиях среды выполнения функций настраивает поведение приложения на основе среды выполнения. Это значение [считывается во время инициализации](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/Program.cs#L43). Можно `AZURE_FUNCTIONS_ENVIRONMENT` задать любое значение, но поддерживаются [три значения](/dotnet/api/microsoft.aspnetcore.hosting.environmentname) : Разработка, [промежуточное](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging) [развертывание](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development)и [Рабочая среда](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production). Если `AZURE_FUNCTIONS_ENVIRONMENT` параметр не задан, по умолчанию `Development` используется значение в локальной среде `Production` и в Azure. Этот параметр следует использовать вместо `ASPNETCORE_ENVIRONMENT` установки среды выполнения. 

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Необязательная строка подключения учетной записи для хранения журналов и их отображения на вкладке **Монитор** на портале. Этот параметр допустим только для приложений, предназначенных для версии 1. x среды выполнения функций Azure. Учетная запись хранения должна быть учетной записью общего назначения, поддерживающей большие двоичные объекты, очереди и таблицы. Дополнительные сведения см. в статье [требования к учетной записи хранения](storage-considerations.md#storage-account-requirements).

|Клавиши|Образец значения|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>|

> [!NOTE]
> Для повышения производительности и эффективности среда выполнения версии 2. x и более поздних версий использует APPINSIGHTS_INSTRUMENTATIONKEY и App Insights для мониторинга, `AzureWebJobsDashboard`а не.

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true` означает отключение целевой страницы по умолчанию, которая отображается для корневого URL-адреса приложения-функции. Значение по умолчанию — `false`.

|Клавиши|Образец значения|
|---|------------|
|AzureWebJobsDisableHomepage|Да|

Если пропустить этот параметр приложения или задать для него значение `false`, то в ответ на URL-адрес `<functionappname>.azurewebsites.net` отобразится страница, аналогичная приведенной ниже.

![Целевая страница приложения-функции](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true` означает использование режима выпуска при компиляции кода .NET; `false` означает использование режима отладки. Значение по умолчанию — `true`.

|Клавиши|Образец значения|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|Да|

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

Определяет, включен ли режим редактирования в портал Azure. Допустимые значения — "readwrite" и "readonly".

|Клавиши|Образец значения|
|---|------------|
|FUNCTION\_APP\_EDIT\_MODE|readonly|

## <a name="functions_extension_version"></a>FUNCTIONS\_EXTENSION\_VERSION

Версия среды выполнения Функций, которая используется в этом приложении-функции. Тильда с основным номером версии означает использование последней версии этого основного номера версии (например, "~2"). Когда доступны новые версии для того же основного номера версии, они устанавливаются в приложении-функции автоматически. Чтобы закрепить для приложения определенную версию, используйте полный номер версии (например, "2.0.12345"). Значение по умолчанию: "~2". Значение `~1` позволяет закрепить для приложения версию 1.x среды выполнения.

|Клавиши|Образец значения|
|---|------------|
|FUNCTIONS\_EXTENSION\_VERSION|~2|

## <a name="functions_v2_compatibility_mode"></a>Режим\_совместимости\_\_функций v2

Этот параметр позволяет приложению-функции работать в режиме совместимости с версией 2. x в среде выполнения версии 3. x. Используйте этот параметр, только если возникли проблемы при [обновлении приложения функции с версии 2. x до 3. x среды выполнения](functions-versions.md#migrating-from-2x-to-3x). 

>[!IMPORTANT]
> Этот параметр предназначен только для краткосрочного решения при обновлении приложения для правильной работы в версии 3. x. Этот параметр поддерживается до тех пор, пока [поддерживается среда выполнения 2. x](functions-versions.md). Если возникли проблемы, препятствующие запуску приложения в версии 3. x без использования этого параметра, [сообщите об ошибке](https://github.com/Azure/azure-functions-host/issues/new?template=Bug_report.md).

Требует, [чтобы\_версия\_расширения функций](functions-app-settings.md#functions_extension_version) была установлена `~3`в значение.

|Клавиши|Образец значения|
|---|------------|
|Режим\_совместимости\_\_функций v2|Да|

## <a name="functions_worker_process_count"></a>число\_рабочих\_процессов\_функций

Указывает максимальное количество рабочих процессов на языке и значение по умолчанию `1`. Максимально допустимое значение — `10`. Вызовы функций равномерно распределяются между рабочими процессами языка. Рабочие процессы языка порождаются каждые 10 секунд, пока не будет достигнут Счетчик\_,\_заданный счетчиком рабочих процессов\_функций. Использование нескольких языковых рабочих процессов отличается от [масштабирования](functions-scale.md). Рекомендуется использовать этот параметр, если в рабочей нагрузке есть сочетание вызовов, привязанных к ЦП, и операций ввода-вывода, связанных с вводом-выводом. Этот параметр применяется ко всем non-.NET языкам.

|Клавиши|Образец значения|
|---|------------|
|число\_рабочих\_процессов\_функций|2|


## <a name="functions_worker_runtime"></a>FUNCTIONS\_WORKER\_RUNTIME

Среда выполнения языка рабочей роли для загрузки в приложении-функции.  Она будет соответствовать языку, используемому в приложении (например, "dotnet"). Функции на нескольких языках потребуется опубликовать в нескольких приложениях с соответствующим значением среды выполнения рабочей роли.  Допустимые значения `dotnet` : (C#/f #) `node` , (JavaScript/TypeScript) `java` , (Java) `powershell` , (PowerShell) и `python` (Python).

|Клавиши|Образец значения|
|---|------------|
|FUNCTIONS\_WORKER\_RUNTIME|dotnet|

## <a name="website_contentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

Только для планов использования & Premium. Строка подключения для учетной записи хранения, где хранятся код и конфигурация приложения-функции. Ознакомьтесь с разделом [Создание приложения-функции](functions-infrastructure-as-code.md#create-a-function-app).

|Клавиши|Образец значения|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

## <a name="website_contentshare"></a>WEBSITE\_CONTENTSHARE

Только для планов использования & Premium. Путь к файлам c кодом и конфигурацией приложения-функции. Используется с WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. Значение по умолчанию — уникальная строка, которая начинается с имени приложения-функции. Ознакомьтесь с разделом [Создание приложения-функции](functions-infrastructure-as-code.md#create-a-function-app).

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

_Только Windows._  
Задает версию Node. js, используемую при запуске приложения функции в Windows. Чтобы среда выполнения использовала последнюю доступную версию целевой основной версии, следует использовать символ тильды (~). Например, если задано значение `~10`, используется последняя версия Node. js 10. Если для основной версии используется тильда, не нужно вручную обновлять дополнительный номер версии. 

|Клавиши|Образец значения|
|---|------------|
|WEBSITE\_NODE\_DEFAULT_VERSION|~ 10|

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
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|Да|Вызовы с URL-адресом внутреннего сервера, указывающим на функцию в локальном приложение-функция больше не будут отправляться непосредственно в функцию, а вместо этого будут переданы обратно в интерфейс HTTP для приложение-функция|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|false|Это значение по умолчанию. Вызовы с URL-адресом внутреннего сервера, указывающим на функцию в локальном приложение-функция будут переадресовываться непосредственно в эту функцию|


## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

Этот параметр контролирует, декодируется ли сочетание знаков %2F как косая черта в параметрах маршрута, если оно вставлено в URL-адрес внутреннего сервера. 

|Клавиши|Значение|Описание|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|Да|Параметры маршрута с закодированными косыми чертами будут его декодировать. `example.com/api%2ftest` станет `example.com/api/test`|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|false|Это поведение по умолчанию. Все параметры маршрута будут передаваться без изменений|

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
|Да|myfunction.com/test%2fapi|example.com/test/api
|false|myfunction.com/test%2fapi|example.com/test%2fapi|


## <a name="next-steps"></a>Дальнейшие шаги

[Узнайте, как обновлять параметры приложения](functions-how-to-use-azure-function-app-settings.md#settings)

[Ознакомьтесь с глобальными параметрами в файле host.json](functions-host-json.md)

[См. другие параметры приложения для приложений Службы приложений Azure](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
