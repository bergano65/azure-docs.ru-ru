---
title: Справочник по параметрам приложений для Функций Azure
description: Справочная документация по параметрам приложений и переменным среды для Функций Azure.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/22/2018
ms.author: glenga
ms.openlocfilehash: 02becd787fcc5f82efff7ef21feaf336fba3a26f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65967590"
---
# <a name="app-settings-reference-for-azure-functions"></a>Справочник по параметрам приложений для Функций Azure

Параметры приложения в приложении-функции содержат параметры глобальной конфигурации, влияющие на все функции данного приложения-функции. При локальном запуске эти параметры доступны как [переменные локальной среды](functions-run-local.md#local-settings-file). В этой статье перечислены параметры приложений, которые доступны в приложениях-функциях.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

В файле [host.json](functions-host-json.md) и в файле [local.settings.json](functions-run-local.md#local-settings-file) содержатся другие параметры глобальной конфигурации.

## <a name="appinsightsinstrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

Ключ инструментирования Application Insights, если используется Application Insights. Дополнительные сведения см. в статье [Мониторинг функций Azure](functions-monitoring.md).

|Ключ|Образец значения|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|5dbdd5e9-af77-484b-9032-64f83bb83bb|

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Необязательная строка подключения учетной записи для хранения журналов и их отображения на вкладке **Монитор** на портале. Учетная запись хранения должна быть учетной записью общего назначения, поддерживающей большие двоичные объекты, очереди и таблицы. Ознакомьтесь с разделами [Учетная запись хранения](functions-infrastructure-as-code.md#storage-account) и [Требования к учетной записи хранения](functions-create-function-app-portal.md#storage-account-requirements).

|Ключ|Образец значения|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

> [!TIP]
> Для достижения наилучшей производительности и взаимодействия вместо AzureWebJobsDashboard для мониторинга рекомендуется использовать APPINSIGHTS_INSTRUMENTATIONKEY и App Insights.

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true` означает отключение целевой страницы по умолчанию, которая отображается для корневого URL-адреса приложения-функции. Значение по умолчанию — `false`.

|Ключ|Образец значения|
|---|------------|
|AzureWebJobsDisableHomepage|Да|

Если пропустить этот параметр приложения или задать для него значение `false`, то в ответ на URL-адрес `<functionappname>.azurewebsites.net` отобразится страница, аналогичная приведенной ниже.

![Целевая страница приложения-функции](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true` означает использование режима выпуска при компиляции кода .NET; `false` означает использование режима отладки. Значение по умолчанию — `true`.

|Ключ|Образец значения|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|Да|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

Разделенный запятыми список бета-функций, которые необходимо включить. Бета-функции, которые здесь отмечаются флагами, еще не готовы для рабочей среды, но их можно включить для использования в экспериментальных целях.

|Ключ|Образец значения|
|---|------------|
|AzureWebJobsFeatureFlags|feature1,feature2|

## <a name="azurewebjobsscriptroot"></a>AzureWebJobsScriptRoot

Путь к корневому каталогу, где расположены файл *host.json* и папки функции. В приложении-функции значение по умолчанию — `%HOME%\site\wwwroot`.

|Ключ|Образец значения|
|---|------------|
|AzureWebJobsScriptRoot|%HOME%\site\wwwroot|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

Указывает репозиторий или поставщик, используемый для хранения ключей. Сейчас поддерживаемыми репозиториями являются большой двоичный объект ("Blob") и локальная файловая система ("Files"). По умолчанию в версии 2 используется большой двоичный объект, а в версии 1 — файловая система.

|Ключ|Образец значения|
|---|------------|
|AzureWebJobsSecretStorageType|Файлы|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

Среда выполнения Функций Azure использует эту строку подключения учетной записи хранения для всех функций, кроме функций, активируемых протоколом HTTP. Учетная запись хранения должна быть учетной записью общего назначения, поддерживающей большие двоичные объекты, очереди и таблицы. Ознакомьтесь с разделами [Учетная запись хранения](functions-infrastructure-as-code.md#storage-account) и [Требования к учетной записи хранения](functions-create-function-app-portal.md#storage-account-requirements).

|Ключ|Образец значения|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

## <a name="azurewebjobstypescriptpath"></a>AzureWebJobs_TypeScriptPath

Путь к компилятору, который используется для TypeScript. Позволяет при необходимости переопределить значение по умолчанию.

|Ключ|Образец значения|
|---|------------|
|AzureWebJobs_TypeScriptPath|%HOME%\typescript|

## <a name="functionappeditmode"></a>FUNCTION\_APP\_EDIT\_MODE

Определяет, разрешено ли редактирование на портале Azure. Допустимые значения — "readwrite" и "readonly".

|Ключ|Образец значения|
|---|------------|
|FUNCTION\_APP\_EDIT\_MODE|readonly|

## <a name="functionsextensionversion"></a>FUNCTIONS\_EXTENSION\_VERSION

Версия среды выполнения Функций, которая используется в этом приложении-функции. Тильда с основным номером версии означает использование последней версии этого основного номера версии (например, "~2"). Когда доступны новые версии для того же основного номера версии, они устанавливаются в приложении-функции автоматически. Чтобы закрепить для приложения определенную версию, используйте полный номер версии (например, "2.0.12345"). Значение по умолчанию: "~2". Значение `~1` позволяет закрепить для приложения версию 1.x среды выполнения.

|Ключ|Образец значения|
|---|------------|
|FUNCTIONS\_EXTENSION\_VERSION|~2|

## <a name="functionsworkerruntime"></a>FUNCTIONS\_WORKER\_RUNTIME

Среда выполнения языка рабочей роли для загрузки в приложении-функции.  Она будет соответствовать языку, используемому в приложении (например, "dotnet"). Функции на нескольких языках потребуется опубликовать в нескольких приложениях с соответствующим значением среды выполнения рабочей роли.  Допустимые значения: `dotnet` (C#/F#), `node` (JavaScript/TypeScript), `java` (Java), `powershell` (PowerShell), и `python` (Python).

|Ключ|Образец значения|
|---|------------|
|FUNCTIONS\_WORKER\_RUNTIME|dotnet|

## <a name="websitecontentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

Только для планов потребления. Строка подключения для учетной записи хранения, где хранятся код и конфигурация приложения-функции. Ознакомьтесь с разделом [Создание приложения-функции](functions-infrastructure-as-code.md#create-a-function-app).

|Ключ|Образец значения|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

## <a name="websitecontentshare"></a>WEBSITE\_CONTENTSHARE

Только для планов потребления. Путь к файлам c кодом и конфигурацией приложения-функции. Используется с WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. Значение по умолчанию — уникальная строка, которая начинается с имени приложения-функции. Ознакомьтесь с разделом [Создание приложения-функции](functions-infrastructure-as-code.md#create-a-function-app).

|Ключ|Образец значения|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## <a name="websitemaxdynamicapplicationscaleout"></a>WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT

Максимальное число экземпляров, до которого можно развернуть приложение-функцию. По умолчанию ограничение не установлено.

> [!NOTE]
> Этот параметр доступен в качестве предварительной версии функции. Надежная работа обеспечивается только при его значении не больше 5.

|Ключ|Образец значения|
|---|------------|
|WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT|5|

## <a name="websitenodedefaultversion"></a>WEBSITE\_NODE\_DEFAULT_VERSION

Значение по умолчанию — "8.11.1".

|Ключ|Образец значения|
|---|------------|
|WEBSITE\_NODE\_DEFAULT_VERSION|8.11.1|

## <a name="websiterunfrompackage"></a>WEBSITE\_RUN\_FROM\_PACKAGE

Позволяет приложению-функции запуск из файла подключенного пакета.

|Ключ|Образец значения|
|---|------------|
|WEBSITE\_RUN\_FROM\_PACKAGE|1|

Допустимые значения: или URL-адрес, который разрешается в путь файла пакета развертывания, или `1`. Если задано значение `1`, пакет должен быть в папке `d:\home\data\SitePackages`. При использовании развертывания из ZIP-файла с этим параметром пакет автоматически передается в это расположение. В предварительной версии этот параметр называется `WEBSITE_RUN_FROM_ZIP`. Дополнительные сведения см. в статье [Запуск Функций Azure из файла пакета](run-functions-from-deployment-package.md).

## <a name="azurefunctionproxydisablelocalcall"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

По умолчанию прокси-серверы Функций будут использовать команду для отправки вызовов API из них самих непосредственно в функции в одном приложении-функции, а не создавать новый HTTP-запрос. Этот параметр позволяет отключить такую реакцию на событие.

|Ключ|Значение|Описание|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|Да|Вызовы с URL-адресом внутреннего сервера, который указывает на функцию в локальном приложении-функции, больше не будут отправляться непосредственно в функцию, а вместо этого будут направляться во внешний интерфейс HTTP для приложения-функции|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|false|Это значение по умолчанию. Вызовы с URL-адреса внутреннего сервера, указывающего на функцию в локальном приложении-функции, будут перенаправляться непосредственно к этой Функции|


## <a name="azurefunctionproxybackendurldecodeslashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

Этот параметр контролирует, декодируется ли сочетание знаков %2F как косая черта в параметрах маршрута, если оно вставлено в URL-адрес внутреннего сервера. 

|Ключ|Значение|Описание|
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
|Декодирование URL-адреса|Вход|Выход|
|-|-|-|
|Да|myfunction.com/test%2fapi|example.com/test/api
|false|myfunction.com/test%2fapi|example.com/test%2fapi|


## <a name="next-steps"></a>Дальнейшие действия

[Узнайте, как обновлять параметры приложения](functions-how-to-use-azure-function-app-settings.md#settings)

[Ознакомьтесь с глобальными параметрами в файле host.json](functions-host-json.md)

[См. другие параметры приложения для приложений Службы приложений Azure](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
