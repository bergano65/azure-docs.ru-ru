---
title: Центр безопасности Azure и служба Kubernetes Azure
description: Сведения об интеграции центра безопасности Azure с Azure Kubernetes Services
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: d5c74ac760b5746d37468d692c2a46d5aedbee72
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80436177"
---
# <a name="azure-kubernetes-services-integration-with-security-center"></a>Интеграция Azure Kubernetes Services с центром безопасности

Служба Azure Kubernetes Service (AKS) — это управляемая служба Майкрософт для разработки и развертывания контейнерных приложений и управления ими. 

Используйте AKS вместе с уровнем "Стандартный" центра безопасности Azure (см. [цены](security-center-pricing.md)), чтобы получить более глубокую видимость узлов AKS, облачного трафика и средств безопасности.

Центр безопасности предоставляет преимущества безопасности для кластеров AKS, используя данные, уже собранные главным узлом AKS. 

![Центр безопасности Azure и общий обзор службы Azure Kubernetes Service (AKS)](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

Вместе эти два средства формируют лучшее облачное, собственное предложение по обеспечению безопасности Kubernetes. 

## <a name="benefits-of-integration"></a>Преимущества интеграции

Совместное использование этих двух служб обеспечивает следующие функции:

* **Рекомендации по безопасности** . Центр безопасности определяет ресурсы AKS и распределяет их по категориям: от кластеров к отдельным виртуальным машинам. Затем можно просмотреть рекомендации по безопасности для каждого ресурса. Дополнительные сведения см. в разделе рекомендации по контейнерам в [справочном списке рекомендаций](recommendations-reference.md#recs-containers). 

* **Усиление защиты в среде** . Центр безопасности постоянно отслеживает конфигурацию кластеров Kubernetes и конфигураций DOCKER. Затем он создает рекомендации по безопасности, отражающие отраслевые стандарты.

* **Защита во время выполнения** . Благодаря непрерывному анализу следующих источников AKS центр безопасности предупреждает об угрозах и вредоносных действиях, обнаруженных на уровне узла *и* кластера AKS.
    * Необработанные события безопасности, такие как данные сети и создание процессов;
    * Журнал аудита Kubernetes

    Дополнительные сведения см. в статье [Защита от угроз для контейнеров Azure](threat-protection.md#azure-containers) .

    Список возможных оповещений см. в следующих разделах справочника по оповещениям: [AKS на уровне кластера](alerts-reference.md#alerts-akscluster) и [предупреждения на уровне узла контейнера](alerts-reference.md#alerts-containerhost).  

![Центр безопасности Azure и служба Azure Kubernetes (AKS) более подробно](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)

> [!NOTE]
> Некоторые данные, проверенные центром безопасности Azure из среды Kubernetes, могут содержать конфиденциальные сведения.


## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные сведения о функциях безопасности контейнеров центра безопасности см. в следующих статьях:

* [Центр безопасности Azure и безопасность контейнеров](container-security.md)

* [Интеграция с Реестром контейнеров Azure](azure-container-registry-integration.md)

* [Управление данными в корпорации Майкрософт](https://www.microsoft.com/trust-center/privacy/data-management) . описывает политики данных служб Майкрософт (включая Azure, Intune и Office 365), сведения об управлении данными Майкрософт и политики хранения, влияющие на данные.
