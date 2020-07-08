---
title: Данные Помощника в Azure Resource Graph
description: Создание запросов для данных Advisor в графе ресурсов Azure
ms.topic: article
ms.date: 03/12/2020
ms.author: sagupt
ms.openlocfilehash: c0786d1d09ff61ddd9c375c68b7199521e319a4f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85117841"
---
# <a name="query-for-advisor-data-in-resource-graph-explorer-azure-resource-graph"></a>Запрос данных Advisor в обозревателе графа ресурсов (граф ресурсов Azure)

Ресурсы Advisor теперь подключены к [графу ресурсов Azure](https://azure.microsoft.com/features/resource-graph/). Это размещает основу для многих масштабируемых сценариев клиентов для рекомендаций Advisor. В некоторых сценариях, которые раньше не выполнялись в масштабе, может быть достигнуто использование графа ресурсов:
* Предоставляет возможность выполнять сложные запросы для всех подписок в портал Azure
* Рекомендации, итоговые по типам категорий (например, надежности, производительности) и типам влияния (высокий, средний, низкий)
* Все рекомендации для конкретного типа рекомендации
* Количество затронутых ресурсов по категориям рекомендаций

![Помощник в обозревателе графа ресурсов Azure](./media/azure-resource-graph-1.png)  


## <a name="advisor-resource-types-in-azure-graph"></a>Типы ресурсов Advisor в Azure Graph

Доступные типы ресурсов Advisor в [графе ресурсов](https://docs.microsoft.com/azure/governance/resource-graph/). для запросов к ресурсам Advisor доступно 3 типа ресурсов. Ниже приведен список ресурсов, которые теперь доступны для запросов в графе ресурсов.
* Microsoft. Advisor и конфигурации
* Microsoft. Advisor/рекомендации
* Microsoft. Advisor и подавления

Эти типы ресурсов перечислены под новой таблицей с именем Адвисорресаурцес, которую можно также запрашивать в обозревателе графа ресурсов в портал Azure.


## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные сведения о рекомендациях Помощника см. в следующих разделах.
* [Общие сведения об Azure Advisor](advisor-overview.md)
* [Начало работы с Помощником](advisor-get-started.md)
* [Рекомендации по стоимости помощника](advisor-cost-recommendations.md)
* [Рекомендации по обеспечению надежности Advisor](advisor-high-availability-recommendations.md)
* [Рекомендации по повышению производительности помощника](advisor-performance-recommendations.md)
* [Рекомендации по безопасности Advisor](advisor-security-recommendations.md)
* [Рекомендации для оперативной работы Advisor](advisor-operational-excellence-recommendations.md)
* [REST API Advisor](https://docs.microsoft.com/rest/api/advisor/)
