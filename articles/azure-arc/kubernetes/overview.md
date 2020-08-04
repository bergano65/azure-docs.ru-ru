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
ms.openlocfilehash: 4f07c0e5e01648984514701cd4838f85478d86af
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87050058"
---
# <a name="what-is-azure-arc-enabled-kubernetes-preview"></a>Общие сведения о предварительной версии Kubernetes с поддержкой Azure Arc

Кластеры Kubernetes в среде Azure и за ее пределами можно подключать и настраивать с помощью предварительной версии Kubernetes с поддержкой Azure Arc. Когда кластер Kubernetes подключен к Azure Arc, он отображается на портале Azure, а также получает идентификатор Azure Resource Manager и управляемое удостоверение. Кластеры подключаются к стандартным подпискам Azure, находятся в группах ресурсов, и им можно назначать теги, как и любым другим ресурсам Azure. 

Чтобы подключить кластер Kubernetes к Azure, администратор кластера должен развернуть агенты. Эти агенты выполняются в пространстве имен Kubernetes с именем `azure-arc` и являются стандартными развертываниями Kubernetes. Агенты отвечают за подключение к Azure, сбор журналов и метрик Azure Arc, а также за отслеживание запросов конфигурации. 

Платформа Kubernetes с поддержкой Azure Arc совместима со стандартным протоколом SSL, защищающим данные при передаче. Кроме того, для обеспечения конфиденциальности информация в базах данных Azure Cosmos DB хранится в зашифрованном виде.
 
> [!NOTE]
> Kubernetes с поддержкой Azure Arc находится на этапе предварительной версии. Мы не рекомендуем использовать ее при нагрузках, обычных для рабочих средах.

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
