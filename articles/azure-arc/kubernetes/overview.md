---
title: Общие сведения о Kubernetes с поддержкой Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 02/15/2021
ms.topic: overview
author: mlearned
ms.author: mlearned
description: В этой статье представлен обзор Kubernetes с поддержкой Azure Arc.
keywords: Kubernetes, Arc, Azure, контейнеры
ms.custom: references_regions
ms.openlocfilehash: 54b439d73ed5dfb0709d33dea1f588b8bdf4489f
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100560263"
---
# <a name="what-is-azure-arc-enabled-kubernetes-preview"></a>Что собой представляет Kubernetes с поддержкой Azure Arc (предварительная версия)?

Кластеры Kubernetes в среде Azure и за ее пределами можно подключать и настраивать с помощью предварительной версии Kubernetes с поддержкой Azure Arc. Когда кластер Kubernetes подключен к Azure Arc, он отображается на портале Azure, а также получает идентификатор Azure Resource Manager и управляемое удостоверение. Кластеры подключаются к стандартным подпискам Azure, находятся в группах ресурсов, и им можно назначать теги, как и любым другим ресурсам Azure. 

Чтобы подключить кластер Kubernetes к Azure, администратор кластера должен развернуть агенты. Эти агенты выполняются в пространстве имен Kubernetes с именем `azure-arc` и являются стандартными развертываниями Kubernetes. Агенты отвечают за подключение к Azure, сбор журналов и метрик Azure Arc, а также за отслеживание запросов конфигурации. 

Kubernetes с поддержкой Azure Arc совместим со стандартным протоколом SSL, защищающим данные при передаче. Кроме того, для обеспечения конфиденциальности информация в базах данных Azure Cosmos DB хранится в зашифрованном виде.
 
> [!NOTE]
> Kubernetes с поддержкой Azure Arc находится на этапе предварительной версии. Мы не рекомендуем использовать ее при нагрузках, обычных для рабочих средах.

## <a name="supported-kubernetes-distributions"></a>Поддерживаемые дистрибутивы Kubernetes

Kubernetes с поддержкой Azure Arc работает с любым кластером Kubernetes, сертифицированном Cloud Native Computing Foundation (CNCF), например на обработчике AKS в Azure, обработчике AKS в Azure Stack Hub, GKE, EKS и кластере VMware vSphere.

Функции Kubernetes с поддержкой Azure Arc были протестированы командой Arc для следующих дистрибутивов:
* RedHat OpenShift 4.3;
* Rancher RKE 1.0.8;
* Canonical Charmed Kubernetes 1.18;
* Обработчик AKS
* Обработчик AKS в Azure Stack Hub
* AKS в Azure Stack HCI
* Cluster API Provider Azure.

## <a name="supported-scenarios"></a>Поддерживаемые сценарии 

Kubernetes с поддержкой Azure Arc используется в следующих сценариях: 

* Подключение кластера Kubernetes, работающего за пределами среды Azure, для учета ресурсов, их группировки и присвоения тегов.

* Развертывание приложений и применение конфигураций с помощью средств управления конфигурацией на основе GitOps. 

* Использование Azure Monitor для контейнеров с целью просмотра и мониторинга кластеров. 

* Применение политик с помощью Политики Azure для Kubernetes. 

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>Поддерживаемые регионы 

Kubernetes с поддержкой Azure Arc в настоящее время доступен в следующих регионах: 

* Восточная часть США 
* Западная Европа

## <a name="next-steps"></a>Дальнейшие действия

* [Подключение кластера](./connect-cluster.md)
