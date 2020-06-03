---
title: Общие сведения о Kubernetes с поддержкой Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: overview
author: mlearned
ms.author: mlearned
description: ''
keywords: Kubernetes, Arc, Azure, контейнеры
ms.openlocfilehash: 3b3b184ff7b18e05dfeb1052c0d6b4aa406a8787
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83665279"
---
# <a name="what-is-azure-arc-enabled-kubernetes-preview"></a>Общие сведения о Kubernetes с поддержкой Azure Arc (предварительная версия)

Вы можете подключать и настраивать кластеры Kubernetes в среде Azure и за ее пределами с помощью Kubernetes с поддержкой Azure Arc (предварительная версия). Когда кластер Kubernetes подключен к Azure Arc, он отображается на портале Azure, а также получает идентификатор Azure Resource Manager и управляемое удостоверение. Кластеры подключаются к стандартным подпискам Azure, находятся в группе ресурсов и могут получать теги, как и любые другие ресурсы Azure. 


Подключить кластер Kubernetes к Azure может администратор кластера для развертывания агентов. Эти агенты выполняются в пространстве имен Kubernetes с именем `azure-arc` и являются стандартными развертываниями Kubernetes. Агенты отвечают за подключение к Azure, сбор журналов и метрик Azure Arc, а также за отслеживание запросов конфигурации.  
 
 > [!NOTE]
> Kubernetes с поддержкой Azure Arc предоставляется в предварительной версии и не рекомендуется к использованию в рабочей среде. 


## <a name="supported-scenarios"></a>Поддерживаемые сценарии 

Kubernetes с поддержкой Azure Arc используется в следующих сценариях: 

* Подключение кластера Kubernetes, работающего за пределами среды Azure, для инвентаризации, группировки и присвоения тегов. 

* Развертывание приложений и применение конфигурации с помощью управления конфигурацией на основе GitOps. 

* Использование Azure Monitor для контейнеров для просмотра и мониторинга кластеров. 

* Применение политик с помощью Политики Azure для Kubernetes. 

 
## <a name="supported-regions"></a>Поддерживаемые регионы 

Kubernetes с поддержкой Azure Arc сейчас предоставляется в следующих регионах: 

* Восточная часть США 
* Западная Европа 


## <a name="next-steps"></a>Дальнейшие действия

* [Подключение кластера](./connect-cluster.md)
