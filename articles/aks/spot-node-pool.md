---
title: Предварительный просмотр - Добавьте пул точечных узлов в кластер Azure Kubernetes Service (AKS)
description: Узнайте, как добавить пул точечных узлов в кластер Azure Kubernetes Service (AKS).
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 02/25/2020
ms.author: zarhoads
ms.openlocfilehash: 466ad7c88547b6676ba0ae263b74d14059322f1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77622046"
---
# <a name="preview---add-a-spot-node-pool-to-an-azure-kubernetes-service-aks-cluster"></a>Предварительный просмотр - Добавьте пул точечных узлов в кластер Azure Kubernetes Service (AKS)

Пул пятнистов узлов представляет собой пул узлов, поддерживаемый [набором спотовых виртуальных машин.][vmss-spot] Использование точечных VMs для узлов с кластером AKS позволяет использовать неиспользованные емкости в Azure при значительной экономии средств. Количество доступных неиспользованных мощностей будет варьироваться в зависимости от многих факторов, включая размер узла, область и время суток.

При развертывании пула точечных узлов Azure распределяет точечные узлы при наличии емкости. Но нет SLA для места узлов. Установка спотового шкалы, которая поддерживает пул точечных узлов, развертывается в одном домене неисправности и не дает гарантий высокой доступности. В любое время, когда Azure требуется обратное емкость, инфраструктура Azure будет выселять точечные узлы.

Точечные узлы отлично подходят для рабочих нагрузок, которые могут обрабатывать перерывы, ранние прекращения или выселения. Например, рабочие нагрузки, такие как задания обработки пакетов, среды разработки и тестирования, а также большие вычислительные рабочие нагрузки могут быть хорошими кандидатами, которые будут запланированы в пуле топового узла.

В этой статье вы добавляете пул вторичных точечных узлов в существующий кластер Azure Kubernetes Service (AKS).

Эта статья предполагает базовое понимание концепций Балансировора загрузки Kubernetes и Azure. Дополнительные сведения см. в статье [Ключевые концепции Kubernetes для службы Azure Kubernetes (AKS)][kubernetes-concepts].

Эта функция в настоящее время находится на стадии предварительной версии.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="before-you-begin"></a>Перед началом

При создании кластера для использования пула точечных узлов этот кластер должен также использовать наборы виртуальной шкалы машин для пулов узлов и балансируера нагрузки *Standard* SKU. Необходимо также добавить дополнительный пул узлов после создания кластера для использования пула топового узла. Добавление дополнительного пула узлов покрыто на более позднем этапе, но сначала необходимо включить функцию предварительного просмотра.

> [!IMPORTANT]
> Функции предварительного просмотра AKS являются самообслуживанием, отказом в обслуживании. Они предоставляются для сбора отзывов и ошибок от нашего сообщества. В предварительном просмотре эти функции не предназначены для использования в целях производства. Функции в общедоступном предварительном просмотре подпадают под поддержку «лучших усилий». Помощь от групп технической поддержки AKS доступна только в рабочее время в Тихоокеанском часовом поясе (PST). Для получения дополнительной информации, пожалуйста, смотрите следующие статьи поддержки:
>
> * [Политики поддержки AKS][aks-support-policies]
> * [Часто задаваемые вопросы о поддержке Azure][aks-faq]

### <a name="register-spotpoolpreview-preview-feature"></a>Функция предварительного просмотра Spotpoolpreview

Для создания кластера AKS, используюго пул точечных узлов, необходимо включить флаг функции *spotpoolpreview* в подписке. Эта функция предоставляет новейший набор улучшений службприна при настройке кластера.

> [!CAUTION]
> При регистрации функции по подписке вы не можете в настоящее время отменить регистрацию этой функции. После включения некоторых функций предварительного просмотра по умолчанию могут использоваться для всех кластеров AKS, а затем созданных в подписке. Не включайте функции предварительного просмотра в производственных подписках. Используйте отдельную подписку для тестирования функций предварительного просмотра и сбора отзывов.

Зарегистрируйте флаг функции *spotpoolpreview,* используя команду [регистра функций az,][az-feature-register] как показано в следующем примере:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "spotpoolpreview"
```

Через несколько минут отобразится состояние *Registered* (Зарегистрировано). Состояние регистрации можно проверить с помощью команды [az feature list][az-feature-list].

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/spotpoolpreview')].{Name:name,State:properties.state}"
```

Когда все будет готово, обновите регистрацию поставщика ресурсов *Microsoft.ContainerService* с помощью команды [az provider register][az-provider-register].

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="install-aks-preview-cli-extension"></a>Установка расширения интерфейса командной строки aks-preview

Для создания кластера AKS, используюго пул топового узла, требуется версия расширения *CLI aks-preview* 0.4.32 или выше. Установите расширение *aks-preview* Azure CLI с помощью команды [расширения az,][az-extension-add] а затем проверьте наличие доступных обновлений с помощью команды [обновления расширения аз:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview
 
# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Ограничения

При создании кластеров AKS с пулом точечных узлов применяются следующие ограничения:

* Пул пятен не может быть пулом узлов узла кластера по умолчанию. Бассейн точечного узла может использоваться только для вторичного бассейна.
* Вы не можете обновить пул пятнистов узлов, так как пулы узлов пятна не могут гарантировать кордон и утечку. Для проведения таких операций, как обновление версии Kubernetes, необходимо заменить существующий пул точечных узлов на новый. Чтобы заменить пул точечных узлов, создайте новый пул точечных узлов с другой версией Kubernetes, подождите, пока его статус *будет готов,* а затем удалите старый пул узлов.
* Пулы управления и узлов не могут быть модернизированы одновременно. Вы должны обновить их отдельно или удалить пул точечного узла для обновления плоскости управления и оставшихся пулов узлов в то же время.
* Пул пятен узлов должен использовать наборы виртуальной шкалы машин.
* Вы не можете изменить ScaleSetPriority или SpotMaxPrice после создания.
* При настройке SpotMaxPrice значение должно быть -1 или положительное значение до пяти десятичных мест.
* Пул пятен узла будет иметь ярлык *kubernetes.azure.com/scalesetpriority:spot,* запятнать *kubernetes.azure.com/scalesetpriority=spot:NoSchedule,* и стручки системы будут иметь anti-affinity.
* Необходимо добавить [соответствующую терпимость][spot-toleration] к расписанию рабочих нагрузок в пуле топового узла.

## <a name="add-a-spot-node-pool-to-an-aks-cluster"></a>Добавление пула топового узла в кластер AKS

Необходимо добавить пул пятен в существующий кластер с включенным несколькими пулами узлов. Более подробная информация о создании кластера AKS с несколькими пулами узлов доступна [здесь.][use-multiple-node-pools]

Создайте пул узлов, используя [az aks nodepool.][az-aks-nodepool-add]
```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name spotnodepool \
    --priority Spot \
    --eviction-policy Delete \
    --spot-max-price -1 \
    --enable-cluster-autoscaler \
    --min-count 1 \
    --max-count 3 \
    --no-wait
```

По умолчанию при создании кластера с несколькими пулами узлов создается пул узлов с *приоритетом* Regular of *Regular* в кластере AKS. Вышеупомянутая команда добавляет вспомогательный пул узлов к существующему кластеру AKS с *приоритетом* *Spot.* *Приоритет* *Spot* делает пул узлов пятничным узлам. Параметр *политики выселения* установлен для *удаления* в приведенном выше примере, который является значением по умолчанию. При [установке политики выселения][eviction-policy] для *удаления*узлы в базовом наборе пула узлов удаляются при их выселении. Вы также можете установить политику выселения для *Deallocate.* При установке политики выселения на *Deallocate*узлы в базовом наборе масштабов устанавливаются в состояние остановленного соглашения после выселения. Узлы в остановленном состоянии отсчитываются по вашей вычислительной квоте и могут вызвать проблемы с масштабированием или обновлением кластеров. Значения *приоритета* и *политики выселения* могут быть установлены только при создании пула узлов. Эти значения не могут быть обновлены позже.

Команда также позволяет [кластера autoscaler][cluster-autoscaler], который рекомендуется использовать с пулами точечных узлов. В зависимости от рабочих нагрузок, работающих в кластере, автоскейлист кластера масштабируется и сокращает количество узлов в пуле узлов. Для пулов точечных узлов кластерный автоскейлбудет увеличит количество узлов после выселения, если по-прежнему необходимы дополнительные узлы. При изменении максимального количества узлов пула узлов необходимо также настроить `maxCount` значение, связанное с автоскалатором кластера. Если вы не используете кластерный автоскалатор, после выселения пул точек в конечном итоге снизится до нуля и потребует ручной работы для получения дополнительных точечных узлов.

> [!Important]
> Только расписание рабочих нагрузок на пулах топовных узлов, которые могут обрабатывать перерывы, такие как задания обработки пакетов и среды тестирования. Рекомендуется настроить [пятна и переносы][taints-tolerations] в пуле узлов пятна, чтобы убедиться, что только рабочие нагрузки, которые могут обрабатывать выселения узлов, запланированы на пуле точечных узлов. Например, вышеупомянутая команда ny default добавляет запятнать *kubernetes.azure.com/scalesetpriority=spot:NoSchedule* поэтому на этом уде запланированы только стручки с соответствующей терпимостью.

## <a name="verify-the-spot-node-pool"></a>Проверить пул точечных узлов

Для проверки пула узлов был добавлен в виде пула топового узла:

```azurecli
az aks nodepool show --resource-group myResourceGroup --cluster-name myAKSCluster --name spotnodepool
```

Подтвердите, *что масштабSetPriority* является *Spot*.

Чтобы запланировать стручок для запуска на месте узла, добавить терпимость, которая соответствует запятнать применяется к пятнистому узлам. В следующем примере показана часть файла yaml, которая определяет толерантность, соответствующую *kubernetes.azure.com/scalesetpriority=spot:NoSchedule* загрязнения, используемого на предыдущем этапе.

```yaml
spec:
  containers:
  - name: spot-example
  tolerations:
  - key: "kubernetes.azure.com/scalesetpriority"
    operator: "Equal"
    value: "spot"
    effect: "NoSchedule"
   ...
```

Когда стручок с этой терпимости развернут, Kubernetes может успешно запланировать стручок на узлах с прикладом.

## <a name="max-price-for-a-spot-pool"></a>Максимальная цена за спот-бассейн
[Цены на спот-инстанции переменны,][pricing-spot]основаны на регионе и SKU. Для получения дополнительной информации оценивайте [цены][pricing-linux] на Linux и [Windows.][pricing-windows]

При переменных ценах, у вас есть возможность установить максимальную цену, в долларах США (USD), используя до 5 десятичных мест. Например, значение *0.98765* будет максимальной ценой $0.98765 USD в час. Если вы установите максимальную цену до *-1,* экземпляр не будет выселен в зависимости от цены. Цена для экземпляра будет текущая цена для Spot или цена для стандартного экземпляра, в зависимости от того, что меньше, до тех пор, пока есть емкость и квота.

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы узнали, как добавить пул топового узла в кластер AKS. Для получения дополнительной информации о том, как управлять стручками в пулах узлов, [см.][operator-best-practices-advanced-scheduler]

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-deploy-create]: /cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-template-deploy]: ../azure-resource-manager/templates/deploy-cli.md#deployment-scope
[cluster-autoscaler]: cluster-autoscaler.md
[eviction-policy]: ../virtual-machine-scale-sets/use-spot.md#eviction-policy
[kubernetes-concepts]: concepts-clusters-workloads.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[pricing-linux]: https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/
[pricing-spot]: ../virtual-machine-scale-sets/use-spot.md#pricing
[pricing-windows]: https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/
[spot-toleration]: #verify-the-spot-node-pool
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[use-multiple-node-pools]: use-multiple-node-pools.md
[vmss-spot]: ../virtual-machine-scale-sets/use-spot.md