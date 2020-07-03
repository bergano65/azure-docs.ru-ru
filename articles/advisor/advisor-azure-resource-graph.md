---
title: Данные Advisor в графе ресурсов Azure
description: Создание запросов для данных Advisor в графе ресурсов Azure
ms.topic: article
ms.date: 03/12/2020
ms.author: sagupt
ms.openlocfilehash: f8ad8fd450bc004d9caa2699922717f38d38b482
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79502455"
---
# <a name="query-for-advisor-data-in-resource-graph-explorer-azure-resource-graph"></a>Запрос данных Advisor в обозревателе графа ресурсов (граф ресурсов Azure)

Ресурсы Advisor теперь подключены к [графу ресурсов Azure](https://azure.microsoft.com/features/resource-graph/). Это размещает основу для многих масштабируемых сценариев клиентов для рекомендаций Advisor. В некоторых сценариях, которые раньше не выполнялись в масштабе, может быть достигнуто использование графа ресурсов:
* Предоставляет возможность выполнять сложные запросы для всех подписок в портал Azure
* Рекомендации, итоговые по типам категорий (например, высокая доступность, производительность) и типы влияния (высокий, средний, низкий)
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
* [Начало работы с Помощником по Azure](advisor-get-started.md)
* [Рекомендации Azure Advisor по затратам](advisor-cost-recommendations.md)
* [Рекомендации Azure Advisor по производительности](advisor-performance-recommendations.md)
* [Рекомендации Azure Advisor по безопасности](advisor-security-recommendations.md)
* [Рекомендации для оперативной работы Advisor](advisor-operational-excellence-recommendations.md)
* [REST API Advisor](https://docs.microsoft.com/rest/api/advisor/)
