---
title: Что такое Azure Application Gateway Ingress Controller?
description: Эта статья дает представление о том, что приложение шлюз Ingress контроллер.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: d6a63b6276c07b1fe6487b97f5c7fc255b6d3411
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335800"
---
# <a name="what-is-application-gateway-ingress-controller"></a>Что такое контроллер входа в шлюз приложений?
Application Gateway Ingress Controller (AGIC) — это приложение Kubernetes, которое позволяет клиентам [Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) использовать нативный нативный [шлюз Azure](https://azure.microsoft.com/services/application-gateway/) L7 для предоставления облачного программного обеспечения в Интернет. AGIC отслеживает кластер Kubernetes, на котором он размещается, и постоянно обновляет шлюз приложений, так что выбранные службы подвергаются воздействию Интернета.

Контроллер Ingress работает в своей капсуле на AKS клиента. AGIC отслеживает подмножество ресурсов Kubernetes для внесения изменений. Состояние кластера AKS переводится в конкретную конфигурацию Application Gateway и применяется к [менеджеру ресурсов Azure (ARM).](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)

## <a name="benefits-of-application-gateway-ingress-controller"></a>Преимущества контроллера входа в шлюз приложений
AGIC позволяет развертыванию управлять несколькими кластерами AKS с помощью одного контроллера входа в шлюз приложений. AGIC также помогает устранить необходимость иметь еще один балансик/общественный IP нагрузки перед кластером AKS и позволяет избежать нескольких переходов в вашем пути данных до того, как запросы достигнут кластера AKS. Приложение Gateway разговаривает с стили, используя их частный IP непосредственно и не требует услуг NodePort или KubeProxy. Это также обеспечивает более высокую производительность развертывания.

Ingress Controller поддерживается исключительно Standard_v2 и WAF_v2 SkUs, что также приносит вам преимущества автомасштабирования. Application Gateway может реагировать в ответ на увеличение или уменьшение нагрузки трафика и масштабирования соответственно, не потребляя никаких ресурсов из кластера AKS.

Использование шлюза приложений в дополнение к AGIC также помогает защитить ваш кластер AKS, предоставляя политику TLS и функциональность Web Application Firewall (WAF).

![Шлюз приложения Azure - AKS](./media/application-gateway-ingress-controller-overview/architecture.png)

AGIC настроен с помощью ресурса Kubernetes [Ingress,](https://kubernetes.io/docs/user-guide/ingress/)а также службы и развертывания/стручков. Он предоставляет ряд функций, используя нативный балансируя для загрузки Azure Gateway L7. Вот некоторые из них:
  - Маршрутизация URL-адресов
  - Сходство на основе файлов cookie,
  - Прекращение TLS
  - Сквозной TLS
  - Поддержка государственных, частных и гибридных веб-сайтов
  - Интегрированный брандмауэр веб-приложений

AGIC способен обрабатывать несколько именных пространств и имеет ProhibitedTargets, что означает, что AGIC может настроить шлюз приложения специально для кластеров AKS, не затрагивая другие существующие бэкэнды. 

## <a name="next-steps"></a>Next Steps

- [**Развертывание Greenfield**](ingress-controller-install-new.md): Инструкции по установке AGIC, AKS и Шлюза приложений на инфраструктуру с пустым листом.
- [**Развертывание Brownfield**](ingress-controller-install-existing.md): Установка AGIC на существующий AKS и шлюз приложений.

