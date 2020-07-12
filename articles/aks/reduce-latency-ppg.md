---
title: Использование групп размещения с учетом расположения для сокращения задержки кластеров службы Azure Kubernetes Service (AKS)
description: Узнайте, как использовать группы размещения с учетом расположения для уменьшения задержки для рабочих нагрузок кластера AKS.
services: container-service
manager: gwallace
ms.topic: article
ms.date: 06/22/2020
ms.openlocfilehash: 1bcdfb4bb3c910feeac0521308e1e7d733fbd959
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86244078"
---
# <a name="reduce-latency-with-proximity-placement-groups-preview"></a>Сокращение задержки с помощью групп размещения с учетом расположения (Предварительная версия)

> [!Note]
> Если вы используете группы размещения с AKS, совместное размещение применяется только к узлам агента. Улучшена скорость между узлом и соответствующим размещенным модулем Pod. Совместное размещение не влияет на размещение плоскости управления кластера.

При развертывании приложения в Azure распределение экземпляров виртуальной машины между регионами или зонами доступности создает задержку в сети, что может повлиять на общую производительность приложения. Группа размещения с учетом расположения — это логическая группировка, используемая для того, чтобы ресурсы вычислений Azure физически размещались близко друг к другу. Для некоторых приложений, таких как игры, инженерные моделирования и торговля с высокой частотой (ХФТ), требуется низкая задержка и задачи, которые быстро выполняются. Для сценариев высокопроизводительных вычислений (HPC) рекомендуется использовать [группы размещения](../virtual-machines/linux/co-location.md#proximity-placement-groups) для пулов узлов кластера.

## <a name="limitations"></a>Ограничения

* Группа размещения с близкой назначением охватывает одну зону доступности.
* Текущая поддержка кластеров AKS, использующих группы доступности виртуальных машин, отсутствует.
* Нельзя изменить существующие пулы узлов для использования группы размещения с учетом расположения.

> [!IMPORTANT]
> Функции предварительной версии AKS доступны на уровне самообслуживания. Предварительные версии предоставляются "как есть" и "как есть" и исключаются из соглашений об уровне обслуживания и ограниченной гарантии. Предварительные версии AKS частично охвачены службой поддержки клиентов. Таким образом, эти функции не предназначены для использования в рабочей среде. Дополнительные сведения доступны в следующих статьях поддержки.
>
> - [Политики поддержки AKS](support-policies.md)
> - [Часто задаваемые вопросы о поддержке Azure](faq.md)

## <a name="before-you-begin"></a>Перед началом

Нужно установить следующие ресурсы:

- Расширение AKS-Preview 0.4.53

### <a name="set-up-the-preview-feature-for-proximity-placement-groups"></a>Настройка предварительной версии функции для групп размещения с учетом расположения

> [!IMPORTANT]
> Если вы используете группы размещения с AKS, совместное размещение применяется только к узлам агента. Улучшена скорость между узлом и соответствующим размещенным модулем Pod. Совместное размещение не влияет на размещение плоскости управления кластера.

```azurecli-interactive
# register the preview feature
az feature register --namespace "Microsoft.ContainerService" --name "ProximityPlacementGroupPreview"
```

Регистрация может занять несколько минут. Используйте приведенную ниже команду, чтобы убедиться, что компонент зарегистрирован.

```azurecli-interactive
# Verify the feature is registered:
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/ProximityPlacementGroupPreview')].{Name:name,State:properties.state}"
```

Во время предварительной версии для использования групп размещения с близкой использованием требуется расширение CLI *AKS-Preview* . Используйте команду [AZ Extension Add][az-extension-add] , а затем проверьте наличие доступных обновлений с помощью команды [AZ Extension Update][az-extension-update] .

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```
## <a name="node-pools-and-proximity-placement-groups"></a>Пулы узлов и группы размещения близости

Первый ресурс, развертываемый с помощью группы размещения с учетом расположения, подключается к определенному центру обработки данных. Дополнительные ресурсы, развернутые с одной и той же группой размещения, находятся в одном центре обработки данных. После того как все ресурсы, использующие группу размещения с учетом расположения, были остановлены (освобождены) или удалены, они больше не будут присоединены.

* Многие пулы узлов можно связать с одной группой размещения близости.
* Пул узлов может быть связан только с одной группой размещения близости.

## <a name="create-a-new-aks-cluster-with-a-proximity-placement-group"></a>Создание нового кластера AKS с группой размещения с учетом расположения

В следующем примере используется команда [AZ Group Create][az-group-create] для создания группы ресурсов с именем *myResourceGroup* в регионе *centralus* . После этого кластер AKS с именем *myAKSCluster* создается с помощью команды [AZ AKS Create][az-aks-create] . 

Ускоренная сеть значительно улучшает производительность сети виртуальных машин. В идеале используйте группы размещения близости в сочетании с ускоренной сетью. По умолчанию AKS использует ускоренную сеть на [поддерживаемых экземплярах виртуальных машин](../virtual-network/create-vm-accelerated-networking-cli.md?toc=/azure/virtual-machines/linux/toc.json#limitations-and-constraints), включая большинство виртуальных машин Azure с двумя или более виртуальных ЦП.

Создайте новый кластер AKS с группой размещения с близкой назначением:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```
Выполните следующую команду и сохраните возвращенный идентификатор:

```azurecli-interactive
# Create proximity placement group
az ppg create -n myPPG -g myResourceGroup -l centralus -t standard
```

Команда создает выходные данные, которые включают значение *идентификатора* , необходимое для предстоящих команд CLI:

```output
{
  "availabilitySets": null,
  "colocationStatus": null,
  "id": "/subscriptions/yourSubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Compute/proximityPlacementGroups/myPPG",
  "location": "centralus",
  "name": "myPPG",
  "proximityPlacementGroupType": "Standard",
  "resourceGroup": "myResourceGroup",
  "tags": {},
  "type": "Microsoft.Compute/proximityPlacementGroups",
  "virtualMachineScaleSets": null,
  "virtualMachines": null
}
```

Используйте идентификатор ресурса группы размещения близости для значения *миппгресаурцеид* в следующей команде:

```azurecli-interactive
# Create an AKS cluster that uses a proximity placement group for the initial node pool
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --ppg myPPGResourceID
```

## <a name="add-a-proximity-placement-group-to-an-existing-cluster"></a>Добавление группы размещения с учетом расположения в существующий кластер

Группу размещения близости можно добавить в существующий кластер, создав новый пул узлов. После этого можно при необходимости перенести существующие рабочие нагрузки в новый пул узлов, а затем удалить исходный пул узлов.

Используйте ту же группу размещения, которая была создана ранее, и это обеспечит, чтобы узлы агента в обоих пулах узлов в кластере AKS физически размещались в одном центре обработки данных.

Используйте идентификатор ресурса из группы размещения с учетом расположения, созданной ранее, и добавьте новый пул узлов с помощью [`az aks nodepool add`][az-aks-nodepool-add] команды:

```azurecli-interactive
# Add a new node pool that uses a proximity placement group, use a --node-count = 1 for testing
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 1 \
    --ppg myPPGResourceID
```

## <a name="clean-up"></a>Очистка

Чтобы удалить кластер, [`az group delete`][az-group-delete] удалите группу ресурсов AKS с помощью команды:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о [группах размещения][proximity-placement-groups]с учетом расположения.

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[proximity-placement-groups]: ../virtual-machines/linux/co-location.md#proximity-placement-groups
[az-aks-create]: /cli/azure/aks#az-aks-create
[system-pool]: ./use-system-pools.md
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
