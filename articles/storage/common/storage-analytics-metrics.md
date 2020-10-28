---
title: Метрики Аналитики Службы хранилища Azure (классические)
description: Узнайте, как использовать метрики Аналитики Службы хранилища в службе хранилища Azure. Сведения о метриках транзакций и емкости, способах хранения метрик, включении метрик и многом другим.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: f1ab2be598a24a2448fed44742733633a8e0fc8f
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/28/2020
ms.locfileid: "92787607"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Метрики Аналитики Службы хранилища Azure (классические)

Служба хранилища Azure использует Аналитику Службы хранилища для хранения метрик, включающих в себя сводную статистику по транзакциям и данные о емкости запросов к службе хранилища. Транзакции описываются на уровне операций API и на уровне службы хранилища. Емкость сообщается на уровне службы хранилища. Данные метрик можно использовать для:
- анализа использования службы хранилища;
- диагностики проблем с запросами к службе хранилища;
- повышения производительности приложений, использующих службу.

 Метрики аналитики хранилища включены по умолчанию для новых учетных записей хранения. Эти метрики можно настроить на [портале Azure](https://portal.azure.com/). Дополнительные сведения см. в статье [Мониторинг учетной записи хранения на портале Azure](./storage-monitor-storage-account.md). Аналитику хранилища также можно включить программно через REST API или клиентскую библиотеку. Используйте операции Set Service Properties, чтобы включить Аналитику Службы хранилища для каждой службы.  

> [!NOTE]
> Метрики Аналитики Службы хранилища доступны для хранилища BLOB-объектов Azure, хранилища очередей Azure, хранилища таблиц Azure и Файлов Azure.
> Метрики Аналитики Службы хранилища теперь являются классическими метриками. Рекомендуется использовать [метрики хранилища в Azure Monitor](../blobs/monitor-blob-storage.md) вместо метрик Аналитики Службы хранилища.

## <a name="transaction-metrics"></a>Метрики транзакций  
 Надежный набор данных записывается раз в час или раз в минуту для каждой службы хранилища и запрошенных операций API, включая входящие и исходящие данные, информацию о доступности, ошибки и процент категоризованных запросов. Полный список сведений о транзакциях см. в разделе [Схема таблицы для метрик аналитики хранилища](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

 Данные транзакций записываются на уровне службы и уровне операций API. На уровне службы статистика, суммирующая все запрошенные операции API, записывается в сущность таблицы каждый час, даже если запросов к службе не было. На уровне операции API статистика записывается в сущность, только если в течение этого часа были запрошены операции.  

 Например, если вы выполняете операцию **GetBlob** в службе BLOB-объектов, метрики Аналитики Службы хранилища будут регистрировать запрос и включать его в объединенные данные по службе BLOB-объектов и по операции **GetBlob** . Если за текущий час операций **GetBlob** не будет запрошено, сущность в *$MetricsTransactionsBlob* для этой операции записана не будет.  

 Метрики транзакций записываются как для запросов пользователей, так и для запросов, сделанных самой Аналитикой Службы хранилища. Например, записываются запросы аналитики хранилища для записи сущностей журналов и таблиц.

## <a name="capacity-metrics"></a>Метрики емкости  

> [!NOTE]
>  В настоящее время метрики емкости доступны только для службы BLOB-объектов.

 Данные о емкости записываются ежедневно для службы BLOB-объектов учетной записи хранения, при этом записываются две сущности таблицы. Одна сущность содержит статистику пользовательских данных, а другая — статистику для контейнера больших двоичных объектов `$logs` , используемого аналитикой хранилища. Таблица *$MetricsCapacityBlob* включает в себя следующую статистику.  

- **Capacity** . Объем хранилища, используемый службой BLOB-объектов учетной записи хранения, в байтах.  
- **ContainerCount** . Количество контейнеров BLOB-объектов в службе BLOB-объектов учетной записи хранения.  
- **ObjectCount** . Количество зафиксированных и незафиксированных блочных или страничных BLOB-объектов в службе BLOB-объектов учетной записи хранения.  

  Дополнительную информацию о метриках емкости см. в разделе [Схема таблицы для метрик аналитики хранилища](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

## <a name="how-metrics-are-stored"></a>Как хранятся метрики  

 Все данные метрик для каждой из служб хранения хранятся в таблицах, зарезервированных для этой службы: одна таблица для данных о транзакциях, вторая для данных о минутных транзакциях и третья - для данных о производительности. Информация о транзакциях и минутных транзакциях включает в себя данные запросов и ответов. Информация о емкости содержит данные об использовании хранилища. Часовые метрики, минутные метрики и емкость для службы BLOB-объектов учетной записи хранения можно получить в таблицах, имена которых приведены в следующей таблице.  

|Уровень метрик|Имена таблиц|Поддержка в версиях|  
|-------------------|-----------------|----------------------------|  
|Часовые метрики, основное расположение|-   $MetricsTransactionsBlob<br />-   $MetricsTransactionsTable<br />-   $MetricsTransactionsQueue|Только версии, выпущенные до 15 августа 2013 года. Таблицы с этими именами по-прежнему поддерживаются, но рекомендуется перейти на использование таблиц, приведенных ниже.|  
|Часовые метрики, основное расположение|-   $MetricsHourPrimaryTransactionsBlob<br />-   $MetricsHourPrimaryTransactionsTable<br />-   $MetricsHourPrimaryTransactionsQueue<br />-   $MetricsHourPrimaryTransactionsFile|все версии Поддержка метрик файловых служб доступна только в версии от 5 апреля 2015 года и более поздних версиях.|  
|Минутные метрики, основное расположение|-   $MetricsMinutePrimaryTransactionsBlob<br />-   $MetricsMinutePrimaryTransactionsTable<br />-   $MetricsMinutePrimaryTransactionsQueue<br />-   $MetricsMinutePrimaryTransactionsFile|все версии Поддержка метрик файловых служб доступна только в версии от 5 апреля 2015 года и более поздних версиях.|  
|Часовые метрики, вторичное расположение|-   $MetricsHourSecondaryTransactionsBlob<br />-   $MetricsHourSecondaryTransactionsTable<br />-   $MetricsHourSecondaryTransactionsQueue|все версии Должна быть включена геоизбыточная репликация с доступом для чтения.|  
|Минутные метрики, вторичное расположение|-   $MetricsMinuteSecondaryTransactionsBlob<br />-   $MetricsMinuteSecondaryTransactionsTable<br />-   $MetricsMinuteSecondaryTransactionsQueue|все версии Должна быть включена геоизбыточная репликация с доступом для чтения.|  
|Емкость (только для службы BLOB-объектов)|$MetricsCapacityBlob|все версии|  

 Эти таблицы автоматически создаются при включении Аналитики Службы хранилища для конечной точки службы хранилища. Доступ к ним осуществляется через пространство имен учетной записи хранения, например: `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`. Таблицы метрик не отображаются операцией вывода списка, обращаться к ним необходимо непосредственно по имени таблицы.

## <a name="enable-metrics-by-using-the-azure-portal"></a>Включение метрик с помощью портала Azure
Выполните следующие действия, чтобы включить метрики на [портале Azure](https://portal.azure.com).

1. Войдите в свою учетную запись хранения.
1. В области меню выберите **Diagnostics settings (classic)** (Параметры диагностики (классическая модель)).
1. Убедитесь, что в разделе **Состояние** задано значение **Вкл** .
1. Выберите метрики для служб, за которыми вы хотите наблюдать.
1. Укажите политику хранения, чтобы задать период хранения метрик и данных журналов.
1. Щелкните **Сохранить** .

Сейчас [портал Azure](https://portal.azure.com) не позволяет настраивать минутные метрики в учетной записи хранения. Эти метрики необходимо включать с помощью PowerShell или программно.

## <a name="enable-storage-metrics-by-using-powershell"></a>Включение метрик хранилища с помощью PowerShell  
Чтобы настроить метрики хранилища для учетной записи хранения, можно использовать PowerShell на локальном компьютере, выполнив командлет Azure PowerShell  **Get-AzStorageServiceMetricsProperty** для получения текущих настроек. Чтобы изменить текущие настройки, используйте командлет **Set-AzStorageServiceMetricsProperty** .  

Командлеты, позволяющие управлять метриками хранилища, используют следующие параметры.  

* **ServiceType** . Возможные значения **Blob** , **Queue** , **Table** и **File** .
* **MetricsType** . Возможные значения: **Hour** и **Minute** .  
* **MetricsLevel** . Возможные значения:
   * **None** . Отключает мониторинг.
   * **Service** . Сбор метрик, таких как исходящие и входящие данные, доступность, задержка и процент успешных операций, для служб больших двоичных объектов, очередей, таблиц и файлов.
   * **ServiceAndApi** . Дополнительно к метрикам служб обеспечивается сбор одинаковых метрик для каждой операции с хранилищем в API службы хранилища Azure.

Например, приведенная ниже команда отключает минутные метрики для службы BLOB-объектов в учетной записи хранения с установкой пятидневного периода удержания. 

> [!NOTE]
> В этой команде предполагается, что вы вошли в подписку Azure с помощью команды `Connect-AzAccount`.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"

Set-AzStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5 -Context $storageAccount.Context
```  

* Замените значение заполнителя `<resource-group-name>` именем своей группы ресурсов.      
* Замените значение заполнителя `<storage-account-name>` именем вашей учетной записи хранения.



Следующая команда получает текущий уровень часовых метрик и длительность периода хранения в днях для службы BLOB-объектов в учетной записи хранения по умолчанию:  

```powershell
Get-AzStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob -Context $storagecontext.Context
```  

Дополнительные сведения о настройке командлетов Azure PowerShell для работы с подпиской Azure и о выборе учетной записи хранения по умолчанию см. в разделе [Установка и настройка Azure PowerShell](/powershell/azure/).  

## <a name="enable-storage-metrics-programmatically"></a>Программное включение метрик хранилища  
Помимо управления метриками хранилища с помощью портала Azure или командлетов Azure PowerShell, можно также использовать один из API службы хранилища Azure. Например, если используется язык .NET, то можно использовать клиентскую библиотеку службы хранилища Azure.  

Классы **CloudBlobClient** , **CloudQueueClient** , **CloudTableClient** и **CloudFileClient** содержат такие методы как **SetServiceProperties** и **SetServicePropertiesAsync** , в которых объект **ServiceProperties** используется как параметр. Объект **ServiceProperties** может использоваться для настройки метрик хранилища. Например, в следующем фрагменте кода C# показано, как изменить уровень метрик и настроить количество дней хранения для часовых метрик очереди.  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

Дополнительные сведения о настройке метрик хранилища с помощью языка .NET см. в статье [API-интерфейсы хранилища Azure для .NET](/dotnet/api/overview/azure/storage).  

Общие сведения о настройке метрик хранилища с использованием API REST см. в статье [Включение и настройка аналитики хранилища](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics).  

##  <a name="view-storage-metrics"></a>Просмотр метрик хранилища  
После настройки метрик аналитики хранилища для мониторинга учетной записи хранения функция аналитики хранилища записывает метрики в набор известных таблиц в учетной записи хранения. Вы можете настроить диаграммы для просмотра почасовых метрик на [портале Azure](https://portal.azure.com).

1. Войдите в свою учетную запись хранения на [портале Azure](https://portal.azure.com).
1. Щелкните **Метрики (классические)** в области меню для службы, метрики которой вы хотите просмотреть.
1. Выберите диаграмму, которую нужно настроить.
1. В области **Изменение диаграммы** щелкните **Диапазон времени** > **Тип диаграммы** и выберите метрики, которые необходимо отобразить на диаграмме.

В разделе **Мониторинг (классический** ) в области меню учетной записи хранения на портале Azure можно настроить [правила генерации оповещений](#metrics-alerts). Например, можно отправлять оповещения по электронной почте, чтобы получать уведомления о том, что конкретная метрика достигла определенного значения.

Чтобы скачивать метрики для длительного хранения или анализа в локальной среде, необходимо использовать соответствующий инструмент или написать код для чтения таблиц. Необходимо скачать минутные метрики для анализа. Эти таблицы не отображаются при выводе списка таблиц в учетной записи хранения. Однако к ним можно обращаться напрямую по имени. Многие инструменты обзора хранилищ поддерживают эти таблицы и позволяют просматривать их напрямую. Список доступных инструментов указан в разделе [Клиентские инструменты службы хранилища Azure](./storage-explorers.md).

|Метрики|Имена таблиц|Примечания| 
|-|-|-|  
|Часовые метрики|$MetricsHourPrimaryTransactionsBlob<br /><br /> $MetricsHourPrimaryTransactionsTable<br /><br /> $MetricsHourPrimaryTransactionsQueue<br /><br /> $MetricsHourPrimaryTransactionsFile|В версиях, выпущенных до 15 августа 2013 года, эти таблицы назывались так:<br /><br /> $MetricsTransactionsBlob<br /><br /> $MetricsTransactionsTable<br /><br /> $MetricsTransactionsQueue<br /><br /> Метрики для службы файлов доступны начиная с версии от 5 апреля 2015 года.|  
|Минутные метрики|$MetricsMinutePrimaryTransactionsBlob<br /><br /> $MetricsMinutePrimaryTransactionsTable<br /><br /> $MetricsMinutePrimaryTransactionsQueue<br /><br /> $MetricsMinutePrimaryTransactionsFile|Могут быть включены только с помощью PowerShell или программно.<br /><br /> Метрики для службы файлов доступны начиная с версии от 5 апреля 2015 года.|  
|Capacity|$MetricsCapacityBlob|Только для службы BLOB-объектов.|  

Подробные сведения о схемах для этих таблиц см. в разделе [Схема таблицы метрик аналитики хранилища](/rest/api/storageservices/storage-analytics-metrics-table-schema). В примере строк ниже показана только часть доступных столбцов, однако они иллюстрируют некоторые важные возможности сохранения метрик с помощью метрик хранилища.  

|PartitionKey|RowKey|Отметка времени|TotalRequests|TotalBillableRequests|TotalIngress|TotalEgress|Доступность|AverageE2ELatency|AverageServerLatency|PercentSuccess| 
|-|-|-|-|-|-|-|-|-|-|-|  
|20140522T1100|user;All|2014-05-22T11:01:16.7650250Z|7|7|4003|46801|100|104.4286|6.857143|100|  
|20140522T1100|user;QueryEntities|2014-05-22T11:01:16.7640250Z|5|5|2694|45951|100|143.8|7.8|100|  
|20140522T1100|user;QueryEntity|2014-05-22T11:01:16.7650250Z|1|1|538|633|100|3|3|100|  
|20140522T1100|user;UpdateEntity|2014-05-22T11:01:16.7650250Z|1|1|771|217|100|9|6|100|  

В этом примере данных минутной метрики для ключа раздела используется время с минутным разрешением. Ключ строки определяет тип информации, хранящейся в строке. Эта информация включает в себя тип доступа и тип запроса.  

-   Типом доступа является либо **user** , либо **system** , где **user** означает все запросы пользователей к службе хранилища, а **system** — запросы, выполненные с помощью Аналитики Службы хранилища.  
-   Типом запроса является либо **all** , в случае чего это строка сводки, либо он определяет конкретный API, например **QueryEntity** или **UpdateEntity** .  

В примере данных выше показаны все записи за одну минуту (начиная с 11:00). Таким образом, если сложить количество запросов **QueryEntities** , количество запросов **QueryEntity** и количество запросов **UpdateEntity** , в сумме получится семь. Эта сумма отображается в строке **user:All** . Аналогичным образом можно определить среднюю сквозную задержку 104,4286 в строке **user:All** , вычислив выражение ((143,8 * 5) + 3 + 9)/7.  

## <a name="metrics-alerts"></a>Оповещения метрик
Рассмотрите возможность настройки оповещений на [портале Azure](https://portal.azure.com), чтобы автоматически получать уведомления о всех важных изменениях в поведении служб хранилища. Если скачивание этих метрик в формате с разделителями происходит с помощью инструмента обозревателя хранилища, то для анализа данных можно использовать Microsoft Excel. Список доступных инструментов Обозревателя службы хранилища см. в разделе [Клиентские инструменты службы хранилища Azure](./storage-explorers.md). Можно настроить оповещения в области **Alert (classic)** (Оповещение (классическое)), доступной в разделе **Мониторинг (классический)** в области меню учетной записи хранения.

> [!IMPORTANT]
> Возможна задержка между событием службы хранилища и записью данных соответствующей часовой или минутной метрики. В случае минутных метрик данные за несколько минут могут записываться одновременно. Эта проблема может привести к объединению транзакций за предшествующие минуты с транзакциями за текущую минуту. В этом случае у службы оповещений могут быть данные не всех доступных метрик за заданный интервал оповещения, что может привести к неожиданному срабатыванию оповещений.
>

## <a name="access-metrics-data-programmatically"></a>Программный доступ к данным метрик  
В следующем списке показан пример кода C#, в котором реализован доступ к минутным метрикам для диапазона минут с отображением результатов в окне консоли. В этом примере кода используется клиентская библиотека службы хранилища Azure версии 4, включающая в себя класс **CloudAnalyticsClient** , упрощающий доступ к таблицам метрик в хранилище. 

> [!NOTE]
> Класс **CloudAnalyticsClient** не включен в клиентскую библиотеку хранилища BLOB-объектов Azure версии 12 для .NET. С **31 августа 2023** аналитика службы хранилища метрики, также называемые *классическими метриками* , будут прекращены. Дополнительные сведения см. в [официальном объявлении](https://azure.microsoft.com/updates/azure-storage-classic-metrics-will-be-retired-on-31-august-2023/). Если вы используете классические метрики, рекомендуется перейти к метрикам в Azure Monitor до этой даты. 

```csharp
private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)  
{  
 // Convert the dates to the format used in the PartitionKey.  
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

     // Filter on "user" transactions after fetching the metrics from Azure Table storage.  
     // (StartsWith is not supported using LINQ with Azure Table storage.)  
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

## <a name="billing-on-storage-metrics"></a>Выставление счетов за метрики хранилища
За запросы записи на создание сущностей таблиц для метрик взимается плата в соответствии со стандартными тарифами, применимыми ко всем операциям службы хранилища Azure.  

К запросам на чтение и удаление данных метрик, отправляемым клиентом, также применяются стандартные тарифы. Если вы настроили политику хранения данных, то за удаление старых данных метрик службой хранилища Azure плата не взимается. Однако при удалении данных аналитики с учетной записи будет взиматься плата за операции удаления.  

Также оплачивается емкость, используемая таблицами метрик. Используйте приведенные ниже сведения, чтобы оценить емкость для хранения данных метрик.  

-   Если каждый час служба использует каждый API в каждой службе, то ежечасно в таблицах транзакций метрик сохраняется примерно 148 КБ данных, если включена сводка уровня службы и уровня API.  
-   Если каждый час служба использует каждый API в каждой службе, то ежечасно в таблицах транзакций метрик сохраняется примерно 12 КБ данных, если включена только сводка уровня службы.  
-   В таблицу емкости для больших двоичных объектов каждый день добавляются две строки, если вы предварительно дали согласие на это для журналов. В этом сценарии подразумевается, что каждый день размер данной таблицы увеличивается примерно на 300 байт.

## <a name="next-steps"></a>Дальнейшие действия
* [Мониторинг учетной записи хранения](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [Схема таблицы метрик аналитики хранилища](/rest/api/storageservices/storage-analytics-metrics-table-schema)   
* [Операции с протоколированием и сообщения о состоянии аналитик хранилища](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)   
* [Ведение журнала Аналитики Службы хранилища](storage-analytics-logging.md)