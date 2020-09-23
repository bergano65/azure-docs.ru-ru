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
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: f1444ea31fe693f1c912a3ecb785bbb1e3aa6333
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90977282"
---
# <a name="azure-kubernetes-services-integration-with-security-center"></a>Интеграция Azure Kubernetes Services с центром безопасности

Служба Azure Kubernetes Service (AKS) — это управляемая служба Майкрософт для разработки и развертывания контейнерных приложений и управления ими. 

Включите **защитник Azure для Kubernetes** , чтобы получить более глубокую видимость узлов AKS, облачного трафика и средств управления безопасностью.

Вместе центр безопасности и AKS формируют лучшее облачное предложение по обеспечению безопасности Kubernetes.

## <a name="what-are-the-components-of-security-centers-kubernetes-protection"></a>Каковы компоненты защиты Kubernetes в центре безопасности?

Защита центра безопасности для Kubernetes обеспечивается сочетанием двух элементов:

- **Защита от угроз центра безопасности Azure для виртуальных машин** . с помощью того же агента log Analytics, который используется центром безопасности на других виртуальных машинах, центр безопасности может показывать проблемы безопасности, происходящие на узлах AKS. Агент также отслеживает аналитику, относящуюся к контейнеру.

- **Дополнительный защитник Azure для центра безопасности Azure для Kubernetes Plan** — план Kubernetes получает журналы и сведения из подсистемы Kubernetes с помощью службы AKS. Эти журналы уже доступны в Azure через службу AKS. При включении защитника Azure для Kubernetes вы предоставляете центру безопасности доступ к журналам. Поэтому центр безопасности предоставляет преимущества безопасности для кластеров AKS, используя данные, уже собранные главным узлом AKS. Некоторые данные, проверенные центром безопасности Azure из среды Kubernetes, могут содержать конфиденциальные сведения.


## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о функциях безопасности контейнеров центра безопасности см. в следующих статьях:

* [Центр безопасности Azure и безопасность контейнеров](container-security.md)

* [Интеграция с Реестром контейнеров Azure](azure-container-registry-integration.md)

* [Управление данными в корпорации Майкрософт](https://www.microsoft.com/trust-center/privacy/data-management) . описывает политики данных служб Майкрософт (включая Azure, Intune и Microsoft 365), сведения об управлении данными Майкрософт и политики хранения, влияющие на данные.
