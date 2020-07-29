---
title: Схема ресурсов на основе рабочей области Azure Monitor Application Insights
description: Сведения о новой структуре и схеме таблицы для Azure Monitor Application Insights ресурсов на основе рабочих областей.
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/09/2020
ms.openlocfilehash: 1d7275c928b4d25e200a3a8d3d690c7575c056e7
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87323185"
---
# <a name="workspace-based-resource-changes-preview"></a>Изменения ресурсов на основе рабочей области (Предварительная версия)

До появления [Application Insights ресурсов на основе рабочей области](create-workspace-resource.md)Application Insights данные хранились отдельно от других данных журнала в Azure Monitor. Они основаны на обозреватель данных Azure и используют один и тот же язык запросов Kusto (ККЛ). Это описано в разделе [журналы в Azure Monitor](../platform/data-platform-logs.md).

Данные ресурсов Application Insights на основе рабочих областей хранятся в Log Analytics рабочей области с другими данными мониторинга и данными приложений. Это упрощает настройку, позволяя более легко анализировать данные в нескольких решениях и использовать возможности рабочих областей.

## <a name="table-structure"></a>Структура таблицы

| Устаревшее имя таблицы | Имя новой таблицы | Описание |
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

|ApplicationInsights|Тип|LogAnalytics|Тип|
|:---|:---|:---|:---|
|appId|строка|\_ResourceGUID|строка|
|application_Version|строка|AppVersion|строка|
|appName|строка|\_ResourceId|строка|
|client_Browser|строка|ClientBrowser|строка|
|client_City|строка|клиентЦити|строка|
|client_CountryOrRegion|строка|клиенткаунтрйоррегион|строка|
|client_IP|строка|ClientIP|строка|
|client_Model|строка|клиентмодел|строка|
|client_OS|строка|клиентос|строка|
|client_StateOrProvince|строка|клиентстатеорпровинце|строка|
|client_Type|строка|клиенттипе|строка|
|cloud_RoleInstance|строка|аппролеинстанце|строка|
|cloud_RoleName|строка|аппроленаме|строка|
|customDimensions|Динамический|Свойства|Динамический|
|кустоммеасурементс|Динамический|Измерения|Динамический|
|длительность|real|DurationMs|real|
|`id`|строка|`Id`|строка|
|iKey|строка|IKey|строка|
|itemCount|INT|ItemCount|INT|
|itemId|строка|\_ItemId|строка|
|itemType|строка|Тип|Строка|
|location|строка|Расположение|строка|
|message|строка|Сообщение|строка|
|name|строка|Название|строка|
|operation_Id|строка|Операции|строка|
|operation_Name|строка|OperationName|строка|
|operation_ParentId|строка|оператионпарентид|строка|
|operation_SyntheticSource|строка|оператионсинсетиксаурце|строка|
|перформанцебуккет|строка|перформанцебуккет|строка|
|sdkVersion|строка|SdkVersion|строка|
|session_Id|строка|SessionId|строка|
|размер;|real|Размер|real|
|Успешное завершение|строка|Успешно|Bool|
|TIMESTAMP|DATETIME|TimeGenerated|DATETIME|
|user_AccountId|строка|UserAccountId|строка|
|user_AuthenticatedId|строка|усераусентикатедид|строка|
|user_Id|строка|UserId|строка|

### <a name="appbrowsertimings"></a>аппбровсертимингс

Устаревшая таблица: Бровсертимингс

|ApplicationInsights|Тип|LogAnalytics|Тип|
|:---|:---|:---|:---|
|appId|строка|\_ResourceGUID|строка|
|application_Version|строка|AppVersion|строка|
|appName|строка|\_ResourceId|строка|
|client_Browser|строка|ClientBrowser|строка|
|client_City|строка|клиентЦити|строка|
|client_CountryOrRegion|строка|клиенткаунтрйоррегион|строка|
|client_IP|строка|ClientIP|строка|
|client_Model|строка|клиентмодел|строка|
|client_OS|строка|клиентос|строка|
|client_StateOrProvince|строка|клиентстатеорпровинце|строка|
|client_Type|строка|клиенттипе|строка|
|cloud_RoleInstance|строка|аппролеинстанце|строка|
|cloud_RoleName|строка|аппроленаме|строка|
|customDimensions|Динамический|Свойства|Динамический|
|кустоммеасурементс|Динамический|Измерения|Динамический|
|iKey|строка|IKey|строка|
|itemCount|INT|ItemCount|INT|
|itemId|строка|\_ItemId|строка|
|itemType|строка|Тип|строка|
|name|строка|Имя|DATETIME|
|нетворкдуратион|real|нетворкдуратионмс|real|
|operation_Id|строка|Операции|строка|
|operation_Name|строка|OperationName|строка|
|operation_ParentId|строка|оператионпарентид|строка|
|operation_SyntheticSource|строка|оператионсинсетиксаурце|строка|
|перформанцебуккет|строка|перформанцебуккет|строка|
|процессингдуратион|real|процессингдуратионмс|real|
|рецеиведуратион|real|рецеиведуратионмс|real|
|sdkVersion|строка|SdkVersion|строка|
|сенддуратион|real|сенддуратионмс|real|
|session_Id|строка|SessionId|строка|
|TIMESTAMP|DATETIME|TimeGenerated|DATETIME|
|totalDuration|real|тоталдуратионмс|real|
|url|строка|Url|строка|
|user_AccountId|строка|UserAccountId|строка|
|user_AuthenticatedId|строка|усераусентикатедид|строка|
|user_Id|строка|UserId|строка|

### <a name="appdependencies"></a>аппдепенденЦиес

Устаревшая таблица: зависимости

|ApplicationInsights|Тип|LogAnalytics|Тип|
|:---|:---|:---|:---|
|appId|строка|\_ResourceGUID|строка|
|application_Version|строка|AppVersion|строка|
|appName|строка|\_ResourceId|строка|
|client_Browser|строка|ClientBrowser|строка|
|client_City|строка|клиентЦити|строка|
|client_CountryOrRegion|строка|клиенткаунтрйоррегион|строка|
|client_IP|строка|ClientIP|строка|
|client_Model|строка|клиентмодел|строка|
|client_OS|строка|клиентос|строка|
|client_StateOrProvince|строка|клиентстатеорпровинце|строка|
|client_Type|строка|клиенттипе|строка|
|cloud_RoleInstance|строка|аппролеинстанце|строка|
|cloud_RoleName|строка|аппроленаме|строка|
|customDimensions|Динамический|Свойства|Динамический|
|кустоммеасурементс|Динамический|Измерения|Динамический|
|.|строка|Данные|строка|
|длительность|real|DurationMs|real|
|`id`|строка|`Id`|строка|
|iKey|строка|IKey|строка|
|itemCount|INT|ItemCount|INT|
|itemId|строка|\_ItemId|строка|
|itemType|строка|Тип|Строка|
|name|строка|Название|строка|
|operation_Id|строка|Операции|строка|
|operation_Name|строка|OperationName|строка|
|operation_ParentId|строка|оператионпарентид|строка|
|operation_SyntheticSource|строка|оператионсинсетиксаурце|строка|
|перформанцебуккет|строка|перформанцебуккет|строка|
|resultCode|строка|ResultCode|строка|
|sdkVersion|строка|SdkVersion|строка|
|session_Id|строка|SessionId|строка|
|Успешное завершение|строка|Успешно|Bool|
|target|строка|Назначение|строка|
|TIMESTAMP|DATETIME|TimeGenerated|DATETIME|
|type|строка|DependencyType|строка|
|user_AccountId|строка|UserAccountId|строка|
|user_AuthenticatedId|строка|усераусентикатедид|строка|
|user_Id|строка|UserId|строка|

### <a name="appevents"></a>аппевентс

Устаревшая таблица: customEvents

|ApplicationInsights|Тип|LogAnalytics|Тип|
|:---|:---|:---|:---|
|appId|строка|\_ResourceGUID|строка|
|application_Version|строка|AppVersion|строка|
|appName|строка|\_ResourceId|строка|
|client_Browser|строка|ClientBrowser|строка|
|client_City|строка|клиентЦити|строка|
|client_CountryOrRegion|строка|клиенткаунтрйоррегион|строка|
|client_IP|строка|ClientIP|строка|
|client_Model|строка|клиентмодел|строка|
|client_OS|строка|клиентос|строка|
|client_StateOrProvince|строка|клиентстатеорпровинце|строка|
|client_Type|строка|клиенттипе|строка|
|cloud_RoleInstance|строка|аппролеинстанце|строка|
|cloud_RoleName|строка|аппроленаме|строка|
|customDimensions|Динамический|Свойства|Динамический|
|кустоммеасурементс|Динамический|Измерения|Динамический|
|iKey|строка|IKey|строка|
|itemCount|INT|ItemCount|INT|
|itemId|строка|\_ItemId|строка|
|itemType|строка|Тип|строка|
|name|строка|Название|строка|
|operation_Id|строка|Операции|строка|
|operation_Name|строка|OperationName|строка|
|operation_ParentId|строка|оператионпарентид|строка|
|operation_SyntheticSource|строка|оператионсинсетиксаурце|строка|
|sdkVersion|строка|SdkVersion|строка|
|session_Id|строка|SessionId|строка|
|TIMESTAMP|DATETIME|TimeGenerated|DATETIME|
|user_AccountId|строка|UserAccountId|строка|
|user_AuthenticatedId|строка|усераусентикатедид|строка|
|user_Id|строка|UserId|строка|

### <a name="appmetrics"></a>аппметрикс

Устаревшая таблица: customMetrics

|ApplicationInsights|Тип|LogAnalytics|Тип|
|:---|:---|:---|:---|
|appId|строка|\_ResourceGUID|строка|
|application_Version|строка|AppVersion|строка|
|appName|строка|\_ResourceId|строка|
|client_Browser|строка|ClientBrowser|строка|
|client_City|строка|клиентЦити|строка|
|client_CountryOrRegion|строка|клиенткаунтрйоррегион|строка|
|client_IP|строка|ClientIP|строка|
|client_Model|строка|клиентмодел|строка|
|client_OS|строка|клиентос|строка|
|client_StateOrProvince|строка|клиентстатеорпровинце|строка|
|client_Type|строка|клиенттипе|строка|
|cloud_RoleInstance|строка|аппролеинстанце|строка|
|cloud_RoleName|строка|аппроленаме|строка|
|customDimensions|Динамический|Свойства|Динамический|
|iKey|строка|IKey|строка|
|itemId|строка|\_ItemId|строка|
|itemType|строка|Тип|строка|
|name|строка|Название|строка|
|operation_Id|строка|Операции|строка|
|operation_Name|строка|OperationName|строка|
|operation_ParentId|строка|оператионпарентид|строка|
|operation_SyntheticSource|строка|оператионсинсетиксаурце|строка|
|sdkVersion|строка|SdkVersion|строка|
|session_Id|строка|SessionId|строка|
|TIMESTAMP|DATETIME|TimeGenerated|DATETIME|
|user_AccountId|строка|UserAccountId|строка|
|user_AuthenticatedId|строка|усераусентикатедид|строка|
|user_Id|строка|UserId|строка|
|Значение|real|удален||
|валуекаунт|INT|валуекаунт|INT|
|валуемакс|real|валуемакс|real|
|валуемин|real|валуемин|real|
|валуестддев|real|валуестддев|real|
|валуесум|real|валуесум|real|

### <a name="apppageviews"></a>апппажевиевс

Устаревшая таблица: pageViews

|ApplicationInsights|Тип|LogAnalytics|Тип|
|:---|:---|:---|:---|
|appId|строка|\_ResourceGUID|строка|
|application_Version|строка|AppVersion|строка|
|appName|строка|\_ResourceId|строка|
|client_Browser|строка|ClientBrowser|строка|
|client_City|строка|клиентЦити|строка|
|client_CountryOrRegion|строка|клиенткаунтрйоррегион|строка|
|client_IP|строка|ClientIP|строка|
|client_Model|строка|клиентмодел|строка|
|client_OS|строка|клиентос|строка|
|client_StateOrProvince|строка|клиентстатеорпровинце|строка|
|client_Type|строка|клиенттипе|строка|
|cloud_RoleInstance|строка|аппролеинстанце|строка|
|cloud_RoleName|строка|аппроленаме|строка|
|customDimensions|Динамический|Свойства|Динамический|
|кустоммеасурементс|Динамический|Измерения|Динамический|
|длительность|real|DurationMs|real|
|`id`|строка|`Id`|строка|
|iKey|строка|IKey|строка|
|itemCount|INT|ItemCount|INT|
|itemId|строка|\_ItemId|строка|
|itemType|строка|Тип|Строка|
|name|строка|Название|строка|
|operation_Id|строка|Операции|строка|
|operation_Name|строка|OperationName|строка|
|operation_ParentId|строка|оператионпарентид|строка|
|operation_SyntheticSource|строка|оператионсинсетиксаурце|строка|
|перформанцебуккет|строка|перформанцебуккет|строка|
|sdkVersion|строка|SdkVersion|строка|
|session_Id|строка|SessionId|строка|
|TIMESTAMP|DATETIME|TimeGenerated|DATETIME|
|url|строка|Url|строка|
|user_AccountId|строка|UserAccountId|строка|
|user_AuthenticatedId|строка|усераусентикатедид|строка|
|user_Id|строка|UserId|строка|

### <a name="appperformancecounters"></a>аппперформанцекаунтерс

Устаревшая таблица: performanceCounters

|ApplicationInsights|Тип|LogAnalytics|Тип|
|:---|:---|:---|:---|
|appId|строка|\_ResourceGUID|строка|
|application_Version|строка|AppVersion|строка|
|appName|строка|\_ResourceId|строка|
|категория|строка|Категория|строка|
|client_Browser|строка|ClientBrowser|строка|
|client_City|строка|клиентЦити|строка|
|client_CountryOrRegion|строка|клиенткаунтрйоррегион|строка|
|client_IP|строка|ClientIP|строка|
|client_Model|строка|клиентмодел|строка|
|client_OS|строка|клиентос|строка|
|client_StateOrProvince|строка|клиентстатеорпровинце|строка|
|client_Type|строка|клиенттипе|строка|
|cloud_RoleInstance|строка|аппролеинстанце|строка|
|cloud_RoleName|строка|аппроленаме|строка|
|Счетчик|строка|удален||
|customDimensions|Динамический|Свойства|Динамический|
|iKey|строка|IKey|строка|
|экземпляр|строка|Экземпляр|строка|
|itemId|строка|\_ItemId|строка|
|itemType|строка|Тип|строка|
|name|строка|Название|строка|
|operation_Id|строка|Операции|строка|
|operation_Name|строка|OperationName|строка|
|operation_ParentId|строка|оператионпарентид|строка|
|operation_SyntheticSource|строка|оператионсинсетиксаурце|строка|
|sdkVersion|строка|SdkVersion|строка|
|session_Id|строка|SessionId|строка|
|TIMESTAMP|DATETIME|TimeGenerated|DATETIME|
|user_AccountId|строка|UserAccountId|строка|
|user_AuthenticatedId|строка|усераусентикатедид|строка|
|user_Id|строка|UserId|строка|
|Значение|real|Значение|real|

### <a name="apprequests"></a>аппрекуестс

Устаревшая таблица: запросы

|ApplicationInsights|Тип|LogAnalytics|Тип|
|:---|:---|:---|:---|
|appId|строка|\_ResourceGUID|строка|
|application_Version|строка|AppVersion|строка|
|appName|строка|\_ResourceId|строка|
|client_Browser|строка|ClientBrowser|строка|
|client_City|строка|клиентЦити|строка|
|client_CountryOrRegion|строка|клиенткаунтрйоррегион|строка|
|client_IP|строка|ClientIP|строка|
|client_Model|строка|клиентмодел|строка|
|client_OS|строка|клиентос|строка|
|client_StateOrProvince|строка|клиентстатеорпровинце|строка|
|client_Type|строка|клиенттипе|строка|
|cloud_RoleInstance|строка|аппролеинстанце|строка|
|cloud_RoleName|строка|аппроленаме|строка|
|customDimensions|Динамический|Свойства|Динамический|
|кустоммеасурементс|Динамический|Измерения|Динамический|
|длительность|real|DurationMs|Real|
|`id`|строка|`Id`|Тип String|
|iKey|строка|IKey|строка|
|itemCount|INT|ItemCount|INT|
|itemId|строка|\_ItemId|строка|
|itemType|строка|Тип|Строка|
|name|строка|Имя|Тип String|
|operation_Id|строка|Операции|строка|
|operation_Name|строка|OperationName|строка|
|operation_ParentId|строка|оператионпарентид|строка|
|operation_SyntheticSource|строка|оператионсинсетиксаурце|строка|
|перформанцебуккет|строка|перформанцебуккет|Тип String|
|resultCode|строка|ResultCode|Тип String|
|sdkVersion|строка|SdkVersion|строка|
|session_Id|строка|SessionId|строка|
|source|строка|Источник|Тип String|
|Успешное завершение|строка|Успешно|Bool|
|TIMESTAMP|DATETIME|TimeGenerated|DATETIME|
|url|строка|Url|Тип String|
|user_AccountId|строка|UserAccountId|строка|
|user_AuthenticatedId|строка|усераусентикатедид|строка|
|user_Id|строка|UserId|строка|

### <a name="appsystemevents"></a>аппсистемевентс

Устаревшая таблица: исключения

|ApplicationInsights|Тип|LogAnalytics|Тип|
|:---|:---|:---|:---|
|appId|строка|\_ResourceGUID|строка|
|application_Version|строка|AppVersion|строка|
|appName|строка|\_ResourceId|строка|
|сборка|строка|Сборка|строка|
|client_Browser|строка|ClientBrowser|строка|
|client_City|строка|клиентЦити|строка|
|client_CountryOrRegion|строка|клиенткаунтрйоррегион|строка|
|client_IP|строка|ClientIP|строка|
|client_Model|строка|клиентмодел|строка|
|client_OS|строка|клиентос|строка|
|client_StateOrProvince|строка|клиентстатеорпровинце|строка|
|client_Type|строка|клиенттипе|строка|
|cloud_RoleInstance|строка|аппролеинстанце|строка|
|cloud_RoleName|строка|аппроленаме|строка|
|customDimensions|Динамический|Свойства|Динамический|
|кустоммеасурементс|Динамический|Измерения|Динамический|
|подробности|Динамический|Сведения|Динамический|
|handledAt|строка|хандледат|строка|
|iKey|строка|IKey|строка|
|иннермостассембли|строка|иннермостассембли|строка|
|иннермостмессаже|строка|иннермостмессаже|строка|
|иннермостмесод|строка|иннермостмесод|строка|
|иннермосттипе|строка|иннермосттипе|строка|
|itemCount|INT|ItemCount|INT|
|itemId|строка|\_ItemId|строка|
|itemType|строка|Тип|строка|
|message|строка|Сообщение|строка|
|method|строка|Метод|строка|
|operation_Id|строка|Операции|строка|
|operation_Name|строка|OperationName|строка|
|operation_ParentId|строка|оператионпарентид|строка|
|operation_SyntheticSource|строка|оператионсинсетиксаурце|строка|
|аутерассембли|строка|аутерассембли|строка|
|аутермессаже|строка|аутермессаже|строка|
|аутермесод|строка|аутермесод|строка|
|аутертипе|строка|аутертипе|строка|
|проблемид|строка|проблемид|строка|
|sdkVersion|строка|SdkVersion|строка|
|session_Id|строка|SessionId|строка|
|северитилевел|INT|SeverityLevel|INT|
|TIMESTAMP|DATETIME|TimeGenerated|DATETIME|
|type|строка|ExceptionType|строка|
|user_AccountId|строка|UserAccountId|строка|
|user_AuthenticatedId|строка|усераусентикатедид|строка|
|user_Id|строка|UserId|строка|

### <a name="apptraces"></a>апптрацес

Устаревшая таблица: трассировки

|ApplicationInsights|Тип|LogAnalytics|Тип|
|:---|:---|:---|:---|
|appId|строка|\_ResourceGUID|строка|
|application_Version|строка|AppVersion|строка|
|appName|строка|\_ResourceId|строка|
|client_Browser|строка|ClientBrowser|строка|
|client_City|строка|клиентЦити|строка|
|client_CountryOrRegion|строка|клиенткаунтрйоррегион|строка|
|client_IP|строка|ClientIP|строка|
|client_Model|строка|клиентмодел|строка|
|client_OS|строка|клиентос|строка|
|client_StateOrProvince|строка|клиентстатеорпровинце|строка|
|client_Type|строка|клиенттипе|строка|
|cloud_RoleInstance|строка|аппролеинстанце|строка|
|cloud_RoleName|строка|аппроленаме|строка|
|customDimensions|Динамический|Свойства|Динамический|
|кустоммеасурементс|Динамический|Измерения|Динамический|
|iKey|строка|IKey|строка|
|itemCount|INT|ItemCount|INT|
|itemId|строка|\_ItemId|строка|
|itemType|строка|Тип|строка|
|message|строка|Сообщение|строка|
|operation_Id|строка|Операции|строка|
|operation_Name|строка|OperationName|строка|
|operation_ParentId|строка|оператионпарентид|строка|
|operation_SyntheticSource|строка|оператионсинсетиксаурце|строка|
|sdkVersion|строка|SdkVersion|строка|
|session_Id|строка|SessionId|строка|
|северитилевел|INT|SeverityLevel|INT|
|TIMESTAMP|DATETIME|TimeGenerated|DATETIME|
|user_AccountId|строка|UserAccountId|строка|
|user_AuthenticatedId|строка|усераусентикатедид|строка|
|user_Id|строка|UserId|строка|

## <a name="next-steps"></a>Дальнейшие действия

* [Изучение метрик](../platform/metrics-charts.md)
* [Написание запросов аналитики](../log-query/log-query-overview.md)

