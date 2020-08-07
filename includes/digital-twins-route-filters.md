---
author: baanders
description: включить файл для параметров фильтра маршрутов Azure Digital двойников
ms.service: digital-twins
ms.topic: include
ms.date: 8/3/2020
ms.author: baanders
ms.openlocfilehash: c5b78d80b1aa958af50b81dc152b4a746ce85f70
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87902214"
---
| Имя фильтра | Описание: | Фильтрация текста схемы | Поддерживаемые значения | 
| --- | --- | --- | --- |
| True/false | Позволяет создать маршрут без фильтрации или отключить маршрут, чтобы события не отправлялись | `<true/false>` | `true`= маршрут включен без фильтрации <br> `false`= маршрут отключен |
| Type | [Тип события](../articles/digital-twins/concepts-route-events.md#types-of-event-messages) , переданного через ваш экземпляр Digital двойника | `type = '<eventType>'` | Возможные значения типа события: <br>`Microsoft.DigitalTwins.Twin.Create` <br> `Microsoft.DigitalTwins.Twin.Delete` <br> `Microsoft.DigitalTwins.Twin.Update`<br>`Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br> `Microsoft.DigitalTwins.Relationship.Delete` <br> `microsoft.iot.telemetry`  |
| Источник | Имя экземпляра Digital двойников для Azure | `source = '<hostname>'`| Возможные значения имени узла: <br> **Для уведомлений**:`<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net` <br> **Для телеметрии**:`<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net/digitaltwins/<twinId>`|
| Тема | Описание события в контексте источника событий выше | `subject = '<subject>'` | Возможные значения субъекта: <br>**Для уведомлений**: Тема`<twinid>` <br> или формат URI для субъектов, которые уникально идентифицируются несколькими частями или идентификаторами:<br>`<twinid>/relationships/<relationshipid>`<br> **Для телеметрии**: субъект — это путь к компоненту (если данные телеметрии создаются из компонента двойника), например `comp1.comp2` . Если данные телеметрии не выдаются из компонента, то его поле subject будет пустым. |
| Схема данных | Идентификатор модели ДТДЛ | `dataschema = '<model-dtmi-ID>'` | **Для телеметрии**: Схема данных — это идентификатор модели двойника или компонента, который создает данные телеметрии. Например, `dtmi:example:com:floor4;2` <br>**Для уведомлений**: Схема данных не поддерживается|
| Тип содержимого | Тип содержимого значения данных | `datacontenttype = '<contentType>'` | Тип содержимого —`application/json` |
| Версия спецификации | Используемая версия схемы событий | `specversion = '<version>'` | Версия должна быть `1.0` . Это означает, что схема Клаудевентс версии 1,0 |

Можно также комбинировать фильтры, используя следующие операции:

| Имя фильтра | Фильтрация текста схемы | Пример | 
| --- | --- | --- |
| И (ИЛИ) | `<filter1> AND <filter2>` | `type != 'microsoft.iot.telemetry' AND datacontenttype = 'application/json'` |
| И (ИЛИ) | `<filter1> OR <filter2>` | `type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json'` |
| Вложенные операции | `(<Comparison1>) AND (<Comparison2>)` | `(type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json') OR (specversion != '1.0')` |

> [!NOTE]
> Во время предварительной версии поддерживается только равенство строк (=,! =).

При реализации или обновлении фильтра может потребоваться несколько минут для отражения в конвейере данных.
