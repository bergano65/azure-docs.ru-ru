---
title: Часто задаваемые вопросы о службе Arc Azure Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 02/15/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Эта статья содержит список часто задаваемых вопросов, связанных с включенной службой Arc Kubernetes.
keywords: Kubernetes, Arc, Azure, контейнеры, конфигурация, Гитопс, вопросы и ответы
ms.openlocfilehash: 237b2629b833a63552b172636f46a1ac92e321c0
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561738"
---
# <a name="frequently-asked-questions---azure-arc-enabled-kubernetes"></a>Часто задаваемые вопросы об Azure Arc Enabled Kubernetes

В этой статье рассматриваются часто задаваемые вопросы о Kubernetes с поддержкой Arc Azure.

## <a name="what-is-the-difference-between-azure-arc-enabled-kubernetes-and-azure-kubernetes-service-aks"></a>В чем разница между Kubernetes с поддержкой Azure Arc и Службой Azure Kubernetes (AKS)?

AKS — это управляемое предложение Kubernetes в Azure. AKS упрощает развертывание управляемого кластера Kubernetes в Azure путем разгрузки значительной сложности и эксплуатационных расходов в Azure. Так как главными узлами Kubernetes управляет платформа Azure, вы администрируете и обслуживаете только узлы агентов.

Служба "Дуга Azure" с поддержкой Kubernetes позволяет расширить возможности управления Azure (например, Azure Monitor и политику Azure), подключив кластеры Kubernetes к Azure. Вы сохраняете базовый кластер Kubernetes.

## <a name="do-i-need-to-connect-my-aks-clusters-running-on-azure-to-azure-arc"></a>Нужно ли подключать кластеры AKS, работающие в Azure, к службе "Дуга" Azure?

Нет. Все функции Kubernetes, включенные в службу "Дуга Azure", включая Azure Monitor и политику Azure (привратник), доступны в AKS (собственный ресурс в Azure Resource Manager).
    
## <a name="should-i-connect-my-aks-hci-cluster-and-kubernetes-clusters-on-azure-stack-hub-and-azure-stack-edge-to-azure-arc"></a>Следует ли подключить кластер AKS-ХЦИ и кластеры Kubernetes в центре Azure Stack, а Azure Stack ребра к службе "Дуга Azure"?

Да, подключение кластера AKS-ХЦИ или кластеров Kubernetes на Azure Stack пограничном или Azure Stackном концентраторе к Azure Arc обеспечивает кластеры с представлением ресурсов в Azure Resource Manager. Это представление ресурсов расширяет такие возможности, как конфигурация кластера, Azure Monitor и политика Azure (привратник) к кластерам Kubernetes, к которым вы подключаетесь.

## <a name="how-to-address-expired-azure-arc-enabled-kubernetes-resources"></a>Как устранить срок действия ресурсов Kubernetes в службе "Дуга Azure" с истекшим периодом?

Сертификат управляемого удостоверения службы (MSI), связанный с включенной службой "Дуга Azure" Куберенетес, имеет период срока действия в 90 дней. После истечения срока действия этого сертификата считается, что ресурс рассматривается `Expired` и все функции, такие как конфигурация, мониторинг и политика, перестают работать в этом кластере. Выполните следующие действия, чтобы снова начать работу с Kubernetes кластером с помощью дуги Azure.

1. Удаление ресурса и агентов Kubernetes с поддержкой дуги Azure в кластере 

    ```console
    az connectedk8s delete -n <name> -g <resource-group>
    ```

1. Повторно создайте ресурс Kubernetes с поддержкой дуги Azure, развернув агенты в кластере снова.
    
    ```console
    az connectedk8s connect -n <name> -g <resource-group>
    ```

> [!NOTE]
> `az connectedk8s delete` также будет удалять конфигурации поверх кластера. После выполнения `az connectedk8s connect` Создайте конфигурации в кластере повторно, либо вручную, либо с помощью политики Azure.

## <a name="if-i-am-already-using-cicd-pipelines-can-i-still-use-azure-arc-enabled-kubernetes-and-configurations"></a>Если я уже использую конвейеры CI/CD, могу ли я по-прежнему использовать Kubernetes и конфигурации с включенной службой Arc?

Да, вы по-прежнему можете использовать конфигурации в кластере, принимающем развертывания с помощью конвейера CI/CD. По сравнению с традиционными конвейерами CI/CD, конфигурации имеют два дополнительных преимущества:

**Выверка смещений**

Конвейер CI/CD применяет изменения только один раз во время выполнения конвейера. Однако оператор Гитопс в кластере постоянно опрашивает репозиторий Git для выборки требуемого состояния ресурсов Kubernetes в кластере. Если оператор Гитопс находит требуемое состояние ресурсов, отличающееся от фактического состояния ресурсов в кластере, это смещение выверено.

**Применение Гитопс в масштабе**

Конвейеры CI/CD хорошо подходят для развертываний на основе событий в кластере Kubernetes, где событие может быть push-уведомлением в репозиторий Git. Однако для развертывания той же конфигурации во всех кластерах Kubernetes требуется, чтобы конвейер CI/CD настроился с учетными данными каждого из этих кластеров Kubernetes вручную. С другой стороны, если в службе "Дуга Azure" включен Kubernetes, так как Azure Resource Manager управляет конфигурациями, можно использовать политику Azure для автоматизации применения требуемой конфигурации во всех кластерах Kubernetes в рамках одной подписки или группы ресурсов. Эта возможность применима даже к ресурсам Kubernetes, включенным в службу Arc Azure, созданным после назначения политики.

Функция конфигурации используется для применения базовых конфигураций, таких как политики сети, привязки ролей и политики безопасности Pod во всей инвентаризации кластеров Kubernetes для соответствия требованиям и управлению.

## <a name="next-steps"></a>Дальнейшие действия

* [Подключение кластера к службе "Дуга Azure"](./connect-cluster.md)
* [Создание конфигураций в кластере Kubernetes с включенной Arc](./use-gitops-connected-cluster.md)
* [Использование политики Azure для применения конфигураций в масштабе](./use-azure-policy.md)
