---
title: Общие сведения о Kubernetes с поддержкой Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: overview
author: mlearned
ms.author: mlearned
description: В статье представлен обзор Kubernetes с поддержкой Azure Arc.
keywords: Kubernetes, Arc, Azure, контейнеры
ms.custom: references_regions
ms.openlocfilehash: 3960a0f0b6866a0a5d3476e841d29c6e5a89a238
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84309940"
---
# <a name="what-is-azure-arc-enabled-kubernetes-preview"></a>Общие сведения о предварительной версии Kubernetes с поддержкой Azure Arc

Кластеры Kubernetes в среде Azure и за ее пределами можно подключать и настраивать с помощью предварительной версии Kubernetes с поддержкой Azure Arc. Когда кластер Kubernetes подключен к Azure Arc, он отображается на портале Azure, а также получает идентификатор Azure Resource Manager и управляемое удостоверение. Кластеры подключаются к стандартным подпискам Azure, находятся в группах ресурсов, и им можно назначать теги, как и любым другим ресурсам Azure. 

Чтобы подключить кластер Kubernetes к Azure, администратор кластера должен развернуть агенты. Эти агенты выполняются в пространстве имен Kubernetes с именем `azure-arc` и являются стандартными развертываниями Kubernetes. Агенты отвечают за подключение к Azure, сбор журналов и метрик Azure Arc, а также за отслеживание запросов конфигурации. 

Платформа Kubernetes с поддержкой Azure Arc совместима со стандартным протоколом SSL, защищающим данные при передаче. Кроме того, для обеспечения конфиденциальности информация в базах данных Azure Cosmos DB хранится в зашифрованном виде.
 
 > [!NOTE]
> Платформа Kubernetes с поддержкой Azure Arc доступна только в предварительной версии. Мы не рекомендуем использовать ее при нагрузках, обычных для рабочих средах. 


## <a name="supported-scenarios"></a>Поддерживаемые сценарии 

Kubernetes с поддержкой Azure Arc используется в следующих сценариях: 

* Подключение кластера Kubernetes, работающего за пределами среды Azure, для учета ресурсов, их группировки и присвоения тегов.

* Развертывание приложений и применение конфигураций с помощью средств управления конфигурацией на основе GitOps. 

* Использование Azure Monitor для контейнеров с целью просмотра и мониторинга кластеров. 

* Применение политик с помощью Политики Azure для Kubernetes. 

 
## <a name="supported-regions"></a>Поддерживаемые регионы 

Сейчас платформа Kubernetes с поддержкой Azure Arc доступна в следующих регионах: 

* Восточная часть США 
* Западная Европа 

## <a name="next-steps"></a>Дальнейшие действия

* [Подключение кластера](./connect-cluster.md)
