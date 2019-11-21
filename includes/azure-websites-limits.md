---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 08/09/2019
ms.author: jroth
ms.openlocfilehash: 707f18ade87e755243a9122ecb7f898a753b2a8e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224540"
---
| Ресурс | Бесплатные | Общая | "Базовый" | Стандарт | Премиум (версия 2) | Изолированный </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [Web, mobile, or API apps](https://azure.microsoft.com/services/app-service/) per [Azure App Service plan](../articles/app-service/overview-hosting-plans.md)<sup>1</sup> |10 |100 |Без ограничений<sup>2</sup> |Без ограничений<sup>2</sup> |Без ограничений<sup>2</sup> |Без ограничений<sup>2</sup>|
| [План обслуживания приложения](../articles/app-service/overview-hosting-plans.md) |10 для каждого региона |10 шт. на одну группу ресурсов |100 шт. на одну группу ресурсов |100 шт. на одну группу ресурсов |100 шт. на одну группу ресурсов |100 шт. на одну группу ресурсов|
| Тип вычислительной операции |Общая |Общая |Выделенный<sup>3</sup> |Выделенный<sup>3</sup> |Выделенный<sup>3</sup></p> |Выделенный<sup>3</sup>|
| [Scale out](../articles/app-service/manage-scale-up.md) (maximum instances) |1 общедоступный |1 общедоступный |3 выделенных<sup>3</sup> |10 выделенных<sup>3</sup> |20 выделенных<sup>3</sup>|100 выделенных<sup>4</sup>|
| Хранилище<sup>5</sup> |1 ГБ<sup>5</sup> |1 ГБ<sup>5</sup> |10 ГБ<sup>5</sup> |50 ГБ<sup>5</sup> |250 ГБ<sup>5</sup></p> |1 ТБ<sup>5</sup>|
| CPU time (5 minutes)<sup>6</sup> |3 минуты |3 минуты |Без ограничений, оплата по стандартным [тарифам](https://azure.microsoft.com/pricing/details/app-service/)</a> |Без ограничений, оплата по стандартным [тарифам](https://azure.microsoft.com/pricing/details/app-service/)</a> |Без ограничений, оплата по стандартным [тарифам](https://azure.microsoft.com/pricing/details/app-service/)</a> |Без ограничений, оплата по стандартным [тарифам](https://azure.microsoft.com/pricing/details/app-service/)</a>|
| Время ЦП (в день)<sup>6</sup> |60 минут |240 минут |Без ограничений, оплата по стандартным [тарифам](https://azure.microsoft.com/pricing/details/app-service/)</a> |Без ограничений, оплата по стандартным [тарифам](https://azure.microsoft.com/pricing/details/app-service/)</a> |Без ограничений, оплата по стандартным [тарифам](https://azure.microsoft.com/pricing/details/app-service/)</a> |Без ограничений, оплата по стандартным [тарифам](https://azure.microsoft.com/pricing/details/app-service/)</a> |
| Память (1 час) |1,024 MB per App Service plan |1,024 MB per app |Н/Д |Н/Д |Н/Д |Н/Д |
| Пропускная способность |165 MB |Без ограничений, применяются [тарифы на передачу данных](https://azure.microsoft.com/pricing/details/data-transfers/) |Без ограничений, применяются [тарифы на передачу данных](https://azure.microsoft.com/pricing/details/data-transfers/) |Без ограничений, применяются [тарифы на передачу данных](https://azure.microsoft.com/pricing/details/data-transfers/) |Без ограничений, применяются [тарифы на передачу данных](https://azure.microsoft.com/pricing/details/data-transfers/) |Без ограничений, применяются [тарифы на передачу данных](https://azure.microsoft.com/pricing/details/data-transfers/) |
| Архитектура приложения |32-битная |32-битная |32- или 64-битная |32- или 64-битная |32- или 64-битная |32- или 64-битная |
| Web sockets per instance<sup>7</sup> |5 |35 |350 |Неограниченная |Неограниченная |Неограниченная |
| IP connections | 600 | 600 | Depends on instance size<sup>8</sup> | Depends on instance size<sup>8</sup> | Depends on instance size<sup>8</sup> | 64 000 |
| Количество одновременных [подключений к отладчику](../articles/app-service/troubleshoot-dotnet-visual-studio.md) для каждого приложения |1 |1 |1 |5 |5 |5 |
| App Service Certificates per subscription<sup>9</sup>| Не поддерживается | Не поддерживается |10 |10 |10 |10 |
| Количество личных доменов на одно приложение</a> |0 (только поддомен azurewebsites.net)|500 |500 |500 |500 |500 |
| личных доменов [Поддержка SSL](../articles/app-service/configure-ssl-certificate.md) |Not supported, wildcard certificate for *.azurewebsites.net available by default|Not supported, wildcard certificate for *.azurewebsites.net available by default|Неограниченное количество подключений SSL на основе SNI |Включено неограниченное количество подключений SSL на основе SNI и 1 подключение SSL на основе IP |Включено неограниченное количество подключений SSL на основе SNI и 1 подключение SSL на основе IP | Включено неограниченное количество подключений SSL на основе SNI и 1 подключение SSL на основе IP|
| Hybrid connections per plan | | | 5 | 25 | 200 | 200 |
| Integrated load balancer | |X |X |X |X |X<sup>10</sup> |
| [Всегда включено](../articles/app-service/configure-common.md) | | |X |X |X |X |
| [Scheduled backups](../articles/app-service/manage-backup.md) | | | | Scheduled backups every 2 hours, a maximum of 12 backups per day (manual + scheduled) | Scheduled backups every hour, a maximum of 50 backups per day (manual + scheduled) | Scheduled backups every hour, a maximum of 50 backups per day (manual + scheduled) |
| [Автомасштабирование](../articles/app-service/manage-scale-up.md) | | | |X |X |X |
| [WebJobs](../articles/app-service/webjobs-create.md)<sup>11</sup> |X |X |X |X |X |X |
| [планировщика Azure](https://azure.microsoft.com/services/scheduler/) | |X |X |X |X |X |
| [Мониторинг конечных точек](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| [Staging slots](../articles/app-service/deploy-staging-slots.md) | | | |5 |20 |20 |
| Соглашение об уровне обслуживания | |  |99,95 %|99,95 %|99,95 %|99,95 %|  

<sup>1</sup>Если не указано иное, квоты приложений и хранилищ указаны в расчете на один план службы приложений.  
<sup>2</sup>Фактическое количество приложений, которые можно разместить на этих машинах, определяется интенсивностью работы приложений, размером экземпляров машин и степенью использования соответствующих ресурсов.  
<sup>3</sup>Выделенные экземпляры могут иметь различные размеры. Дополнительные сведения см. на странице [цен на службу приложений](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>4</sup>More are allowed upon request.  
<sup>5</sup>The storage limit is the total content size across all apps in the same App service plan. The total content size of all apps across all App service plans in a single resource group and region cannot exceed 500GB.  
<sup>6</sup>Эти ресурсы ограничены физическими ресурсами на выделенных экземплярах (размером экземпляра и количеством экземпляров).  
<sup>7</sup>При масштабировании приложения в два экземпляра на уровне Basic для каждого из двух экземпляров создается 350 параллельных подключений. For Standard tier and above, there are no theoretical limits to web sockets, but other factors can limit the number of web sockets. For example, maximum concurrent requests allowed (defined by `maxConcurrentRequestsPerCpu`) are: 7,500 per small VM, 15,000 per medium VM (7,500 x 2 cores), and 75,000 per large VM (18,750 x 4 cores).  
<sup>8</sup>The maximum IP connections are per instance and depend on the instance size: 1,920 per B1/S1/P1V2 instance, 3,968 per B2/S2/P2V2 instance, 8,064 per B3/S3/P3V2 instance.  
<sup>9</sup>The App Service Certificate quota limit per subscription can be increased via a support request to a maximum limit of 200.  
<sup>10</sup>App Service Isolated SKUs can be internally load balanced (ILB) with Azure Load Balancer, so there's no public connectivity from the internet. В результате некоторые функции изолированной службы приложений с ILB следует использовать на компьютерах с прямым доступом к конечной точке сети ILB.  
<sup>11</sup>Run custom executables and/or scripts on demand, on a schedule, or continuously as a background task within your App Service instance. Для непрерывного выполнения веб-заданий требуется постоянная доступность. Для запланированных веб-заданий требуется планировщик Azure бесплатной или стандартной версии. There's no predefined limit on the number of WebJobs that can run in an App Service instance. There are practical limits that depend on what the application code is trying to do.  