---
title: Использование Зоны доступности в службе Kubernetes Azure (AKS)
description: Узнайте, как создать кластер, который распределяет узлы между зонами доступности в службе Kubernetes Azure (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 06/24/2019
ms.author: mlearned
ms.openlocfilehash: 3790511bf3f71cdeb01853e4051a013719502d9f
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73605090"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>Создание кластера Azure Kubernetes Service (AKS), использующего Зоны доступности

Кластер Azure Kubernetes Service (AKS) распространяет ресурсы, такие как узлы и хранилище, в логические разделы базовой инфраструктуры вычислений Azure. Эта модель развертывания гарантирует, что узлы выполняются между отдельными доменами обновления и сбоя в одном центре обработки данных Azure. Кластеры AKS, развернутые с этим поведением по умолчанию, обеспечивают высокий уровень доступности для защиты от сбоев оборудования или запланированного обслуживания.

Чтобы обеспечить более высокий уровень доступности приложений, кластеры AKS можно распределять между зонами доступности. Эти зоны являются физически отдельными центрами обработки данных в пределах заданного региона. Когда компоненты кластера распределяются по нескольким зонам, кластер AKS может допускать сбой в одной из этих зон. Ваши приложения и операции управления по-прежнему будут доступны, даже если в одном из центров обработки данных возникла проблема.

В этой статье показано, как создать кластер AKS и распределить компоненты узлов между зонами доступности.

## <a name="before-you-begin"></a>Перед началом работы

Требуется Azure CLI версии 2.0.76 или более поздней. Чтобы узнать версию, выполните команду  `az --version`. Если необходимо установить или обновить, см. раздел [install Azure CLI][install-azure-cli].

## <a name="limitations-and-region-availability"></a>Ограничения и доступность регионов

В настоящее время кластеры AKS можно создавать с помощью зон доступности в следующих регионах:

* Central US
* Восток США 2
* Восточная часть США
* Центральная Франция
* Восточная часть Японии
* Северная Европа
* Юго-Восточная Азия
* Южная часть Великобритании
* Западная Европа
* Западный регион США 2

При создании кластера AKS с помощью зон доступности применяются следующие ограничения.

* Зоны доступности можно включить только при создании кластера.
* Параметры зоны доступности невозможно обновить после создания кластера. Вы также не можете обновить существующий кластер зоны без доступности для использования зон доступности.
* Вы не сможете отключить зоны доступности для кластера AKS после его создания.
* Выбранный размер узла (SKU виртуальной машины) должен быть доступен во всех зонах доступности.
* Для кластеров с включенными зонами доступности необходимо использовать стандартные подсистемы балансировки нагрузки Azure для распределения между зонами.
* Для развертывания стандартных подсистем балансировки нагрузки необходимо использовать Kubernetes версии 1.13.5 или более поздней.

Кластеры AKS, использующие зоны доступности, должны использовать номер SKU " *стандартный* " балансировщика нагрузки Azure, который является значением по умолчанию для типа подсистемы балансировки нагрузки. Этот тип подсистемы балансировки нагрузки может быть определен только во время создания кластера. Дополнительные сведения и ограничения стандартной подсистемы балансировки нагрузки см. в статье [ограничения стандартного номера SKU для балансировщика нагрузки Azure][standard-lb-limitations].

### <a name="azure-disks-limitations"></a>Ограничения для дисков Azure

Тома, использующие управляемые диски Azure, сейчас не зональные ресурсы. Модули Pod, перезапланированные в другой зоне из исходной зоны, не могут повторно присоединить свои предыдущие диски. Рекомендуется запускать рабочие нагрузки без отслеживания состояния, которые не нуждаются в постоянном хранилище, которое может возникать в зональныеных проблемах.

Если необходимо запустить рабочие нагрузки с отслеживанием состояния, используйте таинтс и допуски в спецификациях Pod, чтобы сообщить планировщику Kubernetes о необходимости создания модулей Pod в той же зоне, что и диски. Кроме того, можно использовать сетевое хранилище, например файлы Azure, которые могут подключаться к модулям Pod, так как они планируются между зонами.

## <a name="overview-of-availability-zones-for-aks-clusters"></a>Общие сведения о Зоны доступности для кластеров AKS

Зоны доступности являются предложением, обеспечивающим высокий уровень доступности и защищающим приложения и данные от сбоев центров обработки данных. Зоны являются уникальными физическими расположениями в регионе Azure. Каждая зона состоит из одного или нескольких центров обработки данных, оснащенных независимыми системами электроснабжения, охлаждения и сетевого взаимодействия. Чтобы обеспечить отказоустойчивость, во всех включенных регионах используются минимум три отдельные зоны. Физическое разделение зон доступности в пределах региона защищает приложения и данные от сбоев центров обработки данных. Избыточные между зонами службы реплицируют приложения и данные в зонах доступности, чтобы обеспечить защиту от возникновения единых точек отказа.

Дополнительные сведения см. [в статье что такое зоны доступности в Azure?][az-overview].

Кластеры AKS, развернутые с помощью зон доступности, могут распределять узлы по нескольким зонам в одном регионе. Например, кластер в регионе " *Восточная часть США 2* " может создавать узлы во всех трех зонах доступности в *восточной части США 2*. Это распределение ресурсов кластера AKS повышает доступность кластера, так как они устойчивы к сбоям определенной зоны.

![Распределение узлов AKS между зонами доступности](media/availability-zones/aks-availability-zones.png)

В случае сбоя зоны узлы можно перераспределить вручную или с помощью автомасштабирования кластера. Если одна зона становится недоступной, приложения продолжают работать.

## <a name="create-an-aks-cluster-across-availability-zones"></a>Создание кластера AKS в разных зонах доступности

При создании кластера с помощью команды [AZ AKS Create][az-aks-create] параметр `--zones` определяет, в какие узлы агента зоны развертываются. Компоненты плоскости управления AKS для кластера также распределяются между зонами в самой высокой доступной конфигурации при определении параметра `--zones` во время создания кластера.

Если вы не определили зоны для пула агентов по умолчанию при создании кластера AKS, компоненты плоскости управления AKS для кластера не будут использовать зоны доступности. Добавить дополнительные пулы узлов можно с помощью команды [AZ AKS нодепул Add][az-aks-nodepool-add] и указать `--zones` для этих новых узлов, однако компоненты плоскости управления остаются без информации о зоне доступности. Невозможно изменить сведения о зоне для пула узлов или компонентов плоскости управления AKS после их развертывания.

В следующем примере создается кластер AKS с именем *myAKSCluster* в группе ресурсов с именем *myResourceGroup*. Создается всего *3* узла — один агент в зоне *1*, один — в *2*, а затем один — в *3*. Компоненты плоскости управления AKS также распределяются между зонами в максимальной доступной конфигурации, так как они определены как часть процесса создания кластера.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --generate-ssh-keys \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --node-count 3 \
    --zones 1 2 3
```

Создание кластера AKS занимает несколько минут.

## <a name="verify-node-distribution-across-zones"></a>Проверка распределения узлов между зонами

Когда кластер будет готов, перечислите узлы агента в масштабируемом наборе, чтобы увидеть, в какой зоне доступности они развернуты.

Сначала получите учетные данные кластера AKS с помощью команды [AZ AKS Get-Credential][az-aks-get-credentials] :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Затем используйте команду [kubectl Опишите][kubectl-describe] , чтобы вывести список узлов в кластере. Выполните фильтрацию по значению *Failure-domain.Beta.kubernetes.IO/Zone* , как показано в следующем примере:

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

В следующем примере выходных данных показаны три узла, распределенные по заданному региону и зонам доступности, например *eastus2-1* для первой зоны доступности и *eastus2-2* для второй зоны доступности:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

При добавлении дополнительных узлов в пул агентов Платформа Azure автоматически распределяет базовые виртуальные машины в указанных зонах доступности.

## <a name="next-steps"></a>Дополнительная информация

В этой статье подробно описано, как создать кластер AKS, использующий зоны доступности. Дополнительные сведения о кластерах высокой доступности см. [в статье рекомендации по обеспечению непрерывности бизнес-процессов и аварийному восстановлению в AKS][best-practices-bc-dr].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-overview]: ../availability-zones/az-overview.md
[best-practices-bc-dr]: operator-best-practices-multi-region.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[standard-lb-limitations]: load-balancer-standard.md#limitations
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-add
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials

<!-- LINKS - external -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
