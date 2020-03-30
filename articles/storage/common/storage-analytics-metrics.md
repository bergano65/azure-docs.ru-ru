---
title: Показатели аналитики azure Storage (Классический)
description: Узнайте, как использовать метрики в хранилище Azure.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 897ae1fa474de8726ed0caa1def162a00e142dbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268409"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Показатели аналитики azure Storage (Классический)

Аналитика хранилища может хранить метрики, включающие сводную статистику по транзакциям и данные о емкости запросов к службе хранилища. Сообщения о транзакциях предоставляются как на уровне операций API, так и на уровне службы хранилища, а сообщения о емкости предоставляются на уровне службы хранилища. Данные метрик можно использовать для анализа использования службы хранилища, диагностики проблем с запросами к службе хранилища и повышения производительности приложений, использующих службу.  

 Метрики аналитики хранилища включены по умолчанию для новых учетных записей хранения. Настроить метрики можно на [портале Azure;](https://portal.azure.com/) для получения подробной информации смотрите [учетную запись хранилища на портале Azure](/azure/storage/storage-monitor-storage-account). Аналитику хранилища также можно включить программно через REST API или клиентскую библиотеку. Используйте операции Set Service Properties для включения аналитики хранения данных для каждой службы.  

> [!NOTE]
> Метрики аналитики хранения данных доступны для служб Blob, Queue, Table и File.
> Показатели аналитики хранения данных теперь являются классическими метриками. Корпорация Майкрософт рекомендует использовать [метрики хранения данных в Azure Monitor](storage-metrics-in-azure-monitor.md) вместо метрик аналитики хранения.

## <a name="transaction-metrics"></a>Метрики транзакций  
 Широкий набор данных записывается раз в час или раз в минуту для каждой службы хранилища и запрошенных операций API, включая входящие и выходящие данные, информацию о доступности, ошибки и процент категоризованных запросов. Полный список сведений о транзакциях см. в разделе [Схема таблицы для метрик аналитики хранилища](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

 Данные транзакций записываются на двух уровнях — уровне служб и уровне операций API. На уровне службы статистика, суммирующая все запрошенные операции API, записывается в сущность таблицы каждый час, даже если запросов к службе не было. На уровне операции API статистика записывается в сущность, только если в течение этого часа были запрошены операции.  

 Например, если вы выполняете операцию **GetBlob** в службе BLOB-объектов, метрики аналитики хранилища будут регистрировать запрос и включать его в объединенные данные как по службе BLOB-объектов, так и по операции **GetBlob**. Однако, если операция **GetBlob** не запрашивается в течение часа, сущность не будет написана *$MetricsTransactionsBlob* для этой операции.  

 Метрики транзакций записываются как для запросов пользователей, так и для запросов, сделанных самой аналитикой хранилища. Например, записываются запросы аналитики хранилища для записи сущностей журналов и таблиц.

## <a name="capacity-metrics"></a>Метрики емкости  

> [!NOTE]
>  В настоящее время метрики емкости доступны только для службы BLOB-объектов.

 Данные о емкости записываются ежедневно для службы BLOB-объектов учетной записи хранения, и при этом записываются две сущности таблицы. Одна сущность содержит статистику пользовательских данных, а другая — статистику для контейнера больших двоичных объектов `$logs` , используемого аналитикой хранилища. Таблица *$MetricsCapacityBlob* включает следующие статистики:  

- **Capacity**. Объем хранилища, используемый службой BLOB-объектов учетной записи хранения, в байтах.  
- **ContainerCount**. Количество контейнеров BLOB-объектов в службе BLOB-объектов учетной записи хранения.  
- **ObjectCount**. Количество зафиксированных и незафиксированных блочных или страничных больших двоичных объектов в службе BLOB-объектов учетной записи хранения.  

  Дополнительную информацию о метриках емкости см. в разделе [Схема таблицы для метрик аналитики хранилища](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

## <a name="how-metrics-are-stored"></a>Как хранятся метрики  

 Все данные метрик для каждой из служб хранилища хранятся в таблицах, зарезервированных для этой службы: одна таблица для данных о транзакциях, вторая для данных о минутных транзакциях и третья для данных о емкости. Информация о транзакциях и минутных транзакциях включает в себя данные запросов и ответов, а информация о емкости содержит данные об использовании хранилища. Метрики часов, метрики минут и емкость для службы BLOB-объектов учетной записи хранилища можно получить в таблицах, имена которых приведены в следующей таблице.  

|Уровень метрик|Имена таблиц|Поддерживается в версиях|  
|-------------------|-----------------|----------------------------|  
|Часовые метрики, основное расположение|- $MetricsTransactionsBlob<br />- $MetricsTransactionsTable<br />- $MetricsTransactionsQueue|Только версии до 15.08.2013 г. Таблицы с такими именами по-прежнему поддерживаются, но рекомендуется перейти на использование таблиц, приведенных ниже.|  
|Часовые метрики, основное расположение|- $MetricsHourPrimaryTransactionsBlob<br />- $MetricsHourPrimaryTransactionsTable<br />- $MetricsHourPrimaryTransactionsQueue<br />- $MetricsHourPrimaryTransactionsFile|все версии Поддержка метрик службы файлов доступна только в версии 2015-04-05 и позже.|  
|Минутные метрики, основное расположение|- $MetricsMinutePrimaryTransactionsBlob<br />- $MetricsMinutePrimaryTransactionsTable<br />- $MetricsMinutePrimaryTransactionsQueue<br />- $MetricsMinutePrimaryTransactionsFile|все версии Поддержка метрик службы файлов доступна только в версии 2015-04-05 и позже.|  
|Часовые метрики, вторичное расположение|- $MetricsHourSecondaryTransactionsBlob<br />- $MetricsHourSecondaryTransactionsTable<br />- $MetricsHourSecondaryTransactionsQueue|все версии Должна быть включена геоизбыточная репликация с доступом для чтения.|  
|Минутные метрики, вторичное расположение|- $MetricsMinuteSecondaryTransactionsBlob<br />- $MetricsMinuteSecondaryTransactionsTable<br />- $MetricsMinuteSecondaryTransactionsQueue|все версии Должна быть включена геоизбыточная репликация с доступом для чтения.|  
|Емкость (только для службы BLOB-объектов)|$MetricsCapacityBlob|все версии|  

 Эти таблицы автоматически создаются при включении analytics хранилища для конечной точки службы хранения данных. Они доступны через пространство имен учетной записи `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`хранения, например: . Таблицы метрик не отображаются в операции листинга и должны быть доступны непосредственно через имя таблицы.  

## <a name="enable-metrics-using-the-azure-portal"></a>Включить метрики с помощью портала Azure
Выполните следующие действия, чтобы включить метрики на [портале Azure](https://portal.azure.com).

1. Войдите в свою учетную запись хранения.
1. Выберите **настройки диагностики (классические)** из панели **меню.**
1. Убедитесь, что в разделе **Состояние** задано значение **Вкл**.
1. Выберите метрики для служб, за которыми вы хотите наблюдать.
1. Укажите политику хранения, чтобы задать период хранения метрик и данных журналов.
1. Нажмите кнопку **Сохранить**.

Сейчас [портал Azure](https://portal.azure.com) не позволяет настраивать минутные метрики в учетной записи хранения. Их необходимо включить с помощью PowerShell или программно.

## <a name="enable-storage-metrics-using-powershell"></a>Включить метрики хранения с помощью PowerShell  
Для настройки метрики хранения в учетной записи хранилища можно использовать PowerShell с помощью cmdlet **Az-AzStorageServiceMediaMetricMediaProperty get-AzStorageServiceMetricSProperty** для получения текущих настроек, а также для изменения текущих настроек. **Set-AzStorageServiceMetricsProperty**  

Командлеты, позволяющие управлять метриками хранилища, имеют следующие параметры.  

* **ServiceType**, возможное значение **Blob**, **Очередь**, **Таблица**, и **файл**.
* **MetricsType**, возможные значения **час** и **минута**.  
* **MetricsLevel,** возможные значения:
* **Нет**: Отключает мониторинг.
* **Служба**: Собирает такие показатели, как вход/выход, доступность, задержка и проценты успеха, которые агрегируются для капли, очереди, таблицы и файловых служб.
* **ServiceAndApi**: В дополнение к метрикам службы, собирает один и тот же набор метрик для каждой операции хранения в API службы хранения Azure.

Например, следующая команда переключает мельчайшие метрики для службы капли в учетной записи хранилища с периодом хранения, установленным до пяти дней: 

> [!NOTE]
> Эта команда предполагает, что вы зарегистрировались в подписке Azure с помощью `Connect-AzAccount` команды.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"

Set-AzStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5 -Context $storageAccount.Context
```  

* Замените `<resource-group-name>` значение заполнителя на имя группы ресурсов.
        
* Замените значение заполнителя `<storage-account-name>` именем вашей учетной записи хранения.



Следующая команда получает текущий уровень часовых метрик и длительность периода хранения в днях для службы BLOB-объектов в учетной записи хранения по умолчанию:  

```powershell
Get-AzStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob -Context $storagecontext.Context
```  

Дополнительные сведения о настройке командлетов Azure PowerShell для работы с подпиской Azure и о выборе учетной записи хранения по умолчанию см. в разделе [Установка и настройка Azure PowerShell](https://azure.microsoft.com/documentation/articles/install-configure-powershell/).  

## <a name="enable-storage-metrics-programmatically"></a>Включить метрики хранения программно  
Помимо использования портала Azure или cmdlets Azure PowerShell для управления метриками хранения, можно также использовать один из AAP хранения данных Azure. Например, если используется язык .NET, можно использовать клиентскую библиотеку хранилища.  

Классы **CloudBlobClient**, **Cloud'Uuclient,** **CloudTableClient**и **CloudFileClient** имеют такие методы, как **SetServiceProperties** и **SetServicePropertiesAsync,** которые принимают объект **ServiceProperties** в качестве параметра. Объект **ServiceProperties** может использоваться для настройки метрик хранилища. Например, следующий фрагмент кода C# показывает, как изменить уровень метрик и настроить количество дней хранения для часовых метрик очереди:  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

Для получения дополнительной информации об использовании языка .NET для настройки метрик хранения, [см.](https://msdn.microsoft.com/library/azure/mt347887.aspx)  

Для получения общей информации о настройке метрик хранения с помощью API REST [см.](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics)  

##  <a name="viewing-storage-metrics"></a>Просмотр метрик хранилища  
После настройки метрик аналитики хранилища для мониторинга учетной записи хранения функция аналитики хранилища записывает метрики в набор известных таблиц в учетной записи хранения. Вы можете настроить диаграммы для просмотра почасовых метрик на [портале Azure](https://portal.azure.com).

1. Войдите в свою учетную запись хранения на [портале Azure](https://portal.azure.com).
1. Выберите **метрики (классические)** в лезвии **меню** для службы, метрики которой вы хотите просмотреть.
1. Нажмите на диаграмму, на коннажную.
1. В колонке **Изменение диаграммы** щелкните **Диапазон времени**, **Тип диаграммы** и метрики, которые необходимо отобразить на диаграмме.

В **разделе «Мониторинг» (классический)** blade меню учетной записи хранилища на портале Azure можно настроить [правила оповещения,](#metrics-alerts)такие как отправка оповещений по электронной почте, чтобы уведомить вас, когда определенная метрика достигнет определенного значения.

Если вы хотите загрузить метрики для долгосрочного хранения или проанализировать их локально, необходимо использовать инструмент или написать код для чтения таблиц. Необходимо скачать минутные метрики для анализа. Таблицы не отображаются, если в учетной записи хранения выведены все таблицы. Однако к ним можно обращаться напрямую по имени. Многие инструменты хранения данных знают об этих таблицах и позволяют просматривать их напрямую (см. инструменты [клиента хранения данных Azure](/azure/storage/storage-explorers) для списка доступных инструментов).

||||  
|-|-|-|  
|**Метрики**|**Имена таблиц**|**Примечания**|  
|Часовые метрики|$MetricsHourPrimaryTransactionsBlob<br /><br /> $MetricsHourPrimaryTransactionsTable<br /><br /> $MetricsHourPrimaryTransactionsQueue<br /><br /> $MetricsHourPrimaryTransactionsFile|В версиях до 2013-08-15 эти таблицы носили следующие имена:<br /><br /> $MetricsTransactionsBlob <br /><br /> $MetricsTransactionsTable<br /><br />  $MetricsTransactionsQueue<br /><br /> Метрики для службы файла доступны начиная с версии 2015-04-05.|  
|Минутные метрики|$MetricsMinutePrimaryTransactionsBlob<br /><br /> $MetricsMinutePrimaryTransactionsTable<br /><br /> $MetricsMinutePrimaryTransactionsQueue<br /><br /> $MetricsMinutePrimaryTransactionsFile|Могут быть включены только с помощью PowerShell или программным способом.<br /><br /> Метрики для службы файла доступны начиная с версии 2015-04-05.|  
|Capacity|$MetricsCapacityBlob|Только для BLOB-службы.|  

Подробные сведения о схемах для этих таблиц см. в разделе [Схема таблицы метрик аналитики хранилища](/rest/api/storageservices/storage-analytics-metrics-table-schema). В примере строк ниже отражена только часть доступных столбцов, однако он иллюстрирует некоторые важные возможности сохранения метрик с помощью метрик хранилища:  

||||||||||||  
|-|-|-|-|-|-|-|-|-|-|-|  
|**PartitionKey**|**Rowkey**|**Timestamp**|**TotalRequests**|**TotalBillableRequests**|**TotalIngress**|**TotalEgress**|**Доступность**|**AverageE2ELatency**|**AverageServerLatency**|**PercentSuccess**|  
|20140522T1100|user;All|2014-05-22T11:01:16.7650250Z|7|7|4003|46801|100|104.4286|6.857143|100|  
|20140522T1100|user;QueryEntities|2014-05-22T11:01:16.7640250Z|5|5|2694|45951|100|143.8|7.8|100|  
|20140522T1100|user;QueryEntity|2014-05-22T11:01:16.7650250Z|1|1|538|633|100|3|3|100|  
|20140522T1100|user;UpdateEntity|2014-05-22T11:01:16.7650250Z|1|1|771|217|100|9|6|100|  

В этом примере данных минутной метрики для ключа раздела используется время с минутным разрешением. Ключ строки определяет тип данных, которые хранятся в строке, и состоит из двух фрагментов данных — типа доступа и типа запроса:  

-   Типом доступа является либо **user**, либо **system**, где **user** означает все запросы пользователей к службе хранения, а **system** означает запросы, выполненные средством аналитики хранилища.  

-   Типом запроса является либо **all**, в случае чего это строка сводки, либо он определяет конкретный API, например **QueryEntity** или **UpdateEntity**.  

В примере данных ниже показаны все записи за одну минуту (начиная с 11:00), и количество запросов **QueryEntities** плюс количество запросов **QueryEntity** плюс количество запросов **UpdateEntity** в сумме дает семь, и эта сумма отображается в строке **user:All**. Аналогичным образом можно определить среднюю сквозную задержку 104.4286 в строке **user:All**, вычислив ((143.8 * 5) + 3 + 9)/7.  

## <a name="metrics-alerts"></a>Оповещения метрик
Следует настроить оповещения на [портале Azure,](https://portal.azure.com) чтобы вас автоматически уведомили о важных изменениях в поведении служб хранения данных. Если скачивание этих метрик в формате с разделителями происходит с помощью средства обозревателя хранилища, для анализа данных можно использовать Microsoft Excel. Список доступных инструментов Storage Explorer см. в разделе [Клиентские инструменты службы хранилища Azure](/azure/storage/storage-explorers). Вы можете настроить оповещения в лезвии **оповещения (классическое),** доступном под **мониторингом (классический)** в лезвии меню меню хранилища.

> [!IMPORTANT]
> Возможна задержка между событием службы хранилища и записью данных соответствующей ежечасной или ежеминутной метрики. В случае ежеминутных метрик данные за несколько минут могут записываться одновременно. Это может привести к объединению транзакций за предшествующие минуты с транзакциями за текущую минуту. В этом случае у службы оповещений могут быть данные не всех доступных метрик за заданный интервал оповещения, что может привести к неожиданному срабатыванию оповещений.
>

## <a name="accessing-metrics-data-programmatically"></a>Программный доступ к данным метрик  
В следующем списке показан пример кода на C#, в котором реализован доступ к минутным метрикам для диапазона минут с отображением результатов в окне консоли. В примере кода используется версия библиотеки 4.x библиотеки хранения данных Azure, которая включает класс **CloudAnalyticsClient,** который упрощает доступ к таблицам метрик в хранилище.  

```csharp
private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)  
{  
 // Convert the dates to the format used in the PartitionKey  
 var start = startDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");  
 var end = endDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");  

 var services = Enum.GetValues(typeof(StorageService));  
 foreach (StorageService service in services)  
 {  
     Console.WriteLine("Minute Metrics for Service {0} from {1} to {2} UTC", service, start, end);  
     var metricsQuery = analyticsClient.CreateMinuteMetricsQuery(service, StorageLocation.Primary);  
     var t = analyticsClient.GetMinuteMetricsTable(service);  
     var opContext = new OperationContext();  
     var query =  
             from entity in metricsQuery  
             // Note, you can't filter using the entity properties Time, AccessType, or TransactionType  
             // because they are calculated fields in the MetricsEntity class.  
             // The PartitionKey identifies the DataTime of the metrics.  
             where entity.PartitionKey.CompareTo(start) >= 0 && entity.PartitionKey.CompareTo(end) <= 0   
             select entity;  

     // Filter on "user" transactions after fetching the metrics from Table Storage.  
     // (StartsWith is not supported using LINQ with Azure Table storage)  
     var results = query.ToList().Where(m => m.RowKey.StartsWith("user"));  
     var resultString = results.Aggregate(new StringBuilder(), (builder, metrics) => builder.AppendLine(MetricsString(metrics, opContext))).ToString();  
     Console.WriteLine(resultString);  
 }  
}  

private static string MetricsString(MetricsEntity entity, OperationContext opContext)  
{  
 var entityProperties = entity.WriteEntity(opContext);  
 var entityString =  
         string.Format("Time: {0}, ", entity.Time) +  
         string.Format("AccessType: {0}, ", entity.AccessType) +  
         string.Format("TransactionType: {0}, ", entity.TransactionType) +  
         string.Join(",", entityProperties.Select(e => new KeyValuePair<string, string>(e.Key.ToString(), e.Value.PropertyAsObject.ToString())));  
 return entityString;  
}  
```  

## <a name="billing-on-storage-metrics"></a>Выставление счетов по показателям хранения  
За запросы записи на создание сущностей таблиц для метрик взимается плата в соответствии со стандартными тарифами, применимыми ко всем операциям службы хранилища Azure.  

Чтение и удаление запросов данных метрик клиентом также выплачиваются по стандартным ставкам. Если вы настроили политику хранения данных, за удаление старых данных метрик хранилищем Azure плата не взимается. Однако при удалении данных аналитики с учетной записи будет взиматься плата за операции удаления.  

Емкость, используемая таблицами метрик, также является оплачиваемой. Можно использовать следующее для оценки объема емкости, используемой для хранения данных метрик:  

-   Если каждый час служба использует каждый API в каждой службе, то примерно 148 KB данных хранится каждый час в таблицах транзакций метрик, если вы включили резюме уровня обслуживания и API.  
-   Если в течение каждого часа служба использует каждый API в службе, то примерно 12 КБ данных хранится каждый час в таблицах транзакций метрик, если вы включили только резюме уровня обслуживания.  
-   Таблица емкости для капли имеет два строки добавил каждый день, при условии, что вы выбрали в для журналов. Это означает, что каждый день размер этой таблицы увеличивается примерно на 300 байт.

## <a name="next-steps"></a>Дальнейшие действия
* [Отслеживание учетной записи хранилища](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [Схема таблицы метрик аналитики хранилища](/rest/api/storageservices/storage-analytics-metrics-table-schema)   
* [Операции с протоколированием и сообщения о состоянии аналитик хранилища](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)   
* [Ведение журнала аналитики службы хранилища](storage-analytics-logging.md)
