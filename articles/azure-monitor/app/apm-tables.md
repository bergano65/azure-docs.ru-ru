---
title: Схема ресурсов на основе рабочей области Azure Monitor Application Insights
description: Сведения о новой структуре и схеме таблицы для Azure Monitor Application Insights ресурсов на основе рабочих областей.
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/09/2020
ms.openlocfilehash: 21f387a87224615ea6afbdce620c56e3ad2cc6ea
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "83210545"
---
# <a name="workspace-based-resource-changes-preview"></a>Изменения ресурсов на основе рабочей области (Предварительная версия)

До появления [Application Insights ресурсов на основе рабочей области](create-workspace-resource.md)Application Insights данные хранились отдельно от других данных журнала в Azure Monitor. Они основаны на обозреватель данных Azure и используют один и тот же язык запросов Kusto (ККЛ). Это описано в разделе [журналы в Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs).

Данные ресурсов Application Insights на основе рабочих областей хранятся в Log Analytics рабочей области с другими данными мониторинга и данными приложений. Это упрощает настройку, позволяя более легко анализировать данные в нескольких решениях и использовать возможности рабочих областей.

## <a name="table-structure"></a>Структура таблицы

| Устаревшее имя таблицы | Имя новой таблицы | Описание: |
|:---|:---|:---|
| availabilityResults | аппаваилабилитиресултс |  Сводные данные из тестов доступности.|
| бровсертимингс | аппбровсертимингс | Данные о производительности клиента, например время, затраченное на обработку входящих данных.|
| зависимости | аппдепенденЦиес | Вызовы из приложения в другие компоненты (включая внешние компоненты), записанные через TrackDependency (), например вызовы REST API, базы данных или файловой системы.  |
| customEvents | аппевентс | Пользовательские события, созданные приложением. |
| customMetrics | аппметрикс | Пользовательские метрики, созданные приложением. |
| pageViews | апппажевиевс| Данные о каждом представлении веб-сайта с информацией браузера. |
| performanceCounters | аппперформанцекаунтерс | Измерения производительности от ресурсов вычислений, поддерживающих приложение, например счетчики производительности Windows. |
| requests | аппрекуестс | Запросы, полученные приложением. Например, отдельная запись запроса записывается в журнал для каждого HTTP-запроса, получаемого веб – приложением.  |
| исключения | аппсистемевентс | Исключения, вызываемые средой выполнения приложения, захватывают исключения как на стороне сервера, так и на стороне клиента (браузеров). |
| traces | апптрацес | Подробные журналы (трассировки), выдаваемые с помощью платформ кода приложения и ведения журналов, записанных с помощью TrackTrace (). |

## <a name="table-schemas"></a>Схемы таблиц

В следующих разделах показано сопоставление между классическими именами свойств и новыми именами свойств Application Insights на основе рабочей области.  Используйте эти сведения для преобразования любых запросов, использующих устаревшие таблицы.

Большинство столбцов имеют одинаковые имена с разной прописной буквой. Так как ККЛ учитывает регистр, необходимо изменить имя каждого столбца вместе с именами таблиц в существующих запросах. Столбцы с изменениями в дополнение к капитализации выделены. Вы по-прежнему можете использовать классические Application Insights запросы на панели **журналы** ресурса Application Insights, даже если это ресурс на основе рабочей области. Новые имена свойств необходимы для выполнения запросов в контексте Log Analytics рабочей области.

### <a name="appavailabilityresults"></a>аппаваилабилитиресултс

Устаревшая таблица: доступность

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|клиентЦити|string|
|client_CountryOrRegion|string|клиенткаунтрйоррегион|string|
|client_IP|string|ClientIP|string|
|client_Model|string|клиентмодел|string|
|client_OS|string|клиентос|string|
|client_StateOrProvince|string|клиентстатеорпровинце|string|
|client_Type|string|клиенттипе|string|
|cloud_RoleInstance|string|аппролеинстанце|string|
|cloud_RoleName|string|аппроленаме|string|
|customDimensions|Динамический|Элемент Property|Динамический|
|кустоммеасурементс|Динамический|Измерения|Динамический|
|длительность|real|DurationMs|real|
|`id`|string|`Id`|string|
|iKey|string|IKey|string|
|itemCount|INT|ItemCount|INT|
|itemId|string|\_ItemId|string|
|itemType|string|Type|Строка|
|location|string|Расположение|string|
|message|строка|Сообщение|string|
|name|string|Название|string|
|operation_Id|string|Операции|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|оператионпарентид|string|
|operation_SyntheticSource|string|оператионсинсетиксаурце|string|
|перформанцебуккет|string|перформанцебуккет|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|размер;|real|Размер|real|
|Успешное завершение|string|Успех|Bool|
|TIMESTAMP|DATETIME|TimeGenerated|DATETIME|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|усераусентикатедид|string|
|user_Id|string|UserId|string|

### <a name="appbrowsertimings"></a>аппбровсертимингс

Устаревшая таблица: Бровсертимингс

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|клиентЦити|string|
|client_CountryOrRegion|string|клиенткаунтрйоррегион|string|
|client_IP|string|ClientIP|string|
|client_Model|string|клиентмодел|string|
|client_OS|string|клиентос|string|
|client_StateOrProvince|string|клиентстатеорпровинце|string|
|client_Type|string|клиенттипе|string|
|cloud_RoleInstance|string|аппролеинстанце|string|
|cloud_RoleName|string|аппроленаме|string|
|customDimensions|Динамический|Элемент Property|Динамический|
|кустоммеасурементс|Динамический|Измерения|Динамический|
|iKey|string|IKey|string|
|itemCount|INT|ItemCount|INT|
|itemId|string|\_ItemId|string|
|itemType|string|Тип|string|
|name|строка|name|DATETIME|
|нетворкдуратион|real|нетворкдуратионмс|real|
|operation_Id|string|Операции|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|оператионпарентид|string|
|operation_SyntheticSource|string|оператионсинсетиксаурце|string|
|перформанцебуккет|string|перформанцебуккет|string|
|процессингдуратион|real|процессингдуратионмс|real|
|рецеиведуратион|real|рецеиведуратионмс|real|
|sdkVersion|string|SdkVersion|string|
|сенддуратион|real|сенддуратионмс|real|
|session_Id|string|SessionId|string|
|TIMESTAMP|DATETIME|TimeGenerated|DATETIME|
|totalDuration|real|тоталдуратионмс|real|
|url|строка|Url|string|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|усераусентикатедид|string|
|user_Id|string|UserId|string|

### <a name="appdependencies"></a>аппдепенденЦиес

Устаревшая таблица: зависимости

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|клиентЦити|string|
|client_CountryOrRegion|string|клиенткаунтрйоррегион|string|
|client_IP|string|ClientIP|string|
|client_Model|string|клиентмодел|string|
|client_OS|string|клиентос|string|
|client_StateOrProvince|string|клиентстатеорпровинце|string|
|client_Type|string|клиенттипе|string|
|cloud_RoleInstance|string|аппролеинстанце|string|
|cloud_RoleName|string|аппроленаме|string|
|customDimensions|Динамический|Элемент Property|Динамический|
|кустоммеасурементс|Динамический|Измерения|Динамический|
|.|string|Данные|string|
|длительность|real|DurationMs|real|
|`id`|string|`Id`|string|
|iKey|string|IKey|string|
|itemCount|INT|ItemCount|INT|
|itemId|string|\_ItemId|string|
|itemType|string|Type|Строка|
|name|строка|Название|string|
|operation_Id|string|Операции|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|оператионпарентид|string|
|operation_SyntheticSource|string|оператионсинсетиксаурце|string|
|перформанцебуккет|string|перформанцебуккет|string|
|resultCode|string|ResultCode|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|Успешное завершение|string|Успех|Bool|
|target|string|целевого объекта|string|
|TIMESTAMP|DATETIME|TimeGenerated|DATETIME|
|type|string|DependencyType|string|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|усераусентикатедид|string|
|user_Id|string|UserId|string|

### <a name="appevents"></a>аппевентс

Устаревшая таблица: customEvents

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|клиентЦити|string|
|client_CountryOrRegion|string|клиенткаунтрйоррегион|string|
|client_IP|string|ClientIP|string|
|client_Model|string|клиентмодел|string|
|client_OS|string|клиентос|string|
|client_StateOrProvince|string|клиентстатеорпровинце|string|
|client_Type|string|клиенттипе|string|
|cloud_RoleInstance|string|аппролеинстанце|string|
|cloud_RoleName|string|аппроленаме|string|
|customDimensions|Динамический|Элемент Property|Динамический|
|кустоммеасурементс|Динамический|Измерения|Динамический|
|iKey|string|IKey|string|
|itemCount|INT|ItemCount|INT|
|itemId|string|\_ItemId|string|
|itemType|string|Тип|string|
|name|строка|Название|string|
|operation_Id|string|Операции|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|оператионпарентид|string|
|operation_SyntheticSource|string|оператионсинсетиксаурце|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|TIMESTAMP|DATETIME|TimeGenerated|DATETIME|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|усераусентикатедид|string|
|user_Id|string|UserId|string|

### <a name="appmetrics"></a>аппметрикс

Устаревшая таблица: customMetrics

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|клиентЦити|string|
|client_CountryOrRegion|string|клиенткаунтрйоррегион|string|
|client_IP|string|ClientIP|string|
|client_Model|string|клиентмодел|string|
|client_OS|string|клиентос|string|
|client_StateOrProvince|string|клиентстатеорпровинце|string|
|client_Type|string|клиенттипе|string|
|cloud_RoleInstance|string|аппролеинстанце|string|
|cloud_RoleName|string|аппроленаме|string|
|customDimensions|Динамический|Элемент Property|Динамический|
|iKey|string|IKey|string|
|itemId|string|\_ItemId|string|
|itemType|string|Тип|string|
|name|строка|Название|string|
|operation_Id|string|Операции|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|оператионпарентид|string|
|operation_SyntheticSource|string|оператионсинсетиксаурце|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|TIMESTAMP|DATETIME|TimeGenerated|DATETIME|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|усераусентикатедид|string|
|user_Id|string|UserId|string|
|value|real|удален||
|валуекаунт|INT|валуекаунт|INT|
|валуемакс|real|валуемакс|real|
|валуемин|real|валуемин|real|
|валуестддев|real|валуестддев|real|
|валуесум|real|валуесум|real|

### <a name="apppageviews"></a>апппажевиевс

Устаревшая таблица: pageViews

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|клиентЦити|string|
|client_CountryOrRegion|string|клиенткаунтрйоррегион|string|
|client_IP|string|ClientIP|string|
|client_Model|string|клиентмодел|string|
|client_OS|string|клиентос|string|
|client_StateOrProvince|string|клиентстатеорпровинце|string|
|client_Type|string|клиенттипе|string|
|cloud_RoleInstance|string|аппролеинстанце|string|
|cloud_RoleName|string|аппроленаме|string|
|customDimensions|Динамический|Элемент Property|Динамический|
|кустоммеасурементс|Динамический|Измерения|Динамический|
|длительность|real|DurationMs|real|
|`id`|string|`Id`|string|
|iKey|string|IKey|string|
|itemCount|INT|ItemCount|INT|
|itemId|string|\_ItemId|string|
|itemType|string|Type|Строка|
|name|строка|Название|string|
|operation_Id|string|Операции|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|оператионпарентид|string|
|operation_SyntheticSource|string|оператионсинсетиксаурце|string|
|перформанцебуккет|string|перформанцебуккет|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|TIMESTAMP|DATETIME|TimeGenerated|DATETIME|
|url|string|Url|string|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|усераусентикатедид|string|
|user_Id|string|UserId|string|

### <a name="appperformancecounters"></a>аппперформанцекаунтерс

Устаревшая таблица: performanceCounters

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|категория|строка|Категория|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|клиентЦити|string|
|client_CountryOrRegion|string|клиенткаунтрйоррегион|string|
|client_IP|string|ClientIP|string|
|client_Model|string|клиентмодел|string|
|client_OS|string|клиентос|string|
|client_StateOrProvince|string|клиентстатеорпровинце|string|
|client_Type|string|клиенттипе|string|
|cloud_RoleInstance|string|аппролеинстанце|string|
|cloud_RoleName|string|аппроленаме|string|
|Счетчик|string|удален||
|customDimensions|Динамический|Элемент Property|Динамический|
|iKey|string|IKey|string|
|экземпляр|string|Экземпляр|string|
|itemId|string|\_ItemId|string|
|itemType|string|Тип|string|
|name|строка|Название|string|
|operation_Id|string|Операции|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|оператионпарентид|string|
|operation_SyntheticSource|string|оператионсинсетиксаурце|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|TIMESTAMP|DATETIME|TimeGenerated|DATETIME|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|усераусентикатедид|string|
|user_Id|string|UserId|string|
|value|real|Значение|real|

### <a name="apprequests"></a>аппрекуестс

Устаревшая таблица: запросы

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|клиентЦити|string|
|client_CountryOrRegion|string|клиенткаунтрйоррегион|string|
|client_IP|string|ClientIP|string|
|client_Model|string|клиентмодел|string|
|client_OS|string|клиентос|string|
|client_StateOrProvince|string|клиентстатеорпровинце|string|
|client_Type|string|клиенттипе|string|
|cloud_RoleInstance|string|аппролеинстанце|string|
|cloud_RoleName|string|аппроленаме|string|
|customDimensions|Динамический|Элемент Property|Динамический|
|кустоммеасурементс|Динамический|Измерения|Динамический|
|длительность|real|DurationMs|Real|
|`id`|string|`Id`|Строка|
|iKey|string|IKey|string|
|itemCount|INT|ItemCount|INT|
|itemId|string|\_ItemId|string|
|itemType|string|Type|Строка|
|name|string|name|Строка|
|operation_Id|string|Операции|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|оператионпарентид|string|
|operation_SyntheticSource|string|оператионсинсетиксаурце|string|
|перформанцебуккет|string|перформанцебуккет|Строка|
|resultCode|string|ResultCode|Строка|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|source|string|Источник|Строка|
|Успешное завершение|string|Успех|Bool|
|TIMESTAMP|DATETIME|TimeGenerated|DATETIME|
|url|string|Url|Строка|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|усераусентикатедид|string|
|user_Id|string|UserId|string|

### <a name="appsystemevents"></a>аппсистемевентс

Устаревшая таблица: исключения

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|сборка|string|Сборка|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|клиентЦити|string|
|client_CountryOrRegion|string|клиенткаунтрйоррегион|string|
|client_IP|string|ClientIP|string|
|client_Model|string|клиентмодел|string|
|client_OS|string|клиентос|string|
|client_StateOrProvince|string|клиентстатеорпровинце|string|
|client_Type|string|клиенттипе|string|
|cloud_RoleInstance|string|аппролеинстанце|string|
|cloud_RoleName|string|аппроленаме|string|
|customDimensions|Динамический|Элемент Property|Динамический|
|кустоммеасурементс|Динамический|Измерения|Динамический|
|подробности|Динамический|Сведения|Динамический|
|handledAt|string|хандледат|string|
|iKey|string|IKey|string|
|иннермостассембли|string|иннермостассембли|string|
|иннермостмессаже|string|иннермостмессаже|string|
|иннермостмесод|string|иннермостмесод|string|
|иннермосттипе|string|иннермосттипе|string|
|itemCount|INT|ItemCount|INT|
|itemId|string|\_ItemId|string|
|itemType|string|Тип|string|
|message|строка|Сообщение|string|
|method|string|Метод|string|
|operation_Id|string|Операции|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|оператионпарентид|string|
|operation_SyntheticSource|string|оператионсинсетиксаурце|string|
|аутерассембли|string|аутерассембли|string|
|аутермессаже|string|аутермессаже|string|
|аутермесод|string|аутермесод|string|
|аутертипе|string|аутертипе|string|
|проблемид|string|проблемид|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|северитилевел|INT|SeverityLevel|INT|
|TIMESTAMP|DATETIME|TimeGenerated|DATETIME|
|type|string|ExceptionType|string|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|усераусентикатедид|string|
|user_Id|string|UserId|string|

### <a name="apptraces"></a>апптрацес

Устаревшая таблица: трассировки

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|клиентЦити|string|
|client_CountryOrRegion|string|клиенткаунтрйоррегион|string|
|client_IP|string|ClientIP|string|
|client_Model|string|клиентмодел|string|
|client_OS|string|клиентос|string|
|client_StateOrProvince|string|клиентстатеорпровинце|string|
|client_Type|string|клиенттипе|string|
|cloud_RoleInstance|string|аппролеинстанце|string|
|cloud_RoleName|string|аппроленаме|string|
|customDimensions|Динамический|Элемент Property|Динамический|
|кустоммеасурементс|Динамический|Измерения|Динамический|
|iKey|string|IKey|string|
|itemCount|INT|ItemCount|INT|
|itemId|string|\_ItemId|string|
|itemType|string|Тип|string|
|message|строка|Сообщение|string|
|operation_Id|string|Операции|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|оператионпарентид|string|
|operation_SyntheticSource|string|оператионсинсетиксаурце|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|северитилевел|INT|SeverityLevel|INT|
|TIMESTAMP|DATETIME|TimeGenerated|DATETIME|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|усераусентикатедид|string|
|user_Id|string|UserId|string|

## <a name="next-steps"></a>Дальнейшие действия

* [Изучение метрик](../../azure-monitor/platform/metrics-charts.md)
* [Написание запросов аналитики](../../azure-monitor/app/analytics.md)