---
title: включить файл
description: включить файл
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 01/22/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 7ddc0234accd9f434aad850d2404e2f3001c4bae
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/23/2021
ms.locfileid: "98742695"
---
## <a name="examples"></a>Примеры

### <a name="filter-on-system-properties"></a>Фильтрация по свойствам системы
Для ссылки на системное свойство в фильтре используйте следующий формат: `sys.<system-property-name>` . 

```csharp
sys.Label LIKE '%bus%'`
sys.messageid = 'xxxx'
sys.correlationid like 'abc-%'
```

### <a name="filter-on-message-properties"></a>Фильтрация по свойствам сообщения
Ниже приведены примеры использования свойств сообщений в фильтре. Доступ к свойствам сообщений можно получить с помощью `user.property-name` или просто `property-name` .

```csharp
MessageProperty = 'A'
SuperHero like 'SuperMan%'
```

### <a name="filter-on-message-properties-with-special-characters"></a>Фильтрация по свойствам сообщения с помощью специальных символов
Если имя свойства сообщения содержит специальные символы, используйте двойные кавычки ( `"` ), чтобы заключить имя свойства. Например, если имя свойства — `"http://schemas.microsoft.com/xrm/2011/Claims/EntityLogicalName"` , используйте следующий синтаксис в фильтре. 

```csharp
"http://schemas.microsoft.com/xrm/2011/Claims/EntityLogicalName" = 'account'
```

### <a name="filter-on-message-properties-with-numeric-values"></a>Фильтрация по свойствам сообщения с помощью числовых значений
В следующих примерах показано, как можно использовать свойства с числовыми значениями в фильтрах. 

```csharp
MessageProperty = 1
MessageProperty > 1
MessageProperty > 2.08
MessageProperty = 1 AND MessageProperty2 = 3
MessageProperty = 1 OR MessageProperty2 = 3
```

### <a name="parameter-based-filters"></a>Фильтры на основе параметров
Ниже приведено несколько примеров использования фильтров на основе параметров. В этих примерах `DataTimeMp` свойство является свойством Message типа `DateTime` и `@dtParam` является параметром, передаваемым в фильтр в качестве `DateTime` объекта.

```csharp
DateTimeMp < @dtParam
DateTimeMp > @dtParam

(DateTimeMp2-DateTimeMp1) <= @timespan //@timespan is a parameter of type TimeSpan
DateTimeMp2-DateTimeMp1 <= @timespan
```

### <a name="using-in-and-not-in"></a>Использование IN и NOT IN

```csharp
StoreId IN('Store1', 'Store2', 'Store3')"

sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'

sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')
```

Пример для C# см. [в разделе Пример фильтров разделов на сайте GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Azure.Messaging.ServiceBus/BasicSendReceiveTutorialwithFilters).


### <a name="set-rule-action-for-a-sql-filter"></a>Задание действия правила для фильтра SQL

```csharp
// instantiate the ManagementClient
this.mgmtClient = new ManagementClient(connectionString);

// create the SQL filter
var sqlFilter = new SqlFilter("source = @stringParam");

// assign value for the parameter
sqlFilter.Parameters.Add("@stringParam", "orders");

// instantiate the Rule = Filter + Action
var filterActionRule = new RuleDescription
{
    Name = "filterActionRule",
    Filter = sqlFilter,
    Action = new SqlRuleAction("SET source='routedOrders'")
};

// create the rule on Service Bus
await this.mgmtClient.CreateRuleAsync(topicName, subscriptionName, filterActionRule);
```

### <a name="correlation-filter-using-correlationid"></a>Фильтр корреляции с использованием CorrelationID

```csharp
new CorrelationFilter("Contoso");
```

Он фильтрует сообщения с параметром, имеющим `CorrelationID` значение `Contoso` . 

### <a name="correlation-filter-using-system-and-user-properties"></a>Фильтр корреляции с использованием свойств системы и пользователя

```csharp
var filter = new CorrelationFilter();
filter.Label = "Important";
filter.ReplyTo = "johndoe@contoso.com";
filter.Properties["color"] = "Red";
```

Это эквивалентно следующему: `sys.ReplyTo = 'johndoe@contoso.com' AND sys.Label = 'Important' AND color = 'Red'`

