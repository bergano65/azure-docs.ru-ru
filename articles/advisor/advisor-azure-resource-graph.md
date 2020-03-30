---
title: Данные советников в графике ресурсов Azure
description: Делайте запросы для данных советников в графике ресурсов Azure
ms.topic: article
ms.date: 03/12/2020
ms.author: sagupt
ms.openlocfilehash: f8ad8fd450bc004d9caa2699922717f38d38b482
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502455"
---
# <a name="query-for-advisor-data-in-resource-graph-explorer-azure-resource-graph"></a>Запрос данных советника в Исследователе графиков ресурсов (Azure Resource Graph)

Ресурсы советника теперь приведены в комплекте для [графика ресурсов Azure.](https://azure.microsoft.com/features/resource-graph/) Это закладывает основу для многих сценариев клиентов в масштабе для рекомендаций консультантов. Немногие сценарии, которые раньше не было возможности сделать в масштабе и теперь могут быть достигнуты с помощью Resource Graph являются:
* Предоставляет возможность выполнения сложного запроса для всех ваших подписок на портале Azure
* Рекомендации, обобщенные по типам категорий (например, высокая доступность, производительность) и типы воздействия (высокие, средние, низкие)
* Все рекомендации по конкретному типу рекомендаций
* Количество ресурсов по рекомендациям

![Советник в исследователе графиков ресурсов Azure](./media/azure-resource-graph-1.png)  


## <a name="advisor-resource-types-in-azure-graph"></a>Типы ресурсов советников в Графике Azure

Доступные типы ресурсов советника в [графике ресурсов](https://docs.microsoft.com/azure/governance/resource-graph/): Для запроса под ресурсами Советника доступно 3 типа ресурсов. Вот список ресурсов, которые теперь доступны для запроса в графике ресурсов.
* Microsoft.Advisor/конфигурации
* Microsoft.Advisor/рекомендации
* Microsoft.Advisor/подавления

Эти типы ресурсов перечислены в новой таблице, названной AdvisorResources, которую вы также можете задать в ресурсном graph Explorer в портале Azure.


## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о рекомендациях Помощника см. в следующих разделах.
* [Общие сведения об Azure Advisor](advisor-overview.md)
* [Начало работы с Помощником по Azure](advisor-get-started.md)
* [Рекомендации Azure Advisor по затратам](advisor-cost-recommendations.md)
* [Рекомендации Azure Advisor по производительности](advisor-performance-recommendations.md)
* [Рекомендации Azure Advisor по безопасности](advisor-security-recommendations.md)
* [Рекомендации советника по операционному совершенству](advisor-operational-excellence-recommendations.md)
* [Советник REST API](https://docs.microsoft.com/rest/api/advisor/)
