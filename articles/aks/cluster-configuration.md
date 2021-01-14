---
title: Настройка кластера в Службе Azure Kubernetes (AKS)
description: Сведения о том, как настроить кластер в Службе Kubernetes Azure (AKS)
services: container-service
ms.topic: article
ms.date: 01/13/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: eacca50e00dfe8625d86362c444544e2fd5d5511
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201116"
---
# <a name="configure-an-aks-cluster"></a>Настройка кластера AKS.

При создании кластера AKS, возможно, потребуется изменить его конфигурацию для выполнения определенных задач. В этой статье описаны некоторые возможности персонализации кластера AKS.

## <a name="os-configuration"></a>Конфигурация ОС

AKS теперь поддерживает Ubuntu 18,04 в качестве операционной системы узла (ОС) в общем доступе для кластеров в kubernetes версиях выше, чем 1.18.8. Для версий ниже 18E. x AKS Ubuntu 16,04 по-прежнему является базовым образом по умолчанию. Начиная с kubernetes v 18E. x и наоборот, база по умолчанию — AKS Ubuntu 18,04.

### <a name="use-aks-ubuntu-1804-generally-available-on-new-clusters"></a>Использование AKS Ubuntu 18,04, как правило, доступно в новых кластерах

Кластеры, созданные в Kubernetes v 18E или более поздней версии по умолчанию, — `AKS Ubuntu 18.04` образ узла. Пулы узлов в поддерживаемой версии Kubernetes менее 1,18 будут по-прежнему получать `AKS Ubuntu 16.04` образ узла, но будут обновлены до тех пор, пока `AKS Ubuntu 18.04` версия кластера или пула узлов Kubernetes обновляется до версии 18E или выше.

Настоятельно рекомендуется протестировать рабочие нагрузки в пулах узлов AKS Ubuntu 18,04, прежде чем использовать кластеры на 1,18 или более поздней версии. Узнайте о том, как [тестировать пулы узлов Ubuntu 18,04](#test-aks-ubuntu-1804-generally-available-on-existing-clusters).

Чтобы создать кластер с помощью `AKS Ubuntu 18.04` образа узла, просто создайте кластер под названием kubernetes v 18E или выше, как показано ниже.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

### <a name="use-aks-ubuntu-1804-generally-available-on-existing-clusters"></a>Использование AKS Ubuntu 18,04, как правило, доступно в существующих кластерах

Кластеры, созданные в Kubernetes v 18E или более поздней версии по умолчанию, — `AKS Ubuntu 18.04` образ узла. Пулы узлов в поддерживаемой версии Kubernetes менее 1,18 будут по-прежнему получать `AKS Ubuntu 16.04` образ узла, но будут обновлены до тех пор, пока `AKS Ubuntu 18.04` версия кластера или пула узлов Kubernetes обновляется до версии 18E или выше.

Настоятельно рекомендуется протестировать рабочие нагрузки в пулах узлов AKS Ubuntu 18,04, прежде чем использовать кластеры на 1,18 или более поздней версии. Узнайте о том, как [тестировать пулы узлов Ubuntu 18,04](#test-aks-ubuntu-1804-generally-available-on-existing-clusters).

Если кластеры или пулы узлов готовы к `AKS Ubuntu 18.04` образу узла, вы можете просто обновить их до версии 18E или выше.

```azurecli
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

Если нужно просто обновить только один пул узлов, выполните следующие действия.

```azurecli
az aks nodepool upgrade -name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

### <a name="test-aks-ubuntu-1804-generally-available-on-existing-clusters"></a>Тестирование AKS Ubuntu 18,04, как правило, доступно в существующих кластерах

Пулы узлов, созданные в Kubernetes v 18E или более поздней версии по умолчанию для `AKS Ubuntu 18.04` образа узла. Пулы узлов в поддерживаемой версии Kubernetes менее 1,18 будут по-прежнему получать `AKS Ubuntu 16.04` образ узла, но будут обновлены до тех пор, пока `AKS Ubuntu 18.04` версия Kubernetes пула узлов будет обновлена до версии v 18E или выше.

Настоятельно рекомендуется протестировать рабочие нагрузки в пулах узлов AKS Ubuntu 18,04 перед обновлением пулов производственных узлов.

Чтобы создать пул узлов с помощью `AKS Ubuntu 18.04` образа узла, просто создайте пул узлов под kubernetes v 18E или более поздней версии. Плоскость управления кластером должна быть не ниже версии v 18E или выше, но другие пулы узлов могут остаться в более старых версиях kubernetes.
Ниже мы сначала обновляем плоскость управления, а затем создаем новый пул узлов с помощью v 18E, который получит новую версию ОС образа узла.

```azurecli
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14 --control-plane-only

az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

### <a name="use-aks-ubuntu-1804-on-new-clusters-preview"></a>Использование AKS Ubuntu 18,04 в новых кластерах (Предварительная версия)

В следующем разделе объясняется, как использовать и тестировать AKS Ubuntu 18,04 в кластерах, которые еще не используют kubernetes версии 18E. x или выше, или были созданы до того, как эта функция стала общедоступной, с помощью предварительной версии конфигурации ОС.

Нужно установить следующие ресурсы:

- [Azure CLI][azure-cli-install]версии 2.2.0 или более поздней.
- расширение aks-preview 0.4.35.

Чтобы установить расширение aks-preview 0.4.35 или более поздней версии, выполните следующие команды Azure CLI:

```azurecli
az extension add --name aks-preview
az extension list
```

Зарегистрируйте компонент `UseCustomizedUbuntuPreview`:

```azurecli
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService
```

Состояние **Registered** (Зарегистрировано) может появиться через несколько минут. Состояние регистрации можно проверить с помощью команды [az feature list](/cli/azure/feature#az-feature-list):

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

Когда отобразится правильный статус, обновите регистрацию поставщика ресурсов `Microsoft.ContainerService` с помощью команды [az provider register](/cli/azure/provider#az-provider-register):

```azurecli
az provider register --namespace Microsoft.ContainerService
```

При создании кластера выберите для него ОС Ubuntu 18.04. Установите флаг `--aks-custom-headers`, чтобы назначить Ubuntu 18.04 операционной системой по умолчанию.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Если вы хотите создать кластеры с образом AKS Ubuntu 16,04, это можно сделать, опустив пользовательский `--aks-custom-headers` тег.

### <a name="use-aks-ubuntu-1804-existing-clusters-preview"></a>Использование существующих кластеров AKS Ubuntu 18,04 (Предварительная версия)

Настройте для нового пула использование Ubuntu 18.04. Установите флаг `--aks-custom-headers`, чтобы назначить Ubuntu 18.04 операционной системой по умолчанию для этого пула узлов.

```azurecli
az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Если вы хотите создать пулы узлов с помощью образа AKS Ubuntu 16,04, это можно сделать, опустив пользовательский `--aks-custom-headers` тег.

## <a name="container-runtime-configuration"></a>Конфигурация среды выполнения контейнера

Среда выполнения контейнера — это программное обеспечение, выполняющее контейнеры и управляющее образами контейнеров на узле. Среда выполнения помогает отказаться от функций, связанных с sys-Calls или операционной системой (ОС), для запуска контейнеров в Linux или Windows. Кластеры AKS с использованием пулов узлов Kubernetes версии 1,19 и более используются `containerd` в качестве среды выполнения контейнеров. Кластеры AKS, использующие Kubernetes до v 1.19 для пулов узлов, используют [значок Кита](https://mobyproject.org/) (вышестоящее DOCKER) в качестве среды выполнения контейнера.

![DOCKER CRI 1](media/cluster-configuration/docker-cri.png)

[`Containerd`](https://containerd.io/) — Это базовая среда выполнения класса [OCI](https://opencontainers.org/) (открытая инициатива по открытому контейнеру), которая предоставляет минимальный набор необходимых функций для выполнения контейнеров и управления образами на узле. Он был [передан в](https://www.cncf.io/announcement/2017/03/29/containerd-joins-cloud-native-computing-foundation/) облачную основу вычислений (кнкф) в марте 2017. Текущая версия значок Кита, используемая AKS, уже использует и строится на основе `containerd` , как показано выше.

С помощью `containerd` пулов узлов и узлов на основе, вместо того `dockershim` , чтобы обращаться к, kubelet будет напрямую взаимодействовать `containerd` через подключаемый модуль CRI (интерфейс среды выполнения контейнера), удаляя дополнительные прыжки в потоке по сравнению с реализацией DOCKER CRI. Таким образом, вы увидите лучшую задержку при запуске модуля и меньшее использование ресурсов (ЦП и памяти).

При использовании `containerd` для узлов AKS задержка запуска Pod повышается, и потребление ресурсов узла уменьшается в результате выполнения контейнера. Эти улучшения реализованы в этой новой архитектуре, где kubelet напрямую обращается к `containerd` подключаемому модулю CRI, а в значок Кита/DOCKER Architecture kubelet будет взаимодействовать с `dockershim` подсистемой DOCKER и до достижения этого места `containerd` , что потребует дополнительных прыжков в потоке.

![DOCKER, CRI 2](media/cluster-configuration/containerd-cri.png)

`Containerd` работает с каждой общедоступной версией Kubernetes в AKS и в каждой вышестоящей версии Kubernetes выше v 1.19 и поддерживает все функции Kubernetes и AKS.

> [!IMPORTANT]
> Кластеры с пулами узлов, созданные в Kubernetes v 1.19 или более поздней версии по умолчанию для `containerd` среды выполнения контейнера. Кластеры с пулами узлов в поддерживаемой версии Kubernetes меньше 1,19 получаются `Moby` для своей среды выполнения контейнера, но будут обновлены до тех пор, пока `ContainerD` версия Kubernetes пула узлов обновится до версии 1.19 или выше. Вы по-прежнему можете использовать `Moby` Пулы узлов и кластеры в старых поддерживаемых версиях, пока они не будут отключены.
> 
> Настоятельно рекомендуется протестировать рабочие нагрузки в пулах узлов AKS `containerD` до использования кластеров в 1,19 или более поздней версии.

В следующем разделе объясняется, как можно использовать и тестировать AKS в `containerD` кластерах, которые еще не используют Kubernetes версии 1,19 или более поздней, или были созданы до того, как эта функция стала общедоступной, с помощью предварительной версии среды выполнения контейнера.

### <a name="use-containerd-as-your-container-runtime-preview"></a>Использование `containerd` в качестве среды выполнения контейнеров (Предварительная версия)

Необходимо иметь следующие предварительные требования:

- [Azure CLI][azure-cli-install], установленная версия 2.8.0 или более поздняя
- Расширение AKS-Preview версии 0.4.53 или более поздней.
- `UseCustomizedContainerRuntime`Зарегистрированный флаг функции
- `UseCustomizedUbuntuPreview`Зарегистрированный флаг функции

Чтобы установить расширение AKS-Preview 0.4.53 или более позднюю версию, используйте следующие Azure CLI команды:

```azurecli
az extension add --name aks-preview
az extension list
```

Зарегистрируйте `UseCustomizedContainerRuntime` `UseCustomizedUbuntuPreview` компоненты и:

```azurecli
az feature register --name UseCustomizedContainerRuntime --namespace Microsoft.ContainerService
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService

```

Состояние **Registered** (Зарегистрировано) может появиться через несколько минут. Состояние регистрации можно проверить с помощью команды [az feature list](/cli/azure/feature#az-feature-list):

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedContainerRuntime')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

Когда отобразится правильный статус, обновите регистрацию поставщика ресурсов `Microsoft.ContainerService` с помощью команды [az provider register](/cli/azure/provider#az-provider-register):

```azurecli
az provider register --namespace Microsoft.ContainerService
```  

### <a name="use-containerd-on-new-clusters-preview"></a>Использовать `containerd` в новых кластерах (Предварительная версия)

Настройте кластер для использования `containerd` при создании кластера. Используйте `--aks-custom-headers` флаг, чтобы задать в `containerd` качестве среды выполнения контейнера.

> [!NOTE]
> `containerd`Среда выполнения поддерживается только для узлов и пулов узлов с помощью образа AKS Ubuntu 18,04.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804,ContainerRuntime=containerd
```

Если вы хотите создавать кластеры с помощью среды выполнения значок Кита (DOCKER), это можно сделать, опустив пользовательский `--aks-custom-headers` тег.

### <a name="use-containerd-on-existing-clusters-preview"></a>Использование `containerd` в существующих кластерах (Предварительная версия)

Настройте новый пул узлов для использования `containerd` . Используйте `--aks-custom-headers` флаг, чтобы задать в `containerd` качестве среды выполнения для этого пула узлов.

```azurecli
az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804,ContainerRuntime=containerd
```

Если вы хотите создать пулы узлов с помощью среды выполнения значок Кита (DOCKER), это можно сделать, опустив пользовательский `--aks-custom-headers` тег.


### <a name="containerd-limitationsdifferences"></a>`Containerd` ограничения и различия

* Чтобы использовать в `containerd` качестве среды выполнения контейнера, в качестве базового образа ОС необходимо использовать AKS Ubuntu 18,04.
* Хотя набор инструментов DOCKER по-прежнему существует на узлах, Kubernetes использует в `containerd` качестве среды выполнения контейнера. Таким образом, поскольку значок Кита/DOCKER не управляет контейнерами, созданными Kubernetes на узлах, вы не можете просматривать контейнеры и взаимодействовать с ними с помощью команд DOCKER (например `docker ps` ,) или API DOCKER.
* Для для `containerd` [`crictl`](https://kubernetes.io/docs/tasks/debug-application-cluster/crictl) **устранения неполадок** в Pod, контейнерах и образах контейнеров на узлах Kubernetes рекомендуется использовать в качестве замещающего интерфейса командной строки вместо DOCKER CLI (например, `crictl ps` ). 
   * Он не предоставляет полный набор функций интерфейса командной строки DOCKER. Он предназначен только для устранения неполадок.
   * `crictl` предлагает более kubernetes представление контейнеров с такими концепциями, как модули Pod и т. д.
* `Containerd` Настраивает ведение журнала с использованием стандартизированного `cri` формата ведения журнала (который отличается от используемого в настоящее время драйвера JSON DOCKER). Ваше решение для ведения журнала должно поддерживать `cri` Формат ведения журнала (например, [Azure Monitor для контейнеров](../azure-monitor/insights/container-insights-enable-new-cluster.md)).
* Вы больше не можете получить доступ к подсистеме DOCKER `/var/run/docker.sock` или использовать DOCKER-in-DOCKER (динд).
  * Если в данный момент вы извлечете журналы приложений или данные мониторинга из подсистемы DOCKER, используйте вместо них нечто вроде [Azure Monitor для контейнеров](../azure-monitor/insights/container-insights-enable-new-cluster.md) . Кроме того, AKS не поддерживает выполнение команд с использованием аппаратного контроллера управления на узлах агента, которые могут привести к нестабильной работе.
  * Даже при использовании значок Кита/DOCKER создание образов и непосредственное использование подсистемы DOCKER с помощью приведенных выше методов настоятельно не рекомендуется. Kubernetes не полностью осведомлен о потребляемых ресурсах, и эти подходы представляют множество проблем, описанных [здесь](https://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/) , и [здесь](https://securityboulevard.com/2018/05/escaping-the-whale-things-you-probably-shouldnt-do-with-docker-part-1/), например.
* Создание образов. Вы можете продолжать использовать текущий рабочий процесс DOCKER в качестве обычного, если вы не создаете имагажес в кластере AKS. В этом случае рассмотрите возможность перехода на рекомендуемый подход к созданию образов с помощью [задач контроля](../container-registry/container-registry-quickstart-task-cli.md)доступа или более безопасного варианта в кластере, такого как [DOCKER буилдкс](https://github.com/docker/buildx).

## <a name="generation-2-virtual-machines-preview"></a>Виртуальные машины поколения 2 (Предварительная версия)

Azure поддерживает [виртуальные машины версии 2 (Gen2)](../virtual-machines/generation-2.md). Виртуальные машины поколения 2 поддерживают ключевые функции, которые не поддерживаются в виртуальных машинах поколения 1 (GEN1). Эти функции включают увеличенную память, Intel Software Guard Extensions и виртуализированную постоянную память (vPMEM).

Виртуальные машины 2-го поколения используют новую архитектуру загрузки на основе UEFI, а не архитектуру на основе BIOS, используемую виртуальными машинами 1-го поколения.
Виртуальные машины Gen2 поддерживают только определенные номера SKU и размеры. Проверьте [список поддерживаемых размеров](../virtual-machines/generation-2.md#generation-2-vm-sizes), чтобы узнать, поддерживает ли SKU или требует Gen2.

Кроме того, не все образы виртуальных машин поддерживают Gen2, на виртуальных машинах AKS Gen2 будет использоваться новый [образ AKS Ubuntu 18,04](#os-configuration). Этот образ поддерживает все номера SKU и размеры Gen2.

Чтобы использовать виртуальные машины Gen2 в режиме предварительной версии, вам потребуется:
- `aks-preview`Установленное расширение CLI.
- `Gen2VMPreview`Зарегистрированный флаг функции.

Зарегистрируйте компонент `Gen2VMPreview`:

```azurecli
az feature register --name Gen2VMPreview --namespace Microsoft.ContainerService
```

Состояние **Registered** (Зарегистрировано) может появиться через несколько минут. Состояние регистрации можно проверить с помощью команды [az feature list](/cli/azure/feature#az-feature-list):

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/Gen2VMPreview')].{Name:name,State:properties.state}"
```

Когда отобразится правильный статус, обновите регистрацию поставщика ресурсов `Microsoft.ContainerService` с помощью команды [az provider register](/cli/azure/provider#az-provider-register):

```azurecli
az provider register --namespace Microsoft.ContainerService
```

Чтобы установить расширение CLI AKS-Preview, используйте следующие Azure CLI команды:

```azurecli
az extension add --name aks-preview
```

Чтобы обновить расширение aks-preview, выполните следующие команды Azure CLI:

```azurecli
az extension update --name aks-preview
```

### <a name="use-gen2-vms-on-new-clusters-preview"></a>Использование виртуальных машин Gen2 в новых кластерах (Предварительная версия)
Настройте кластер для использования виртуальных машин Gen2 для выбранного SKU при создании кластера. Используйте `--aks-custom-headers` флаг, чтобы задать Gen2 в качестве создания виртуальной машины в новом кластере.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_D2s_v3 --aks-custom-headers usegen2vm=true
```

Если вы хотите создать обычный кластер с помощью виртуальных машин поколения 1 (GEN1), это можно сделать, опустив пользовательский `--aks-custom-headers` тег. Вы также можете добавить виртуальные машины Gen1 или Gen2 в соответствии с разрядом.

### <a name="use-gen2-vms-on-existing-clusters-preview"></a>Использование виртуальных машин Gen2 в существующих кластерах (Предварительная версия)
Настройте новый пул узлов для использования виртуальных машин Gen2. Используйте `--aks-custom-headers` флаг, чтобы задать Gen2 в качестве создания виртуальной машины для этого пула узлов.

```azurecli
az aks nodepool add --name gen2 --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_D2s_v3 --aks-custom-headers usegen2vm=true
```

Если вы хотите создать обычные пулы узлов Gen1, это можно сделать, опустив пользовательский `--aks-custom-headers` тег.


## <a name="ephemeral-os"></a>Эфемерная ОС

По умолчанию Azure автоматически реплицирует диск операционной системы для виртуальной машины в службу хранилища Azure, чтобы избежать потери данных, если необходимо переместить ВИРТУАЛЬную машину на другой узел. Однако, поскольку контейнеры не предназначены для сохранения локального состояния, это поведение предлагает ограниченное значение, предоставляя некоторые недостатки, включая более медленную подготовку узлов и более высокую задержку при чтении и записи.

Напротив, временные диски ОС хранятся только на главном компьютере, как и временный диск. Это обеспечивает меньшую задержку при чтении и записи, а также ускоряет масштабирование узлов и обновление кластера.

Как и временный диск, диск временного ОС включается в цену виртуальной машины, поэтому дополнительные затраты на хранение не взимается.

> [!IMPORTANT]
>Когда пользователь явно не запрашивает управляемые диски для операционной системы, AKS по возможности по умолчанию использует временную ОС для данной конфигурации нодепул.

При использовании эфемерной ОС диск ОС должен находиться в кэше виртуальной машины. Размеры кэша виртуальных машин доступны в [документации по Azure](../virtual-machines/dv3-dsv3-series.md) в круглых скобках рядом с пропускной способностью ввода-вывода ("размер кэша в гиб").

Используя размер виртуальной машины AKS по умолчанию Standard_DS2_v2 с размером диска ОС по умолчанию (100 ГБ) в качестве примера, этот размер виртуальной машины поддерживает временную ОС, но имеет только 86GB размера кэша. Если пользователь не указал явно, эта конфигурация будет по умолчанию использовать управляемые диски. Если пользователь явно запросил временную ОС, он получит ошибку проверки.

Если пользователь запрашивает тот же Standard_DS2_v2 с диском ОС 60 ГБ, эта конфигурация будет по умолчанию иметь временную ОС: запрошенный размер 60 ГБ меньше, чем максимальный размер кэша 86GB.

При использовании Standard_D8s_v3 с диском операционной системы емкостью 100 ГБ этот размер виртуальной машины поддерживает временную ОС и имеет 200 ГБ пространства кэша. Если пользователь не указал тип диска ОС, нодепул по умолчанию получит временную ОС. 

Для эфемерной ОС требуется по крайней мере 2.15.0 версии Azure CLI.

### <a name="use-ephemeral-os-on-new-clusters"></a>Использование эфемерной ОС в новых кластерах

Настройте кластер для использования временных дисков ОС при создании кластера. Используйте `--node-osdisk-type` флаг, чтобы задать эфемерную ОС в качестве типа диска ОС для нового кластера.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --node-osdisk-type Ephemeral
```

Если вы хотите создать обычный кластер с подключенными к сети дисками ОС, это можно сделать, указав `--node-osdisk-type=Managed` . Вы также можете добавить дополнительные пулы временных узлов ОС, как описано ниже.

### <a name="use-ephemeral-os-on-existing-clusters"></a>Использование эфемерной ОС в существующих кластерах
Настройте новый пул узлов для использования временных дисков ОС. Используйте `--node-osdisk-type` флаг, чтобы задать в качестве типа диска ОС в качестве типа диска ОС для пула узлов.

```azurecli
az aks nodepool add --name ephemeral --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --node-osdisk-type Ephemeral
```

> [!IMPORTANT]
> При использовании эфемерной ОС можно развернуть образы виртуальных машин и экземпляров вплоть до размера кэша виртуальной машины. В случае с AKS в конфигурации диска с ОС узла по умолчанию используется 128 ГБ, что означает, что требуется размер виртуальной машины с кэшем, превышающим 128 ГБ. Standard_DS2_v2 по умолчанию имеет размер кэша 86GB, который недостаточно велик. Standard_DS3_v2 имеет размер кэша 172GB, который достаточно большой. Можно также уменьшить размер диска ОС по умолчанию с помощью `--node-osdisk-size` . Минимальный размер AKS изображений — 30 ГБ. 

Если вы хотите создать пулы узлов с дисками ОС, подключенными к сети, это можно сделать, указав `--node-osdisk-type Managed` .

## <a name="custom-resource-group-name"></a>Пользовательское имя группы ресурсов

При развертывании кластера Azure Kubernetes Service в Azure создается дополнительная группа ресурсов для рабочих узлов. По умолчанию AKS присваивает группе ресурсов для узлов имя `MC_resourcegroupname_clustername_location`. Но вы можете изменить его произвольным образом.

Чтобы указать собственное имя для группы ресурсов, установите для Azure CLI расширение aks-preview 0.3.2 или более поздней версии. В Azure CLI укажите параметр `--node-resource-group` при выполнении команды `az aks create`, чтобы указать пользовательское имя для группы ресурсов. Если вы применяете шаблон Azure Resource Manager для развертывания кластера AKS, группу ресурсов можно определить с помощью свойства `nodeResourceGroup`.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

Дополнительную группу ресурсов автоматически создает поставщик ресурсов Azure в вашей подписке. Вы можете указать пользовательское имя для группы ресурсов только при создании кластера. 

При работе с группой ресурсов для узлов учитывайте, что нельзя:

- указать существующую группу ресурсов для узлов;
- поместить узлы в группу ресурсов в другой подписке;
- изменить имя группы ресурсов узла после создания кластера;
- выбрать имена управляемых ресурсов в группе ресурсов для узлов;
- изменять или удалять созданные Azure теги управляемых ресурсов в группе ресурсов для узлов.

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте [, как обновить образы узлов](node-image-upgrade.md) в кластере.
- Сведения о том, как обновить кластер до последней версии Kubernetes, см. в статье [Обновление кластера Службы Azure Kubernetes (AKS)](upgrade-cluster.md).
- Ознакомьтесь с дополнительными сведениями о [ `containerd` и Kubernetes](https://kubernetes.io/blog/2018/05/24/kubernetes-containerd-integration-goes-ga/)
- В списке [вопросов и ответов об AKS](faq.md) собраны самые популярные рекомендации.
- Узнайте больше о [временных дисках ОС](../virtual-machines/ephemeral-os-disks.md).


<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
