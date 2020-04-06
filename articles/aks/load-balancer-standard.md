---
title: Использование балансивизатора нагрузки Standard SKU
titleSuffix: Azure Kubernetes Service
description: Узнайте, как использовать балансомер нагрузки со стандартным SKU для предоставления услуг службы Azure Kubernetes Service (AKS).
services: container-service
author: zr-msft
ms.topic: article
ms.date: 09/27/2019
ms.author: zarhoads
ms.openlocfilehash: 17e474de9c221126d67cc2982ba11c6ff75e7aa3
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668492"
---
# <a name="use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Используйте балансиватор нагрузки Standard SKU в службе Azure Kubernetes (AKS)

Для обеспечения доступа к приложениям через `LoadBalancer` службы типа Kubernetes в службе Azure Kubernetes (AKS) можно использовать балансер загрузки Azure. Балансилер нагрузки, работающий на AKS, может использоваться в качестве внутреннего или внешнего сальдора нагрузки. Внутренний балансосиватор нагрузки делает сервис Kubernetes доступным только для приложений, работающих в той же виртуальной сети, что и кластер AKS. Внешний балансосиватор нагрузки получает один или несколько публичных ИС для входа и делает услугу Kubernetes доступной внешне с помощью публичных ИС.

Доступны два номера SKU Azure Load Balancer: ценовых категорий *Базовый* и *Стандартный*. По умолчанию *Стандартный* SKU используется при создании кластера AKS. Использование *балансиста* нагрузки Standard SKU обеспечивает дополнительные функции и функциональные возможности, такие как больший размер пула бэкэнда и зоны доступности. Важно, чтобы вы понимали различия между балансисаторами *стандартной* и *основной* нагрузки, прежде чем выбрать, какой из них использовать. После создания кластера AKS невозможно изменить балансосм-баланс омрачаемых нагрузками SKU для этого кластера. Для получения дополнительной информации о *базовых* и *стандартных* SkUS, [Azure load balancer SKU comparison][azure-lb-comparison]см.

Эта статья предполагает базовое понимание концепций Балансировора загрузки Kubernetes и Azure. Для получения дополнительной информации [см. основные концепции Kubernetes для службы Azure Kubernetes Service (AKS)][kubernetes-concepts] и [Что такое балансер загрузки Azure?.][azure-lb]

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать CLI локально, эта статья требует, чтобы вы запускали версию Azure CLI 2.0.81 или позже. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][install-azure-cli].

## <a name="before-you-begin"></a>Подготовка к работе

В этой статье предполагается, что у вас есть кластер AKS со *стандартным* балансом загрузки SKU Azure. Если вам нужен кластер AKS, обратитесь к этому краткому руководству по работе с AKS [с помощью Azure CLI][aks-quickstart-cli] или [портала Azure][aks-quickstart-portal].

Директору кластерного обслуживания AKS также требуется разрешение на управление сетевыми ресурсами, если вы используете существующую подсеть или группу ресурсов. Как правило, назначьте роль *вкладчика Сети* доверию службы на делегированных ресурсах. Для получения дополнительной информации [Delegate AKS access to other Azure resources][aks-sp]о разрешениях см.

### <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>Переход от базового балансировора нагрузки SKU к стандартному SKU

Если у вас есть существующий кластер с basic SKU Load Balancer, при миграции необходимо отметить важные поведенческие различия при использовании кластера со стандартным балансом загрузки SKU.

Например, создание синих/зеленых развертываний для мигрировать кластеров является обычной практикой, учитывая `load-balancer-sku` тип кластера может быть определен только во время создания кластера. Тем не менее, основные балансы загрузки *SKU* используют основные IP-адреса *SKU,* которые не совместимы со стандартными балансироворами загрузки *SKU,* поскольку они требуют стандартных IP-адресов *SKU.* При миграции кластеров для обновления СКУ По грузоподъемности потребуется новый IP-адрес с совместимым IP-адресом SKU.

Для получения дополнительных соображений о том, как мигрировать кластеры, посетите [нашу документацию по вопросам миграции,](aks-migration.md) чтобы просмотреть список важных тем, которые следует учитывать при миграции. Ниже ограничения также важны поведенческие различия, чтобы отметить при использовании стандартных балансов загрузки SKU в AKS.

### <a name="limitations"></a>Ограничения

При создании и управлении кластерами AKS, поддерживающими балансосмлер нагрузки *со Стандартным* SKU, применяются следующие ограничения:

* Для разрешения трафика из кластера AKS требуется по крайней мере одна публичная приставка IP или IP. Публичная приставка IP или IP также необходима для поддержания связи между плоскости управления и узлами агента, а также для поддержания совместимости с предыдущими версиями AKS. У вас есть следующие варианты указания общедоступных IP-адресов или IP-префиксов с балансивизатором нагрузки *Standard* SKU:
    * Предоставьте свои собственные публичные ИС.
    * Предоставьте свои собственные публичные ip-префиксы.
    * Укажите число до 100, чтобы позволить кластеру AKS создать так много общедоступных ИП *Standard* SKU в той же группе ресурсов, что и кластер AKS, который обычно называется с *MC_* в начале. AKS присваивает публичный IP балансеру нагрузки *Standard* SKU. По умолчанию один публичный IP автоматически создается в той же группе ресурсов, что и кластер AKS, если не указаны общедоступный IP-адрес, публичная приставка IP или количество IP-адресов. Вы также должны разрешить публичные адреса и избегать создания какой-либо политики Azure, запрещающего создание IP-адресов.
* При использовании *Standard* SKU для балансоилира нагрузки необходимо использовать версию Kubernetes *1.13 или больше.*
* Определение баланса нагрузки SKU может быть сделано только при создании кластера AKS. Вы не можете изменить балансоровую нагрузку SKU после создания кластера AKS.
* Можно использовать только один тип балансирутеля нагрузки SKU (Основной или стандартный) в одном кластере.
* *Стандартный* Балансирумы загрузки SKU поддерживают только *стандартные* IP-адреса SKU.

## <a name="use-the-standard-sku-load-balancer"></a>Используйте *балансизатор* нагрузки Standard SKU

При создании кластера AKS по умолчанию при запуске служб в этом кластере используется балансилер нагрузки *Standard* SKU. Например, [быстрый запуск с помощью Azure CLI][aks-quickstart-cli] развертывает пример приложения, использующего балансера нагрузки *Standard* SKU.

> [!IMPORTANT]
> Публичных IP-адресов можно избежать, настроив пользовательский маршрут (UDR). Определение исходящего типа кластера AKS как UDR может пропустить настройку IP-предложения и настройку пула бэкэнда для аксовсозданного балансисатора нагрузки Azure. [Просмечку `outboundType` настройки кластера на 'userDefinedRouting'.](egress-outboundtype.md)

## <a name="configure-the-load-balancer-to-be-internal"></a>Настройка балансомера нагрузки на внутренний

Можно также настроить балансомер нагрузки на внутренний и не разоблачать общедоступный IP-адрес. Чтобы настроить балансоусилитель нагрузки `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` как внутренний, добавьте в качестве аннотации к службе *LoadBalancer.* Вы можете увидеть пример yaml манифест, а также более подробную информацию о внутренней нагрузки балансера [здесь][internal-lb-yaml].

## <a name="scale-the-number-of-managed-public-ips"></a>Масштабирование числа управляемых общедоступных ИС

При использовании балансиста нагрузки *Standard* SKU с управляемыми исходящими общедоступными IP-адресами, которые создаются по умолчанию, можно масштабировать количество управляемых исходящих публичных IP-адресов с помощью параметра *нагрузки-баланс-управляемый-ip-счет.*

Чтобы обновить существующий кластер, запустите следующую команду. Этот параметр также может быть установлен в момент создания кластера, чтобы иметь несколько управляемых исходящих общедоступных ИП.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

В приведенном выше примере количество управляемых исходящих публичных МП до *2* для кластера *myAKSCluster* в *myResourceGroup.* 

Можно также использовать параметр, *управляемый нагрузкой, управляемым ип-счетом,* чтобы установить начальное число `--load-balancer-managed-outbound-ip-count` управляемых исходящих общедоступных IP-адресов при создании кластера путем добавления параметра и установки его на желаемое значение. По умолчанию число управляемых исходящих публичных ИП составляет 1.

## <a name="provide-your-own-public-ips-or-prefixes-for-egress"></a>Предоставьте свои собственные публичные ИП или префиксы для выход

При использовании балансиста нагрузки *Standard* SKU кластер AKS автоматически создает общедоступный IP в той же группе ресурсов, созданной для кластера AKS, и присваивает публичный IP балансеру нагрузки *Standard* SKU. Кроме того, можно назначить свой собственный общедоступный IP во время создания кластера или обновить свойства балансивателя нагрузки существующего кластера.

Принося несколько IP-адресов или префиксов, вы можете определить несколько служб поддержки при определении IP-адреса за одним объектом балансоровы нагрузок. Конечная точка входа конкретных узлов будет зависеть от того, с какой услугой они связаны.

> [!IMPORTANT]
> Вы должны использовать *стандартные* I-iPs SKU для вынашиваемого со *стандартным* SKU балансера нагрузки. Вы можете проверить SKU ваших публичных IP-адресов с помощью команды [шоу a network public-ip][az-network-public-ip-show] show:
>
> ```azurecli-interactive
> az network public-ip show --resource-group myResourceGroup --name myPublicIP --query sku.name -o tsv
> ```

Используйте команду [шоу общедоступного шоу сети az,][az-network-public-ip-show] чтобы перечислить иди ваших общедоступных IP-адресов.

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

Вышеупомянутая команда показывает идентификатор для публичного IP *myPublicIP* в группе ресурсов *myResourceGroup.*

Используйте команду *обновления az aks* с параметром *нагрузочно-балансового ипового входящих ипподвитах* для обновления кластера с помощью общедоступных IP-адресов.

В следующем примере используется параметр *исходящих ипов грузоподъемно-ips* с имитаторами предыдущей команды.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Вы также можете использовать общедоступные IP-префиксы для выхода с балансером *нагрузки Standard* SKU. В следующем примере используется команда [от отображитого в рекламе сети az-ip-показа][az-network-public-ip-prefix-show] для того, чтобы перечислить иди ваших общедоступных префиксов IP:

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

Вышеприведенная команда показывает идентификатор для публичной ip-приставки *myPublicIPPrefix* в группе ресурсов *myResourceGroup.*

В следующем примере используется параметр *исходящих исходящих ип-префиксов с* иданными ими из предыдущей команды.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

> [!IMPORTANT]
> Публичные IP-адреса и IP-префиксы должны находиться в том же регионе и быть частью той же подписки, что и ваш кластер AKS. 

### <a name="define-your-own-public-ip-or-prefixes-at-cluster-create-time"></a>Определите свой собственный публичный IP или префиксы во время создания кластера

Вы можете принести свои собственные IP-адреса или IP-префиксы для выхода на время создания кластера для поддержки сценариев, таких как белые списки конечных точек. Прикажите те же параметры, показанные выше, к этапу создания кластера, чтобы определить свои собственные общедоступные IP-адреса и ip-префиксы в начале жизненного цикла кластера.

Используйте команду *az aks create* с параметром *нагрузочно-балансового ипового входящих в* эксплуатацию ips для создания нового кластера с общедоступными IP-адресами в начале.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Используйте команду *az aks create* command с параметром *нагрузки-баланса-ип-префиксов* для создания нового кластера с общедоступными ip-префиксами в начале.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

## <a name="configure-outbound-ports-and-idle-timeout"></a>Настройка исходящих портов и простоя тайм-аута

> [!WARNING]
> Следующий раздел предназначен для продвинутых сценариев более масштабной сети или для решения проблем истощения SNAT с конфигурациями по умолчанию. Вы должны иметь точный перечень доступных квот для VMs и IP-адресов, прежде чем изменить *AllocatedOutboundPorts* или *IdleTimeoutInMinutes* от их значения по умолчанию для поддержания здоровых кластеров.
> 
> Изменение значений для *AllocatedOutboundPorts* и *IdleTimeoutInMinutes* может существенно изменить поведение исходящего правила для балансиваля нагрузки. Просмотрите [правила исходящего баланса нагрузки,][azure-lb-outbound-rules-overview] [загрузите правила исходящих балансов][azure-lb-outbound-rules]и [исходящие соединения в Azure,][azure-lb-outbound-connections] прежде чем обновлять эти значения, чтобы полностью понять влияние ваших изменений.

Исходящие выделенные порты и их холостого хода тайм-ауты используются для [SNAT.][azure-lb-outbound-connections] По умолчанию балансилер нагрузки *Standard* SKU использует [автоматическое назначение для числа исходящих портов на основе размера пула бэкэнда][azure-lb-outbound-preallocatedports] и 30-минутного тайм-аута для каждого порта. Чтобы увидеть эти значения, используйте [список исходящих правил сети az,][az-network-lb-outbound-rule-list] чтобы показать исходящие правила для балансиваля нагрузки:

```azurecli-interactive
NODE_RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az network lb outbound-rule list --resource-group $NODE_RG --lb-name kubernetes -o table
```

Предыдущие команды будут перечислять исходящие правила для баланса нагрузки, например:

```console
AllocatedOutboundPorts    EnableTcpReset    IdleTimeoutInMinutes    Name             Protocol    ProvisioningState    ResourceGroup
------------------------  ----------------  ----------------------  ---------------  ----------  -------------------  -------------
0                         True              30                      aksOutboundRule  All         Succeeded            MC_myResourceGroup_myAKSCluster_eastus  
```

На примере вывода отображается значение по умолчанию для *AllocatedOutboundPorts* и *IdleTimeoutInMinutes.* Значение 0 для *AllocatedOutboundPorts* устанавливает количество исходящих портов с помощью автоматического назначения для числа исходящих портов на основе размера пула бэкэнда. Например, если кластер имеет 50 или менее узлов, для каждого узла выделяется 1024 порта.

Рассмотрите возможность изменения настройки *выделенных OutboundPorts* или *IdleTimeoutInMinutes,* если вы ожидаете столкнуться с истощением SNAT на основе вышеуказанной конфигурации по умолчанию. Каждый дополнительный IP-адрес позволяет выделить 64 000 дополнительных портов, однако балансер стандартной нагрузки Azure не увеличивает автоматически порты на узел при добавлении дополнительных IP-адресов. Эти значения можно изменить, установив параметры *грузоподъемности и перегрузки* и параметры *простоя груза.* Пример:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ports 0 \
    --load-balancer-idle-timeout 30
```

> [!IMPORTANT]
> Необходимо [рассчитать требуемую квоту][calculate-required-quota] перед настройкой *выделенных OutboundPorts,* чтобы избежать проблем с подключением или масштабированием. Значение, указанное для *выделенногоOutboundPorts,* также должно быть кратным 8.

Вы также можете использовать параметры *грузоотза-баланс-исходящих портов* и параметры *простоя груза-баланса-тайм-аута* при создании кластера, но вы также должны указать либо *нагрузочные-баланс-управляемые-ис-счет,* *нагрузка-баланс-баланс-ис-IPs,* или *нагрузка-баланс-баланс-изгоев-ip-префиксы,* а также.  Пример:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-managed-outbound-ip-count 2 \
    --load-balancer-outbound-ports 0 \
    --load-balancer-idle-timeout 30
```

При изменении параметров грузоподъемности *и* перегрузки и времени простоя *груза* по умолчанию это влияет на поведение профиля балансоровы груза, что влияет на весь кластер.

### <a name="required-quota-for-customizing-allocatedoutboundports"></a>Обязательная квота для настройки выделенныхВыездныхПортов
Вы должны иметь достаточно исходящих возможностей IP в зависимости от числа ваших узлов VMs и желаемых выделенных исходящих портов. Для проверки достаточного исходящего ip-потенциала используйте следующую формулу: 
 
*outboundIPs* \* 64000 \> *nodeVMs* \* *desiredAllocatedOutboundPorts*.
 
Например, если у вас есть 3 *nodeVMs*и 50 000 *желаемыхAllocatedOutboundPorts,* вам необходимо иметь по крайней мере 3 *исходящих.* Рекомендуется включить дополнительные исходящие ip-возможности сверх того, что вам нужно. Кроме того, необходимо учитывать автоскейлизатор кластера и возможность обновления пула узлов при расчете исходящих возможностей IP- иС. Для автоскалатора кластера просмотрите текущее количество узлов и максимальное количество узлов и используйте более высокое значение. Для обновления приходится дополнительный узло VM для каждого пула узлов, что позволяет модернизировать.
 
При настройке *IdleTimeoutInMinutes* на другое значение, чем по умолчанию 30 минут, учитывайте, как долго ваши рабочие нагрузки будут нуждаться в исходящих соединениях. Также учитывайте значение тайм-аута по умолчанию для балансивизатора нагрузки *Standard* SKU, используемого за пределами AKS, составляет 4 минуты. Значение *IdleTimeoutInMinutes,* которое более точно отражает вашу конкретную рабочую нагрузку AKS, может помочь уменьшить истощение SNAT, вызванное связыванием подключений, которые больше не используются.

## <a name="restrict-access-to-specific-ip-ranges"></a>Ограничение доступа к определенным диапазонам ИС

Группа сетевой безопасности (NSG), связанная с виртуальной сетью для балансоилира нагрузки, по умолчанию имеет правило, позволяющее входить в внешний трафик. Вы можете обновить это правило, чтобы разрешить только определенные диапазоны IP для входящего трафика. В следующем манифесте используется *loadBalancerSourceRanges* для определения нового диапазона IP для входящего внешнего трафика:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
  loadBalancerSourceRanges:
  - MY_EXTERNAL_IP_RANGE
```

Вышеприведенный пример обновляет правило, чтобы разрешить входящий внешний трафик только из *диапазона MY_EXTERNAL_IP_RANGE.* Более подробная информация об использовании этого метода для ограничения доступа к службе балансо-баланса нагрузки доступна в [документации Kubernetes.][kubernetes-cloud-provider-firewall]

## <a name="next-steps"></a>Следующие шаги

Узнайте больше о службах Kubernetes в [документации по службам Kubernetes][kubernetes-services].

<!-- LINKS - External -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubernetes-cloud-provider-firewall]: https://kubernetes.io/docs/tasks/access-application-cluster/configure-cloud-provider-firewall/#restrict-access-for-loadbalancer-service
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-network-lb-outbound-rule-list]: /cli/azure/network/lb/outbound-rule?view=azure-cli-latest#az-network-lb-outbound-rule-list
[az-network-public-ip-show]: /cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show
[az-network-public-ip-prefix-show]: /cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/concepts-limitations.md#skus
[azure-lb-outbound-rules]: ../load-balancer/load-balancer-outbound-rules-overview.md#snatports
[azure-lb-outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#snat
[azure-lb-outbound-preallocatedports]: ../load-balancer/load-balancer-outbound-connections.md#preallocatedports
[azure-lb-outbound-rules-overview]: ../load-balancer/load-balancer-outbound-rules-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[calculate-required-quota]: #required-quota-for-customizing-allocatedoutboundports
