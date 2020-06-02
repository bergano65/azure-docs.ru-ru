---
title: Использование Load Balancer ценовой категории "Стандартный"
titleSuffix: Azure Kubernetes Service
description: Узнайте, как использовать подсистему балансировки нагрузки ценовой категории "Стандартный" для предоставления служб с помощью Службы Azure Kubernetes (AKS).
services: container-service
author: zr-msft
ms.topic: article
ms.date: 09/27/2019
ms.author: zarhoads
ms.openlocfilehash: 14e80f6348772af77c5a53b1d5e9111c4ae8ba9b
ms.sourcegitcommit: 90d2d95f2ae972046b1cb13d9956d6668756a02e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2020
ms.locfileid: "83402069"
---
# <a name="use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Использование подсистемы балансировки нагрузки ценовой категории "Стандартный" в Службе Azure Kubernetes (AKS)

Чтобы предоставить доступ к приложениям с помощью служб Kubernetes типа `LoadBalancer` в Службе Azure Kubernetes (AKS), можно использовать Azure Load Balancer. Экземпляр Load Balancer, выполняющийся в AKS, можно использовать в качестве внутренней или внешней подсистемы балансировки нагрузки. Внутренняя подсистема балансировки нагрузки позволяет сделать службу Kubernetes доступной только для приложений, работающих в той же виртуальной сети, что и кластер AKS. Внешняя подсистема балансировки нагрузки получает один или несколько общедоступных входящих IP-адресов и делает службу Kubernetes доступной извне с помощью этих общедоступных IP-адресов.

Доступны два номера SKU Azure Load Balancer: ценовых категорий *Базовый* и *Стандартный*. По умолчанию при создании кластера AKS используется номер SKU *ценовой категории "Стандартный"* . Использование подсистемы балансировки нагрузки *ценовой категории "Стандартный"* обеспечивает дополнительные функции и возможности, такие как больший размер серверного пула и Зоны доступности. Важно понимать различия между подсистемами балансировки нагрузки ценовых категорий *Стандартный* и *Базовый*, прежде чем выбрать одну из них. После создания кластера AKS вы не сможете изменить ценовую категорию подсистемы балансировки нагрузки для него. Дополнительные сведения о номерах SKU ценовых категорий *Базовый* и *Стандартный* см. в разделе [Сравнение SKU подсистемы балансировки нагрузки][azure-lb-comparison].

В этой статье предполагается наличие базового понимания принципов работы Kubernetes и Azure Load Balancer. Дополнительные сведения см. в разделах [Ключевые концепции Kubernetes для службы Azure Kubernetes (AKS)][kubernetes-concepts] и [Что такое Azure Load Balancer?][azure-lb].

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этой статьей вам понадобится Azure CLI 2.0.81 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][install-azure-cli].

## <a name="before-you-begin"></a>Перед началом

В этой статье предполагается, что у вас есть кластер AKS с Azure Load Balancer ценовой категории *Стандартный*. Если вам нужен кластер AKS, обратитесь к краткому руководству по работе с AKS [с помощью Azure CLI][aks-quickstart-cli] или [портала Azure][aks-quickstart-portal].

Субъекту-службе кластера AKS также необходимо разрешение на управление сетевыми ресурсами, если используется существующая подсеть или группа ресурсов. В общем случае назначьте субъекту-службе роль *Участник сети* для делегированных ресурсов. Вместо субъекта-службы для разрешений можно также использовать управляемое удостоверение, назначаемое системой. Дополнительные сведения см. в статье о том, [как использовать управляемые удостоверения](use-managed-identity.md). Дополнительные сведения о разрешениях см. в разделе [Делегирование прав доступа другим ресурсам Azure][aks-sp].

### <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>Переход с Load Balancer ценовой категории "Базовый" на Load Balancer ценовой категории "Стандартный"

Если у вас есть кластер, использующий Load Balancer ценовой категории "Базовый", следует учесть важные различия в поведении при переходе на использование кластера с Load Balancer ценовой категории "Стандартный".

Например, создание "сине-зеленых" шаблонов развертывания для миграции кластеров является распространенной практикой, так как тип `load-balancer-sku` кластера можно определить только во время создания кластера. Однако экземпляры Load Balancer *ценовой категории "Базовый"* используют IP-адреса *ценовой категории "Базовый"* , несовместимые с экземплярами Load Balancer *ценовой категории "Стандартный"* , которым требуются IP-адреса *ценовой категории "Стандартный"* . При миграции кластеров для обновления номеров SKU Load Balancer потребуется новый IP-адрес с совместимым номером SKU.

Дополнительные рекомендации по миграции кластеров см. в [документации по миграции](aks-migration.md). В ней вы сможете изучить список важных тем, которые следует учитывать при миграции. Приведенные ниже ограничения также являются важными различиями в поведении, которые следует учитывать при использовании экземпляров Load Balancer ценовой категории "Стандартный" в AKS.

### <a name="limitations"></a>Ограничения

При создании и администрировании кластеров AKS, поддерживающих подсистему балансировки нагрузки *ценовой категории "Стандартный"* , действуют следующие ограничения.

* Необходим по крайней мере один общедоступный IP-адрес или префикс IP-адреса для передачи исходящего трафика из кластера AKS. Общедоступный IP-адрес или префикс IP-адреса также требуется для подключения между уровнем управления и узлами агентов, а также для обеспечения совместимости с предыдущими версиями AKS. Ниже приведены способы указания общедоступных IP-адресов или префиксов IP-адреса с помощью подсистемы балансировки нагрузки *ценовой категории "Стандартный"* .
    * Предоставьте собственные общедоступные IP-адреса.
    * Предоставьте собственные префиксы общедоступных IP-адресов.
    * Укажите число до 100, чтобы кластер AKS мог создать столько общедоступных IP-адресов *ценовой категории "Стандартный"* в той же группе ресурсов, в которой был создан кластер AKS. Обычно ее имя начинается с *MC_* . AKS назначит общедоступный IP-адрес подсистеме балансировки нагрузки *ценовой категории "Стандартный"* . По умолчанию один общедоступный IP-адрес будет автоматически создан в той же группе ресурсов, что и кластер AKS, если не указан общедоступный IP-адрес, префикс общедоступного IP-адреса или число IP-адресов. Кроме того, необходимо разрешить общедоступные адреса и не создавать политику Azure, запрещающую создание IP-адресов.
* При использовании номера SKU *ценовой категории "Стандартный"* для подсистемы балансировки нагрузки необходимо использовать Kubernetes *1.13 или более поздней версии*.
* Определить номер SKU подсистемы балансировки нагрузки можно только при создании кластера AKS. Вы не сможете изменить номер SKU подсистемы балансировки нагрузки после создания кластера AKS.
* В одном кластере можно использовать подсистему балансировки нагрузки только одной ценовой категории — "Базовый" или "Стандартный".
* Подсистемы балансировки нагрузки *ценовой категории "Стандартный"* поддерживают только IP-адреса *ценовой категории "Стандартный"* .

## <a name="use-the-standard-sku-load-balancer"></a>Использование подсистемы балансировки нагрузки *ценовой категории "Стандартный"*

После создания кластера AKS при запуске служб в нем по умолчанию используется подсистема балансировки нагрузки *ценовой категории "Стандартный"* . Например, в [кратком руководстве для Azure CLI][aks-quickstart-cli] развертывается пример приложения, использующего подсистему балансировки нагрузки *ценовой категории "Стандартный"* .

> [!IMPORTANT]
> Чтобы не использовать общедоступные IP-адреса, можно настроить определяемый пользователем маршрут (UDR). Если указать UDR в качестве типа исходящего трафика кластера AKS, то можно пропустить подготовку IP-адресов и настройку серверного пула для созданной AKS подсистемы балансировки нагрузки Azure. Узнайте, как [задать для кластера`outboundType` режим userDefinedRouting](egress-outboundtype.md).

## <a name="configure-the-load-balancer-to-be-internal"></a>Настройка внутренней подсистемы балансировки нагрузки

Можно также настроить подсистему балансировки нагрузки как внутреннюю и не предоставлять общедоступный IP-адрес. Чтобы настроить подсистему балансировки нагрузки как внутреннюю, добавьте `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` в качестве заметки в службу *LoadBalancer*. Вы можете ознакомиться с примером манифеста в формате YAML и дополнительными сведениями о внутренней подсистеме балансировки нагрузки [здесь][internal-lb-yaml].

## <a name="scale-the-number-of-managed-public-ips"></a>Масштабирование количества управляемых общедоступных IP-адресов

При использовании подсистемы балансировки нагрузки *ценовой категории "Стандартный"* с управляемыми общедоступными исходящими IP-адресами, которые создаются по умолчанию, можно масштабировать их количество с помощью параметра *load-balancer-managed-ip-count*.

Чтобы обновить существующий кластер, выполните следующую команду. Этот параметр можно также задать во время создания кластера, чтобы использовать несколько управляемых общедоступных исходящих IP-адресов.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

В приведенном выше примере задано *2* управляемых общедоступных исходящих IP-адреса для кластера *myAKSCluster* в *myResourceGroup*. 

Можно также использовать параметр *load-balancer-managed-ip-count*, чтобы задать начальное число управляемых общедоступных исходящих адресов при создании кластера, добавив параметр `--load-balancer-managed-outbound-ip-count` и задав для него нужное значение. По умолчанию число управляемых общедоступных исходящих IP-адресов равно 1.

## <a name="provide-your-own-public-ips-or-prefixes-for-egress"></a>Настройка собственных общедоступных IP-адресов или префиксов для исходящего трафика

При использовании подсистемы балансировки нагрузки *ценовой категории "Стандартный"* кластер AKS автоматически создает общедоступный IP-адрес в группе ресурсов, в которой был создан этот кластер AKS, и назначает этот общедоступный IP-адрес подсистеме балансировки нагрузки *ценовой категории "Стандартный"* . Кроме того, вы можете назначить собственный общедоступный IP-адрес во время создания кластера или обновить свойства подсистемы балансировки нагрузки существующего кластера.

Добавив несколько IP-адресов или префиксов, можно определить несколько резервных служб при определении IP-адреса одного объекта подсистемы балансировки нагрузки. Конечная точка исходящего трафика конкретных узлов будет зависеть от того, с какой службой они связаны.

### <a name="pre-requisites-to-bring-your-own-ip-addresses-or-ip-prefixes"></a>Предварительные требования для использования собственных IP-адресов или префиксов IP-адресов
1. Вы должны использовать общедоступные IP-адреса *ценовой категории "Стандартный"* для исходящего трафика подсистемы балансировки нагрузки *ценовой категории "Стандартный"* . Номер SKU общедоступных IP-адресов можно проверить с помощью команды [az network public-ip show][az-network-public-ip-show].

   ```azurecli-interactive
   az network public-ip show --resource-group myResourceGroup --name myPublicIP --query sku.name -o tsv
   ```
 1. Общедоступные IP-адреса и префиксы IP-адресов должны относиться к тому же региону и подписке, что и кластер AKS.
 1. Общедоступные IP-адреса и префиксы IP-адресов не могут быть управляемыми IP-адресами, созданными AKS. Убедитесь, что все IP-адреса, указанные в качестве настраиваемых IP-адресов, созданы вручную и не относятся к службе AKS.
 1. Общедоступные IP-адреса и префиксы IP-адресов не могут использоваться другим ресурсом или службой.

 ### <a name="define-your-own-public-ip-or-prefixes-on-an-existing-cluster"></a>Определение собственных общедоступных IP-адресов или префиксов в существующем кластере

Чтобы получить список идентификаторов общедоступных IP-адресов, используйте команду [az network public-ip show][az-network-public-ip-show].

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

Приведенная выше команда отображает идентификатор общедоступного IP-адреса *myPublicIP* в группе ресурсов *myResourceGroup*.

Чтобы применить к кластеру новые общедоступные IP-адреса, используйте команду *az aks update* с параметром *load-balancer-outbound-ips*.

В следующем примере используется параметр *load-balancer-outbound-ips* с идентификаторами из предыдущей команды.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Для исходящего трафика подсистемы балансировки нагрузки *ценовой категории "Стандартный"* можно также использовать префиксы общедоступных IP-адресов. В следующем примере для отображения идентификаторов общедоступных IP-адресов используется команда [az network public-ip prefix show][az-network-public-ip-prefix-show].

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

Приведенная выше команда отображает идентификатор префикса общедоступного IP-адреса *myPublicIPPrefix* в группе ресурсов *myResourceGroup*.

В следующем примере используется параметр *load-balancer-outbound-ip-prefixes* с идентификаторами из предыдущей команды.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

### <a name="define-your-own-public-ip-or-prefixes-at-cluster-create-time"></a>Определение собственных общедоступных IP-адресов или префиксов пи создании кластера

Вы можете настроить собственные IP-адреса или префиксы IP-адресов для исходящего трафика во время создания кластера, например, чтобы добавить конечные точки исходящего трафика в список разрешений. Добавьте параметры, показанные выше, на шаге создания кластера, чтобы определить собственные общедоступные IP-адреса и префиксы IP-адресов в самом начале жизненного цикла кластера.

Чтобы создать кластер с указанными общедоступными IP-адресами, используйте команду *az aks create* с параметром *load-balancer-outbound-ips*.

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

Используйте команду *az aks create* с параметром *load-balancer-outbound-ip-prefixes*, чтобы создать кластер с префиксами общедоступных IP-адресов.

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

## <a name="configure-outbound-ports-and-idle-timeout"></a>Настройка исходящих портов и времени ожидания

> [!WARNING]
> Следующий раздел предназначен для реализации масштабных сетей или устранения проблем нехватки адресов SNAT в конфигурациях по умолчанию. Чтобы сохранить работоспособность кластеров, необходимо точно учесть доступную квоту виртуальных машин и IP-адресов, прежде чем изменять значения по умолчанию параметров *AllocatedOutboundPorts* и *IdleTimeoutInMinutes*.
> 
> Изменение значений *AllocatedOutboundPorts* и *IdleTimeoutInMinutes* может значительно изменить поведение правила для исходящего трафика для подсистемы балансировки нагрузки. Проверьте [правила для исходящего трафика][azure-lb-outbound-rules-overview], [правила для входящего трафика][azure-lb-outbound-rules] и [исходящие подключения в Azure][azure-lb-outbound-connections] для подсистемы балансировки нагрузки, прежде чем изменять эти значения, чтобы полностью понять влияние вносимых изменений.

Для [SNAT][azure-lb-outbound-connections] используются выделенные исходящие порты и их время ожидания. По умолчанию подсистема балансировки нагрузки *ценовой категории "Стандартный"* использует [автоматическое назначение количества исходящих портов на основе размера серверного пула][azure-lb-outbound-preallocatedports] и время ожидания в 30 минут для каждого порта. Чтобы просмотреть эти значения, используйте команду [az network lb outbound-rule list][az-network-lb-outbound-rule-list]. Она отобразит правило для исходящего трафика для подсистемы балансировки нагрузки.

```azurecli-interactive
NODE_RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az network lb outbound-rule list --resource-group $NODE_RG --lb-name kubernetes -o table
```

Предыдущие команды отобразили бы правило для исходящего трафика для подсистемы балансировки нагрузки, например:

```console
AllocatedOutboundPorts    EnableTcpReset    IdleTimeoutInMinutes    Name             Protocol    ProvisioningState    ResourceGroup
------------------------  ----------------  ----------------------  ---------------  ----------  -------------------  -------------
0                         True              30                      aksOutboundRule  All         Succeeded            MC_myResourceGroup_myAKSCluster_eastus  
```

В выходных данных примера показано значение по умолчанию для *AllocatedOutboundPorts* и *IdleTimeoutInMinutes*. Значение 0 параметра *AllocatedOutboundPorts* позволяет автоматически назначать количество исходящих портов на основе размера серверного пула. Например, если кластер содержит 50 или менее узлов, для каждого узла выделяются 1024 порта.

Рекомендуется изменить значение параметра *allocatedOutboundPorts* или *IdleTimeoutInMinutes*, если предполагается нехватка адресов SNAT при использовании указанной выше конфигурации по умолчанию. Каждый дополнительный IP-адрес обеспечивает выделение 64 000 дополнительных портов, однако Load Balancer Azure ценовой категории "Стандартный" не увеличивает число портов на узел автоматически при добавлении дополнительных IP-адресов. Вы можете изменить эти значения, задав параметры *load-balancer-outbound-ports* и *load-balancer-idle-timeout*. Пример:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ports 0 \
    --load-balancer-idle-timeout 30
```

> [!IMPORTANT]
> Чтобы избежать проблем с подключениями или масштабированием, перед настройкой *allocatedOutboundPorts* необходимо [вычислить требуемую квоту][calculate-required-quota]. Значение, указанное для *allocatedOutboundPorts*, также должно быть кратно 8.

При создании кластера можно также использовать параметры *load-balancer-outbound-ports* и *load-balancer-idle-timeout*, но тогда обязательно указать параметр *load-balancer-managed-outbound-ip-count*, *load-balancer-outbound-ips* или *load-balancer-outbound-ip-prefixes*.  Пример:

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

Если изменить значения по умолчанию параметров *load-balancer-outbound-ports* и *load-balancer-idle-timeout*, это повлияет на поведение профиля подсистемы балансировки нагрузки, что отразится на работе всего кластера.

### <a name="required-quota-for-customizing-allocatedoutboundports"></a>Требуемая квота для настройки allocatedOutboundPorts
Необходимо иметь достаточное количество исходящих IP-адресов в зависимости от числа виртуальных машин узла и требуемых выделенных исходящих портов. Чтобы проверить, достаточно ли выделено исходящих IP-адресов, используйте приведенную ниже формулу. 
 
*outboundIPs* \* 64 000 \> *nodeVMs* \* *desiredAllocatedOutboundPorts*.
 
Например, если имеются 3 узла *nodeVMs* и 50 000 портов *desiredAllocatedOutboundPorts*, необходимо по крайней мере 3 адреса *outboundIPs*. Рекомендуется подготовить дополнительные исходящие IP-адреса. Кроме того, при вычислении емкости исходящих IP-адресов необходимо учитывать автомасштабирование кластера и возможность обновления пула узлов. Чтобы учесть автомасштабирование кластера, проверьте текущее число узлов и максимальное количество узлов и используйте большее значение. Чтобы учесть обновление, добавьте по одной дополнительной виртуальной машине узла на каждый пул узлов, который допускает обновление.
 
Если для параметра *IdleTimeoutInMinutes* задано значение, отличное от значения по умолчанию (30 минут), определите, какой продолжительности исходящее подключение потребуется рабочим нагрузкам. Также учтите, что время ожидания по умолчанию для подсистемы балансировки нагрузки *ценовой категории "Стандартный"* , используемой за пределами AKS, составляет 4 минуты. Значение *IdleTimeoutInMinutes*, более точно соответствующее конкретной рабочей нагрузке AKS, может снизить нехватку адресов SNAT, вызванную поддержанием подключений, которые больше не используются.

## <a name="restrict-access-to-specific-ip-ranges"></a>Ограничение доступа для определенных диапазонов IP-адресов

Группа безопасности сети (NSG), связанная с виртуальной сетью подсистемы балансировки нагрузки, по умолчанию содержит правило, разрешающее весь внешний входящий трафик. Вы можете изменить это правило, чтобы разрешить только определенные диапазоны IP-адресов для передачи входящего трафика. В приведенном ниже манифесте с помощью параметра *loadBalancerSourceRanges* указан новый диапазон IP-адресов для внешнего входящего трафика.

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

В приведенном выше примере правило изменено, чтобы разрешить внешний входящий трафик из диапазона *MY_EXTERNAL_IP_RANGE*. Дополнительные сведения об использовании этого метода для ограничения доступа к службе подсистемы балансировки нагрузки доступны в [документации по Kubernetes][kubernetes-cloud-provider-firewall].

## <a name="next-steps"></a>Дальнейшие действия

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
[azure-lb-comparison]: ../load-balancer/skus.md
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
