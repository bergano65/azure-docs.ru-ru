---
title: Использование балансировщика нагрузки "Стандартный" в службе Kubernetes Azure (AKS)
description: Узнайте, как использовать подсистему балансировки нагрузки со стандартным SKU для предоставления служб службе Azure Kubernetes Service (AKS).
services: container-service
author: zr-msft
ms.topic: article
ms.date: 09/27/2019
ms.author: zarhoads
ms.openlocfilehash: 9c414572e1c3b2f046ae9a14139885e9927ab3bb
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79252913"
---
# <a name="use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Использование балансировщика нагрузки "Стандартный" в службе Kubernetes Azure (AKS)

Чтобы предоставить доступ к приложениям с помощью Kubernetes Services типа `LoadBalancer` в службе Kubernetes Azure (AKS), можно использовать Azure Load Balancer. Балансировщик нагрузки, выполняющийся в AKS, можно использовать в качестве внутренней или внешней подсистемы балансировки нагрузки. Внутренний балансировщик нагрузки делает службу Kubernetes доступной только для приложений, работающих в той же виртуальной сети, что и кластер AKS. Внешняя подсистема балансировки нагрузки получает один или несколько общедоступных IP-адресов для входящего трафика и делает службу Kubernetes доступной извне с помощью общедоступных IP-адресов.

Доступны два номера SKU Azure Load Balancer: ценовых категорий *Базовый* и *Стандартный*. По умолчанию при создании кластера AKS используется номер SKU " *стандартный* ". Использование подсистемы балансировки нагрузки уровня " *стандартный* " предоставляет дополнительные функции и функции, такие как размер внутреннего пула и зоны доступности. Важно понимать различия между *стандартными* и *базовыми* подсистемами балансировки нагрузки перед выбором используемого. После создания кластера AKS вы не сможете изменить SKU подсистемы балансировки нагрузки для этого кластера. Дополнительные сведения о SKU уровня " *базовый* " и " *стандартный* " см. в статье [Сравнение SKU в подсистеме балансировки нагрузки Azure][azure-lb-comparison].

В этой статье предполагается, что основное понимание Kubernetes и Azure Load Balancer концепций. Дополнительные сведения см. в разделе [Основные понятия Kubernetes Core для службы Kubernetes Azure (AKS)][kubernetes-concepts] и [что Azure Load Balancer?][azure-lb].

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать CLI локально, для работы с этой статьей требуется Azure CLI версии 2.0.81 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][install-azure-cli].

## <a name="before-you-begin"></a>Перед началом

В этой статье предполагается, что у вас есть кластер AKS со *стандартным* номером SKU Azure Load Balancer. Если вам нужен кластер AKS, ознакомьтесь с кратким руководством по AKS, [используя Azure CLI][aks-quickstart-cli] или [с помощью портал Azure][aks-quickstart-portal].

Субъекту-службе кластера AKS также необходимо разрешение на управление сетевыми ресурсами, если используется существующая подсеть или группа ресурсов. В общем случае назначьте роль *участника сети* субъекту-службе для делегированных ресурсов. Дополнительные сведения о разрешениях см. в статье [Делегирование AKS доступ к другим ресурсам Azure][aks-sp].

### <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>Переход с базового SKU Load Balancer на SKU уровня "Стандартный"

Если у вас есть кластер, имеющий базовый Load Balancer SKU, существуют важные различия в поведении, которые следует учитывать при миграции на использование кластера со стандартным SKU Load Balancer.

Например, для миграции кластеров обычно используются синие и зеленые развертывания, поэтому тип `load-balancer-sku` кластера можно определить только во время создания кластера. Однако подсистемы балансировки нагрузки уровня " *базовый* " используют базовые IP-адреса *SKU* , которые несовместимы с подсистемами балансировки нагрузки уровня " *стандартный* ", так как им требуются стандартные IP-адреса *SKU* . При переносе кластеров для обновления Load Balancer номера SKU потребуется новый IP-адрес с совместимым номером SKU IP-адреса.

Дополнительные сведения о миграции кластеров см. в [документации по миграции](aks-migration.md) , чтобы просмотреть список важных вопросов, которые следует учитывать при миграции. Приведенные ниже ограничения также являются важными различиями в поведении, которые следует учитывать при использовании подсистем балансировки нагрузки уровня "Стандартный" в AKS.

### <a name="limitations"></a>Ограничения

При создании кластеров AKS, поддерживающих подсистему балансировки нагрузки со *стандартным* SKU, применяются следующие ограничения.

* По крайней мере один общедоступный IP-адрес или префикс IP-адреса необходим для разрешения исходящего трафика из кластера AKS. Общедоступный IP-адрес или префикс IP-адреса также требуется для поддержания подключения между узлами управления и агентами, а также для обеспечения совместимости с предыдущими версиями AKS. Существуют следующие варианты указания общедоступных IP-адресов или префиксов IP с подсистемой балансировки нагрузки уровня " *стандартный* ":
    * Предоставьте собственные общедоступные IP-адреса.
    * Предоставьте собственные префиксы общедоступных IP-адресов.
    * Укажите число до 100, чтобы разрешить кластеру AKS создавать общедоступные IP-адреса уровня " *стандартный* " в той же группе ресурсов, которая создается в качестве кластера AKS, которая обычно называется *MC_* в начале. AKS назначает общедоступный IP-адрес подсистеме балансировки нагрузки уровня " *стандартный* ". По умолчанию один общедоступный IP-адрес будет автоматически создан в той же группе ресурсов, что и кластер AKS, если не указан общедоступный IP-адрес, префикс общедоступного IP-адреса или число адресов IP. Кроме того, необходимо разрешить общедоступные адреса и избежать создания политики Azure, Ban создание IP-адресов.
* При использовании SKU " *стандартный* " для подсистемы балансировки нагрузки необходимо использовать Kubernetes версии *1,13 или более поздней*.
* Определение номера SKU подсистемы балансировки нагрузки может выполняться только при создании кластера AKS. Вы не можете изменить SKU балансировщика нагрузки после создания кластера AKS.
* В одном кластере можно использовать только один тип номера SKU балансировщика нагрузки (базовый или стандартный).
* *Стандартный выпуск* Подсистемы балансировки нагрузки SKU поддерживают только IP-адреса *стандартного* SKU.

## <a name="use-the-standard-sku-load-balancer"></a>Использование балансировщика нагрузки SKU " *стандартный* "

При создании кластера AKS по умолчанию подсистема балансировки нагрузки уровня " *стандартный* " используется при запуске служб в этом кластере. Например, [Краткое руководство][aks-quickstart-cli] , в котором используется Azure CLI, развертывает пример приложения, использующего подсистему балансировки нагрузки уровня " *стандартный* ".

> [!IMPORTANT]
> Общедоступные IP-адреса можно избежать, настроив определяемый пользователем маршрут (UDR). При указании типа исходящего трафика кластера AKS в качестве UDR можно пропустить подготовку IP-адресов и настройки внутреннего пула для созданной AKS подсистемы балансировки нагрузки Azure. См. раздел [настройка `outboundType` кластера в значение "усердефинедраутинг"](egress-outboundtype.md).

## <a name="configure-the-load-balancer-to-be-internal"></a>Настройка подсистемы балансировки нагрузки в качестве внутренней

Вы также можете настроить подсистему балансировки нагрузки как внутреннюю и не предоставлять общедоступный IP-адрес. Чтобы настроить подсистему балансировки нагрузки как внутреннюю, добавьте `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` в качестве заметки *для службы балансировщика нагрузки.* Пример манифеста YAML, а также дополнительные сведения о внутреннем подсистеме балансировки нагрузки можно увидеть [здесь][internal-lb-yaml].

## <a name="scale-the-number-of-managed-public-ips"></a>Масштабирование количества управляемых общедоступных IP-адресов

При использовании балансировщика нагрузки " *стандартный* " с управляемыми исходящими общедоступными IP-адресами, которые создаются по умолчанию, можно масштабировать количество управляемых исходящих общедоступных IP-адресов с помощью параметра *подсистемы балансировки нагрузки, управляемого счетчиком* .

Чтобы обновить существующий кластер, выполните следующую команду. Этот параметр также можно задать во время создания кластера, чтобы иметь несколько управляемых исходящих общедоступных IP-адресов.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

В приведенном выше примере в качестве количества управляемых исходящих общедоступных IP-адресов устанавливается значение *2* для кластера *myAKSCluster* в *myResourceGroup*. 

Можно также использовать параметр *балансировки нагрузки-Managed-IP-Count* , чтобы задать начальное число управляемых исходящих общедоступных адресов при создании кластера, добавив параметр `--load-balancer-managed-outbound-ip-count` и задав для него нужное значение. По умолчанию общее число управляемых исходящих общедоступных IP-адресов равно 1.

## <a name="provide-your-own-public-ips-or-prefixes-for-egress"></a>Предоставьте собственные общедоступные IP-адреса или префиксы для исходящего трафика

При использовании балансировщика нагрузки *стандартного* SKU кластер AKS автоматически создает общедоступный IP-адрес в той же группе ресурсов, которая создана для кластера AKS, и назначает общедоступный IP-адрес подсистеме балансировки нагрузки уровня " *стандартный* ". Кроме того, вы можете назначить собственный общедоступный IP-адрес во время создания кластера или обновить свойства подсистемы балансировки нагрузки существующего кластера.

Путем объединения нескольких IP-адресов или префиксов можно определить несколько служб резервного копирования при определении IP-адреса за одним объектом балансировщика нагрузки. Конечная точка исходящего трафика конкретных узлов будет зависеть от того, с какой службой они связаны.

> [!IMPORTANT]
> Вы должны использовать общедоступные IP-адреса уровня " *стандартный* " для исходящего трафика с помощью *стандартного* SKU балансировщика нагрузки. Номер SKU общедоступных IP-адресов можно проверить с помощью команды [AZ Network public-IP-шоу][az-network-public-ip-show] :
>
> ```azurecli-interactive
> az network public-ip show --resource-group myResourceGroup --name myPublicIP --query sku.name -o tsv
> ```

Чтобы получить список идентификаторов общедоступных IP-адресов, используйте команду [AZ Network public-IP-шоу][az-network-public-ip-show] .

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

Приведенная выше команда показывает идентификатор общедоступного IP-адреса *myPublicIP* в группе ресурсов *myResourceGroup* .

Используйте команду *AZ AKS Update* с параметром *Load-балансировщика-Outbound-IP* , чтобы обновить кластер с помощью общедоступных адресов.

В следующем примере используется параметр *балансировки нагрузки — исходящие IP-адреса* с идентификаторами из предыдущей команды.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Вы также можете использовать префиксы общедоступных IP-адресов для исходящего трафика с помощью балансировщика нагрузки *стандартного* SKU. В следующем примере используется команда [AZ Network public-IP prefix показывать][az-network-public-ip-prefix-show] , чтобы получить список идентификаторов префиксов общедоступных IP-адресов:

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

Приведенная выше команда показывает идентификатор для префикса общедоступного IP-адреса *мипублиЦиппрефикс* в группе ресурсов *myResourceGroup* .

В следующем примере используется параметр *Loading-outbounding-IP-префиксы* с идентификаторами из предыдущей команды.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

> [!IMPORTANT]
> Общедоступные IP-адреса и префиксы IP должны находиться в том же регионе и в той же подписке, что и кластер AKS. 

### <a name="define-your-own-public-ip-or-prefixes-at-cluster-create-time"></a>Определение собственных общедоступных IP-адресов или префиксов во время создания кластера

Вы можете приложить собственные IP-адреса или префиксы IP-адресов для исходящего трафика во время создания кластера для поддержки таких сценариев, как конечные точки исходящего трафика список разрешений. Добавьте те же параметры, показанные выше, на шаг создания кластера, чтобы определить собственные общедоступные IP-адреса и префиксы IP в начале жизненного цикла кластера.

Чтобы создать новый кластер с общедоступными IP-адресами в начале, используйте команду *AZ AKS Create* с параметром *балансировки нагрузки — исходящие IP-адреса* .

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

Используйте команду *AZ AKS Create* с параметром *Load-балансировщик-Outbound-IP-префиксы* , чтобы создать новый кластер с префиксами общедоступного IP-адреса в начале.

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

## <a name="configure-outbound-ports-and-idle-timeout"></a>Настройка исходящих портов и времени ожидания простоя

> [!WARNING]
> Следующий раздел предназначен для расширенных сценариев более масштабных сетей или для устранения проблем нехватки SNAT с конфигурациями по умолчанию. Чтобы поддерживать работоспособные кластеры, необходимо иметь точную инвентаризацию доступной квоты для виртуальных машин и IP-адресов перед изменением значения по умолчанию *аллокатедаутбаундпортс* или *идлетимеаутинминутес* .
> 
> Изменение значений для *аллокатедаутбаундпортс* и *идлетимеаутинминутес* может значительно изменить поведение исходящего правила для балансировщика нагрузки. Перед обновлением этих значений проверьте [Load Balancer правила][azure-lb-outbound-rules-overview]исходящих подключений, [Исходящие правила подсистемы балансировки нагрузки][azure-lb-outbound-rules]и [Исходящие подключения в Azure][azure-lb-outbound-connections] , чтобы полностью оценить влияние изменений.

Для [SNAT][azure-lb-outbound-connections]используются исходящие выделенные порты и их время ожидания простоя. По умолчанию балансировщик нагрузки " *стандартный* " использует [Автоматическое назначение количества исходящих портов на основе размера серверного пула][azure-lb-outbound-preallocatedports] и 30-минутное время ожидания простоя для каждого порта. Чтобы просмотреть эти значения, используйте команду [AZ Network фунтов исходящие-Rules][az-network-lb-outbound-rule-list] , чтобы отобразить правило исходящего трафика для балансировщика нагрузки:

```azurecli-interactive
NODE_RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az network lb outbound-rule list --resource-group $NODE_RG --lb-name kubernetes -o table
```

В предыдущих командах будет перечисляться правило исходящего трафика для балансировщика нагрузки, например:

```console
AllocatedOutboundPorts    EnableTcpReset    IdleTimeoutInMinutes    Name             Protocol    ProvisioningState    ResourceGroup
------------------------  ----------------  ----------------------  ---------------  ----------  -------------------  -------------
0                         True              30                      aksOutboundRule  All         Succeeded            MC_myResourceGroup_myAKSCluster_eastus  
```

В выходных данных примера показано значение по умолчанию для *аллокатедаутбаундпортс* и *идлетимеаутинминутес*. Значение 0 для *аллокатедаутбаундпортс* задает количество исходящих портов, использующих автоматическое назначение количества исходящих портов на основе размера внутреннего пула. Например, если кластер содержит 50 или менее узлов, выделяются 1024 портов для каждого узла.

Рассмотрите возможность изменения параметра *аллокатедаутбаундпортс* или *идлетимеаутинминутес* , если вы хотите настроить нехватку SNAT на основе указанной выше конфигурации по умолчанию. Каждый дополнительный IP-адрес обеспечивает 64 000 дополнительных портов для выделения, однако Load Balancer (цен. категория "Стандартный") Azure не увеличивает число портов автоматически на узел при добавлении дополнительных IP-адресов. Эти значения можно изменить, задав параметры *балансировки нагрузки-исходящие-порты* и *балансировки нагрузки — время ожидания простоя* . Пример:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ports 0 \
    --load-balancer-idle-timeout 30
```

> [!IMPORTANT]
> Необходимо [рассчитать требуемую квоту][calculate-required-quota] перед настройкой *аллокатедаутбаундпортс* , чтобы избежать проблем с подключением или масштабированием. Значение, указанное для *аллокатедаутбаундпортс* , также должно быть кратным 8.

Кроме того, при создании кластера можно использовать параметры *балансировки нагрузки-исходящих портов* и *балансировщика нагрузки — время ожидания* , но необходимо также указать *подсистемы балансировки нагрузки-Managed-исходящий-IP-Count*, *Load-* IP-и (или) *балансировки нагрузки-исходящего трафика* .  Пример:

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

При изменении параметров по умолчанию для подсистемы *балансировки нагрузки — исходящих портов* и *подсистемы балансировки нагрузки-истечения времени ожидания* , это влияет на поведение профиля подсистемы балансировки нагрузки, что влияет на весь кластер.

### <a name="required-quota-for-customizing-allocatedoutboundports"></a>Требуемая квота для настройки Аллокатедаутбаундпортс
Необходимо иметь достаточное количество исходящих IP-адресов на основе числа виртуальных машин узлов и требуемых выделенных исходящих портов. Чтобы проверить наличие достаточного количества исходящих IP-адресов, используйте следующую формулу: 
 
*аутбаундипс* \* 64 000 \> *нодевмс* \* *десиредаллокатедаутбаундпортс*.
 
Например, если у вас есть 3 *нодевмс*и 50 000 *десиредаллокатедаутбаундпортс*, необходимо иметь по крайней мере 3 *аутбаундипс*. Рекомендуется включать дополнительный объем исходящих IP-адресов, помимо того, что вам нужно. Кроме того, при расчете исходящих IP-адресов необходимо учитывать автоматическое масштабирование кластера и возможность обновления пула узлов. Для автомасштабирования кластера Проверьте текущее число узлов и максимальное количество узлов и используйте более высокое значение. Для обновления учетная запись для дополнительного узла виртуальной машины для каждого пула узлов, который допускает обновление.
 
Если для параметра *идлетимеаутинминутес* задано значение, отличное от 30 минут, определите, сколько времени для рабочих нагрузок потребуется исходящее подключение. Также рассмотрите значение времени ожидания по умолчанию для подсистемы балансировки нагрузки уровня " *стандартный* ", используемой за пределами AKS, — 4 минуты. Значение *идлетимеаутинминутес* , которое более точно отражает вашу конкретную рабочую нагрузку AKS, может помочь уменьшить нехватку SNAT, вызванное присоединением подключений, которые больше не используются.

## <a name="restrict-access-to-specific-ip-ranges"></a>Ограничение доступа к определенным диапазонам IP-адресов

Группа безопасности сети (NSG), связанная с виртуальной сетью для балансировщика нагрузки, по умолчанию имеет правило, разрешающее весь входящий внешний трафик. Вы можете обновить это правило, разрешающее только определенные диапазоны IP-адресов для входящего трафика. Следующий манифест использует *лоадбаланцерсаурцеранжес* для указания нового диапазона IP-адресов для входящего внешнего трафика:

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

В приведенном выше примере правило обновляется, разрешающим только входящий внешний трафик из диапазона *MY_EXTERNAL_IP_RANGE* . Дополнительные сведения об использовании этого метода для ограничения доступа к службе балансировщика нагрузки доступны в [документации по Kubernetes][kubernetes-cloud-provider-firewall].

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Kubernetes Services см. в [документации по службам Kubernetes][kubernetes-services].

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
