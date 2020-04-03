---
title: Используйте несколько пулов узлов в службе Azure Kubernetes (AKS)
description: Узнайте, как создать и управлять несколькими пулями узлов для кластера в службе Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: 87f066ed17e5274439082956803d269bdd5853f5
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80616502"
---
# <a name="create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Создание и управление несколькими пулями узлов для кластера в службе Azure Kubernetes (AKS)

В службе Azure Kubernetes (AKS) узлы одной и той же конфигурации сгруппированы в *пулы узлов.* Эти пулы узлов содержат базовые VMs, которые управляют приложениями. Начальное число узлов и их размер (SKU) определяется при создании кластера AKS, который создает *пул узлов по умолчанию.* Для поддержки приложений с различными требованиями к вычислениям или хранению можно создать дополнительные пулы узлов. Например, используйте дополнительные пулы узлов для предоставления графических процессоров для вычислительно-интенсивных приложений или доступа к высокопроизводительному хранению SSD.

> [!NOTE]
> Эта функция позволяет более высоко контролировать способы создания и управления несколькими пулями узлов. В результате для создания/обновления/удаления требуются отдельные команды. Ранее операции `az aks create` кластера через или `az aks update` использовали управляемый Кластер API и были единственным вариантом для изменения плоскости управления и одного пула узлов. Эта функция предоставляет отдельный набор операций для пулов агентов через `az aks nodepool` API agentPool и требует использования набора команд для выполнения операций в отдельном пуле узлов.

В этой статье показано, как создавать и управлять несколькими пулями узлов в кластере AKS.

## <a name="before-you-begin"></a>Перед началом

Вам нужна версия Azure CLI 2.2.0 или более поздняя установка и настройка. Чтобы узнать версию, выполните команду `az --version`. Если вам нужно установить или обновить, [см.][install-azure-cli]

## <a name="limitations"></a>Ограничения

При создании и управлении кластерами AKS, поддерживающих несколько пулов узлов, применяются следующие ограничения:

* См [Квоты, ограничения размера виртуальных машин и доступность региона в службе Azure Kubernetes (AKS)][quotas-skus-regions].
* Вы не можете удалить пул узлов системы, по умолчанию пул первого узла.
* Кластер AKS должен использовать балансиватор нагрузки Standard SKU для использования нескольких пулов узлов, функция не поддерживается балансилерами нагрузки Basic SKU.
* Кластер AKS должен использовать виртуальные наборы масштабов машин для узлов.
* Название пула узлов может содержать только буквенные символы нижнего регистра и должно начинаться с буквы нижнего регистра. Для пулов узлов Linux длина должна быть от 1 до 12 символов, для пулов узлов Windows длина должна быть от 1 до 6 символов.
* Все пулы узлов должны находиться в одной виртуальной сети.
* При создании нескольких пулов узлов во время создания кластера все версии Kubernetes, используемые пулами узлов, должны соответствовать набору версий для плоскости управления. Это может быть обновлено после того, как кластер был подготовлен с помощью операций пула для узла.

## <a name="create-an-aks-cluster"></a>Создание кластера AKS

Для начала создайте кластер AKS с единым пулом узлов. В следующем примере используется команда [создания группы az][az-group-create] для создания группы ресурсов под названием *myResourceGroup* в регионе *востока.* Кластер AKS под названием *myAKSCluster* создается с помощью команды [az aks create.][az-aks-create] A *--kubernetes-версия* *1.15.7* используется для показа того, как обновить пул узлов на следующем этапе. Вы можете указать любую [поддерживаемую версию Kubernetes][supported-versions].

> [!NOTE]
> *Балансизатор основной* нагрузки SKU **не поддерживается** при использовании нескольких пулов узлов. По умолчанию кластеры AKS создаются с помощью sKU балансовых *стандартных* нагрузок sKU с портала Azure CLI и Azure.

```azurecli-interactive
# Create a resource group in East US
az group create --name myResourceGroup --location eastus

# Create a basic single-node AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 2 \
    --generate-ssh-keys \
    --kubernetes-version 1.15.7 \
    --load-balancer-sku standard
```

Создание кластера занимает несколько минут.

> [!NOTE]
> Чтобы обеспечить надежное функционирование кластера, необходимо запустить не менее 2 (двух) узлов в пуле узлов по умолчанию, так как основные системные службы работают через этот пул узлов.

Когда кластер будет готов, используйте команду [az aks get-credentials,][az-aks-get-credentials] `kubectl`чтобы получить учетные данные кластера для использования с:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>Добавить пул узлов

Кластер, созданный на предыдущем этапе, имеет единый пул узлов. Давайте добавим второй пул узлов, используя команду [az aks nodepool.][az-aks-nodepool-add] Следующий пример создает пул узлов под названием *mynodepool,* который работает *3* узла:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --kubernetes-version 1.15.5
```

> [!NOTE]
> Название пула узлов должно начинаться с буквы нижнего регистра и может содержать только буквенно-цифровые символы. Для пулов узлов Linux длина должна быть от 1 до 12 символов, для пулов узлов Windows длина должна быть от 1 до 6 символов.

Чтобы увидеть состояние пулов узлов, используйте команду [пула пула аз-аксов][az-aks-nodepool-list] и укажите группу ресурсов и имя кластера:

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster
```

Следующий вывод показывает, что *mynodepool* был успешно создан с тремя узлами в пуле узлов. Когда кластер AKS был создан на предыдущем этапе, был создан *узло* по умолчанию с числом узлов *2*.

```output
[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.5",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

> [!TIP]
> Если при добавлении пула узлов *VmSize* не указывается, размер по умолчанию *Standard_DS2_v3* для пулов узлов Windows и *Standard_DS2_v2* для пулов узлов Linux. Если не указано *OrchestratorVersion,* он по умолчанию по умолчанию по той же версии, что и плоскость управления.

### <a name="add-a-node-pool-with-a-unique-subnet-preview"></a>Добавьте пул узлов с уникальной подсетью (предварительный просмотр)

Рабочая нагрузка может потребовать разделения узлов кластера на отдельные пулы для логической изоляции. Эта изоляция может быть поддержана отдельными подсетями, предназначенными для каждого пула узлов в кластере. Это может решить такие требования, как наличие несъемного виртуального сетевого адресного пространства для разделения на пулы узлов.

#### <a name="limitations"></a>Ограничения

* Все подсети, назначенные узлам, должны принадлежать одной и той же виртуальной сети.
* Системные стручки должны иметь доступ ко всем узлам кластера, чтобы обеспечить критически важные функциональные возможности, такие как разрешение DNS через coreDNS.
* Назначение уникальной подсети на пул узлов ограничено Azure CNI во время предварительного просмотра.
* Использование сетевых политик с уникальной подсетью на пул узлов не поддерживается во время предварительного просмотра.

Чтобы создать пул узлов со выделенной подсетью, передайте идентификатор ресурса подсети в качестве дополнительного параметра при создании пула узлов.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --kubernetes-version 1.15.5
    --vnet-subnet-id <YOUR_SUBNET_RESOURCE_ID>
```

## <a name="upgrade-a-node-pool"></a>Обновление пула узлов

> [!NOTE]
> Операции обновления и масштабирования в пуле кластера или узла не могут происходить одновременно, если попытка возврата ошибки. Вместо этого каждый тип операции должен быть завершен на целевом ресурсе до следующего запроса на том же ресурсе. Узнайте больше об этом на нашем [руководстве по устранению неполадок](https://aka.ms/aks-pending-upgrade).

Когда кластер AKS был первоначально создан на `--kubernetes-version` первом этапе, был указан *1.15.7.* Это установило версию Kubernetes как для плоскости управления, так и для пула узлов по умолчанию. Команды в этом разделе объясняют, как обновить один определенный пул узлов.

Взаимосвязь между обновлением Kubernetes версия плоскости управления и пул узлов объясняются в [разделе ниже](#upgrade-a-cluster-control-plane-with-multiple-node-pools).

> [!NOTE]
> Версия изображения пула пула Os привязана к версии кластера Kubernetes. Вы получите только обновления изображений ОС после обновления кластера.

Поскольку в этом примере есть два пула узлов, мы должны использовать [обновление узлового узла az aks][az-aks-nodepool-upgrade] для обновления пула узлов. Давайте обновить *mynodepool* в Kubernetes *1.15.7*. Используйте команду [обновления az aks nodepool][az-aks-nodepool-upgrade] для обновления пула узлов, как показано в следующем примере:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version 1.15.7 \
    --no-wait
```

Перечислите состояние пулов узлов снова, используя команду [списка пулов az aks.][az-aks-nodepool-list] Следующий пример показывает, что *mynodepool* находится в состоянии *обновления* до *1.15.7*:

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Upgrading",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Обновление узлов до указанной версии занимает несколько минут.

В качестве наилучшей практики следует обновить все пулы узлов в кластере AKS до той же версии Kubernetes. Поведение по `az aks upgrade` умолчанию заключается в обновлении всех пулов узлов вместе с плоскостью управления для достижения этого выравнивания. Возможность обновления отдельных пулов узлов позволяет выполнять скользящее обновление и планировать стручки между пулами узлов для поддержания времени простоя приложения в пределах вышеуказанных ограничений.

## <a name="upgrade-a-cluster-control-plane-with-multiple-node-pools"></a>Обновление плоскости управления кластером с несколькими пулами узлов

> [!NOTE]
> Kubernetes использует стандартную схему версий [Semantic Versioning.](https://semver.org/) Номер версии выражается как *x.y.z*, где *x* является основной версией, *у* является незначительной версией, и *z* является патч-версия. Например, в *версии 1.12.6*, 1 является основной версией, 12 является второстепенной версией, а 6 - патч-версией. Версия kubernetes плоскости управления и исходный пул узлов устанавливаются во время создания кластера. Все дополнительные пулы узлов имеют набор своих версий Kubernetes при добавлении в кластер. Версии Kubernetes могут отличаться между пулами узлов, а также между пулом узлов и плоскостью управления.

Кластер AKS имеет два кластерных ресурсных объекта с связанными версиями Kubernetes.

1. Группа управления самолетом Kubernetes версии.
2. Бассейн узлов с версией Kubernetes.

Карты плоскости управления одним или многими пулами узлов. Поведение операции обновления зависит от того, какая команда Azure CLI используется.

Модернизация плоскости управления `az aks upgrade`AKS требует использования. Эта команда обновляет версию плоскости управления и все пулы узлов в кластере.

Выдача `az aks upgrade` команды `--control-plane-only` с флагом обновляет только плоскость управления кластером. Ни один из связанных пулов узлов в кластере не изменяется.

Обновление отдельных пулов `az aks nodepool upgrade`узлов требует использования. Эта команда обновляет только пул целевых узлов с указанной версией Kubernetes

### <a name="validation-rules-for-upgrades"></a>Правила проверки обновлений

Действительные обновления Kubernetes для плоскости управления кластера и пулов узлов проверяются следующими наборами правил.

* Правила для действительных версий для обновления узловбассейнов:
   * Версия пула узлов должна иметь ту же *основную* версию, что и плоскость управления.
   * *Незначительная* версия пула узлов должна находиться в двух *незначительных* версиях версии самолета управления.
   * Версия пула узлов не может `major.minor.patch` быть больше, чем версия управления.

* Правила отправки операции обновления:
   * Вы не можете понизить плоскость управления или пул узлов Kubernetes версии.
   * Если версия пула узла Kubernetes не указана, поведение зависит от используемого клиента. Декларация в шаблонах «Менеджер ресурсов» возвращается к существующей версии, определенной для пула узлов, если она не установлена, то версия плоскости управления используется для отступления.
   * Вы можете обновить или масштабировать плоскость управления или пул узлов в данный момент времени, вы не можете представить несколько операций на одном плоскости управления или ресурс пула узлов одновременно.

## <a name="scale-a-node-pool-manually"></a>Масштабирование пула узлов вручную

По мере необходимости изменения рабочей нагрузки приложения может потребоваться масштабирование числа узлов в пуле узлов. Количество узлов можно увеличить или опустить вверх или вниз.

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

Для масштабирования количества узлов в пуле узлов используйте команду масштаба пула az [aks.][az-aks-nodepool-scale] Следующий пример масштабирует количество узлов в *mynodepool* до *5:*

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

Перечислите состояние пулов узлов снова, используя команду [списка пулов az aks.][az-aks-nodepool-list] Следующий пример показывает, что *mynodepool* находится в *состоянии масштабирования* с новым количеством *5* узлов:

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Scaling",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Операция по масштабу занимает несколько минут.

## <a name="scale-a-specific-node-pool-automatically-by-enabling-the-cluster-autoscaler"></a>Масштабирование определенного пула узлов автоматически, включив кластерный автоскалатор

AKS предлагает отдельную функцию для автоматического масштабирования пулов узлов с функцией, называемой [кластерным автоскейлом.](cluster-autoscaler.md) Эта функция может быть включена в пул узлов с уникальным минимальным и максимальным количеством веса на пул узлов. Узнайте, как [использовать кластерный автоскейл по пулу узлов.](cluster-autoscaler.md#use-the-cluster-autoscaler-with-multiple-node-pools-enabled)

## <a name="delete-a-node-pool"></a>Удалить пул узлов

Если вам больше не нужен пул, вы можете удалить его и удалить основные узлы VM. Чтобы удалить пул узлов, используйте команду [удаления пула узлов az aks][az-aks-nodepool-delete] и укажите имя пула узлов. Следующий пример удаляет *mynoodepool,* созданный в предыдущих шагах:

> [!CAUTION]
> При удалении пула узлов нет никаких вариантов восстановления данных. Если стручки не могут быть запланированы на других пулах узлов, эти приложения недоступны. Убедитесь, что пул узлов не удаляет, когда в приложениях нет резервных копий данных или возможности запуска на других пулах узлов в кластере.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

Следующий пример вывода из команды [пула пула az aks][az-aks-nodepool-list] показывает, что *mynodepool* находится в состоянии *деления:*

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Deleting",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Удаление узлов и пула узлов занимает несколько минут.

## <a name="specify-a-vm-size-for-a-node-pool"></a>Укажите размер VM для пула узлов

В предыдущих примерах для создания пула узлов для узлов, созданных в кластере, использовался размер VM по умолчанию. Более распространенным сценарием является создание пулов узлов с различными размерами и возможностями VM. Например, можно создать пул узлов, содержащий узлы с большим количеством процессора или памяти, или пул узлов, обеспечивающий поддержку графического процессора. На следующем этапе вы [используете пятна и терпимости,](#schedule-pods-using-taints-and-tolerations) чтобы рассказать планировщику Kubernetes, как ограничить доступ к стручкам, которые могут работать на этих узлах.

В следующем примере создайте пул узлов на основе графического пула, который использует *Standard_NC6* размер VM. Эти VMs оснащены картой NVIDIA Tesla K80. Для получения информации о доступных размерах VM [см.][vm-sizes]

Создайте пул узлов, используя [пул узлов az aks, снова добавить][az-aks-nodepool-add] команду. На этот раз укажите имя *gpunodepool*и используйте `--node-vm-size` параметр, чтобы указать размер *Standard_NC6:*

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

Следующий пример вывода из команды [пула пула az aks][az-aks-nodepool-list] показывает, что *gpunodepool* *создает* узлы с указанным *VmSize:*

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 1,
    ...
    "name": "gpunodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "vmSize": "Standard_NC6",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Это займет несколько минут для *gpunodepool,* чтобы быть успешно создан.

## <a name="schedule-pods-using-taints-and-tolerations"></a>Расписание стручки с использованием загрязнений и терпимости

Теперь в кластере есть два пула узлов : изначально созданный пул узлов по умолчанию и пул узлов на основе gPU. Используйте команду [kubectl получить узлы][kubectl-get] для просмотра узлов в кластере. Следующий пример вывода показывает узлы:

```console
kubectl get nodes
```

```output
NAME                                 STATUS   ROLES   AGE     VERSION
aks-gpunodepool-28993262-vmss000000  Ready    agent   4m22s   v1.15.7
aks-nodepool1-28993262-vmss000000    Ready    agent   115m    v1.15.7
```

Для ограничения рабочих нагрузок, которые могут выполняться на узлах, в планировщике Kubernetes используются параметры taint (отметка) и toleration (толерантность).

* **Отметка** применяется к узлу, указывая, что на него могут назначаться только определенные модули pod.
* Затем к модулям pod применяется параметр **toleration**, указывающий, что они *допускают* метку узла.

Для получения дополнительной информации о том, как использовать расширенные функции Kubernetes, смотрите [лучшие практики для расширенных функций планировщика в AKS][taints-tolerations]

В этом примере примените запятнание к узлам на основе графического процессора, используя команду --узла-запятнать. Укажите имя узла на основе графического процессора `kubectl get nodes` из вывода предыдущей команды. Запятнать применяется в качестве пары *ключей,* а затем пара планирования. В следующем примере используется пара *sku'gpu* и определяет стручки в противном случае имеют способность *NoSchedule:*

```console
az aks nodepool add --node-taints aks-gpunodepool-28993262-vmss000000 sku=gpu:NoSchedule
```

Следующий основной пример YAML манифест использует терпимость, чтобы позволить планировщик Kubernetes для запуска NGINX стручок на GPU основе узла. Для более подходящего, но трудоемкого примера для выполнения заданий Tensorflow с набором данных MNIST [см.][gpu-cluster]

Создайте файл `gpu-toleration.yaml` и скопируйте в него следующий пример кода YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: nginx:1.15.9
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 1
        memory: 2G
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

Расписание стручка `kubectl apply -f gpu-toleration.yaml` с помощью команды:

```console
kubectl apply -f gpu-toleration.yaml
```

Это займет несколько секунд, чтобы запланировать стручок и вытащить изображение NGINX. Используйте [команду kubectl для][kubectl-describe] просмотра состояния стручка. Следующий сжатый пример вывода показывает *sku'gpu: NoSchedule* толерантность применяется. В разделе событий планировщик присвоил стручок *акс-гпунодпул-28993262-vmss0000000* GPU на основе узла:

```console
kubectl describe pod mypod
```

```output
[...]
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
                 sku=gpu:NoSchedule
Events:
  Type    Reason     Age    From                                          Message
  ----    ------     ----   ----                                          -------
  Normal  Scheduled  4m48s  default-scheduler                             Successfully assigned default/mypod to aks-gpunodepool-28993262-vmss000000
  Normal  Pulling    4m47s  kubelet, aks-gpunodepool-28993262-vmss000000  pulling image "nginx:1.15.9"
  Normal  Pulled     4m43s  kubelet, aks-gpunodepool-28993262-vmss000000  Successfully pulled image "nginx:1.15.9"
  Normal  Created    4m40s  kubelet, aks-gpunodepool-28993262-vmss000000  Created container
  Normal  Started    4m40s  kubelet, aks-gpunodepool-28993262-vmss000000  Started container
```

Только стручки, которые имеют эту терпимость может быть запланировано на узлах в *gpunodepool*. Любой другой стручок будет запланирован в пуле узла *nodepool1.* При создании дополнительных пулов узлов можно использовать дополнительные загрязнения и терпимости, чтобы ограничить, какие стручки могут быть запланированы на этих ресурсах узлов.

## <a name="specify-a-taint-label-or-tag-for-a-node-pool"></a>Указать запятнать, метку или тег для пула узлов

При создании пула узлов можно добавить в пул узлов пятна. При добавлении taint, label или тега все узлы в пуле узлов также получают эту запятнание, метку или тег.

Чтобы создать пул узлов с запятнанной, используйте [az aks nodepool добавить.][az-aks-nodepool-add] Укажите имя *taintnp* `--node-taints` и используйте параметр, чтобы указать *sku'gpu:NoSchedule* для taint.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name taintnp \
    --node-count 1 \
    --node-taints sku=gpu:NoSchedule \
    --no-wait
```

Следующий пример вывода из команды [списка az aks nodepool][az-aks-nodepool-list] показывает, что *taintnp* *создает* узлы с указанными *узлами Taints:*

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "taintnp",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "nodeTaints":  {
      "sku": "gpu:NoSchedule"
    },
    ...
  },
 ...
]
```

Информация о загрязнении видна в Kubernetes для обработки правил планирования узлов.

Вы также можете добавить метки в пул узлов во время создания пула узлов. Этикетки, установленные в пуле узлов, добавляются к каждому узлам в пуле узлов. Эти [метки видны в Kubernetes][kubernetes-labels] для обработки правил планирования узлов.

Чтобы создать пул узлов с меткой, используйте [az aks nodepool.][az-aks-nodepool-add] Укажите имя *labelnp* `--labels` и используйте параметр для указания *dept-IT* и *costcenter-9999* для меток.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name labelnp \
    --node-count 1 \
    --labels dept=IT costcenter=9999 \
    --no-wait
```

> [!NOTE]
> Метка может быть установлена только для пулов узлов во время создания пула узлов. Этикетки также должны быть парой ключей/стоимости и иметь [действительный синтаксис.][kubernetes-label-syntax]

Следующий пример вывода из команды [списка az aks nodepool][az-aks-nodepool-list] показывает, что *labelnp* *создает* узлы с указанными *обозначенными обозначениями:*

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "labelnp",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "nodeLabels":  {
      "dept": "IT",
      "costcenter": "9999"
    },
    ...
  },
 ...
]
```

Тег Azure можно применить к пулам узлов в кластере AKS. Теги, применяемые к пулу узлов, применяются к каждому узлам в пуле узлов и сохраняются за счет обновлений. Теги также применяются к новым узлам, добавленным в пул узлов во время операций масштабирования. Добавление тега может помочь с такими задачами, как отслеживание политики или оценка затрат.

Создайте пул узлов, используя [az aks nodepool.][az-aks-nodepool-add] Укажите имя *tagnodepool* `--tag` и используйте параметр для указания *dept-IT* и *costcenter-9999* для тегов.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name tagnodepool \
    --node-count 1 \
    --tags dept=IT costcenter=9999 \
    --no-wait
```

> [!NOTE]
> Вы также можете `--tags` использовать параметр при использовании команды [обновления az aks nodepool,][az-aks-nodepool-update] а также при создании кластера. Во время создания `--tags` кластера параметр применяет тег к исходной пулу узлов, созданного с кластером. Все имена тегов должны соблюдать ограничения в [тегах Use для организации ресурсов Azure.][tag-limitation] Обновление пула узлов с `--tags` параметром обновляет все существующие значения тегов и придаткивает любые новые теги. Например, если в пуле узлов был *dept-IT* и *costcenter-9999* для тегов, и вы обновили его с *помощью team'dev* и *costcenter-111* для тегов, у вас узла будет *dept-IT,* *costcenter-111*, и *team'dev* для тегов.

Следующий пример вывода из команды [списка az aks nodepool][az-aks-nodepool-list] показывает, что *tagnodepool* *создает* узлы с указанным *тегом:*

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 1,
    ...
    "name": "tagnodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "tags": {
      "dept": "IT",
      "costcenter": "9999"
    },
    ...
  },
 ...
]
```

## <a name="manage-node-pools-using-a-resource-manager-template"></a>Управление пулами узлов с помощью шаблона «Менеджер ресурсов»

При использовании шаблона Azure Resource Manager для создания и управления ресурсами обычно можно обновить настройки в шаблоне и передислоцировать сярты для обновления ресурса. С пулами узлов в AKS исходный профиль пула узлов не может быть обновлен после создания кластера AKS. Это поведение означает, что нельзя обновить существующий шаблон управления ресурсами, внести изменения в пулы узлов и передислоцировать. Вместо этого необходимо создать отдельный шаблон управления ресурсами, который обновляет только пулы узлов для существующего кластера AKS.

Создайте шаблон, `aks-agentpools.json` например, и вставьте следующий пример манифеста. Этот пример шаблона настраивает следующие настройки:

* Обновляет пул узлов *Linux* под названием *myagentpool* для запуска трех узлов.
* Устанавливает узлы в пуле узлов для запуска kubernetes версии *1.15.7*.
* Определяет размер узла как *Standard_DS2_v2.*

Отспособите эти значения по мере необходимости обновлять, добавлять или удалять пулы узлов по мере необходимости:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "clusterName": {
            "type": "string",
            "metadata": {
                "description": "The name of your existing AKS cluster."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "The location of your existing AKS cluster."
            }
        },
        "agentPoolName": {
            "type": "string",
            "defaultValue": "myagentpool",
            "metadata": {
                "description": "The name of the agent pool to create or update."
            }
        },
        "vnetSubnetId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The Vnet subnet resource ID for your existing AKS cluster."
            }
        }
    },
    "variables": {
        "apiVersion": {
            "aks": "2020-01-01"
        },
        "agentPoolProfiles": {
            "maxPods": 30,
            "osDiskSizeGB": 0,
            "agentCount": 3,
            "agentVmSize": "Standard_DS2_v2",
            "osType": "Linux",
            "vnetSubnetId": "[parameters('vnetSubnetId')]"
        }
    },
    "resources": [
        {
            "apiVersion": "2020-01-01",
            "type": "Microsoft.ContainerService/managedClusters/agentPools",
            "name": "[concat(parameters('clusterName'),'/', parameters('agentPoolName'))]",
            "location": "[parameters('location')]",
            "properties": {
                "maxPods": "[variables('agentPoolProfiles').maxPods]",
                "osDiskSizeGB": "[variables('agentPoolProfiles').osDiskSizeGB]",
                "count": "[variables('agentPoolProfiles').agentCount]",
                "vmSize": "[variables('agentPoolProfiles').agentVmSize]",
                "osType": "[variables('agentPoolProfiles').osType]",
                "storageProfile": "ManagedDisks",
                "type": "VirtualMachineScaleSets",
                "vnetSubnetID": "[variables('agentPoolProfiles').vnetSubnetId]",
                "orchestratorVersion": "1.15.7"
            }
        }
    ]
}
```

Развертывание этого шаблона с помощью [развертывания группы аз создать][az-group-deployment-create] команду, как показано в следующем примере. Вам предлагается назвать существующее название и местоположение кластера AKS:

```azurecli-interactive
az group deployment create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

> [!TIP]
> Вы можете добавить тег в пул узлов, добавив свойство *тега* в шаблоне, как показано в следующем примере.
> 
> ```json
> ...
> "resources": [
> {
>   ...
>   "properties": {
>     ...
>     "tags": {
>       "name1": "val1"
>     },
>     ...
>   }
> }
> ...
> ```

Обновление кластера AKS может занять несколько минут в зависимости от параметров пула узлов и операций, которые вы определяете в шаблоне «Менеджер ресурсов».

## <a name="assign-a-public-ip-per-node-for-a-node-pool-preview"></a>Назначить общедоступный IP-адрес для пула узлов (предварительный просмотр)

> [!WARNING]
> Во время предварительного просмотра назначения публичного IP на узла, он не может быть использован со *стандартной нагрузкой балансоровой SKU в AKS* из-за возможных правил балансоровой нагрузки, противоречащих подготовке VM. В результате этого ограничения пулы агентов Windows не поддерживаются этой функцией предварительного просмотра. В ходе предварительного просмотра необходимо использовать *SKU Basic Load Balancer,* если вам необходимо назначить общедоступный IP-адрес на узла.

Узлы AKS не требуют собственных общедоступных IP-адресов для связи. Однако для сценариев может потребоваться, чтобы узлы в пуле узлов получали свои собственные выделенные общедоступные IP-адреса. Распространенным сценарием является для игровых рабочих нагрузок, где консоль должна сделать прямое подключение к облачной виртуальной машине, чтобы свести к минимуму хмель. Этот сценарий может быть достигнут на AKS путем регистрации для функции предварительного просмотра, Узлы Public IP (предварительный просмотр).

Зарегистрируйтесь для функции IP-адреса Узловой общественности, выпустив следующую команду Azure CLI.

```azurecli-interactive
az feature register --name NodePublicIPPreview --namespace Microsoft.ContainerService
```

После успешной регистрации разместите шаблон Менеджера ресурсов Azure в `enableNodePublicIP` соответствии с теми же инструкциями, что и [выше,](#manage-node-pools-using-a-resource-manager-template) и добавьте свойство boolean в agentPoolProfiles. Установите значение, как `true` по умолчанию он устанавливается как `false` если бы не указаны. 

Это свойство является только создание меменее свойством и требует минимальной версии API 2019-06-01. Это может быть применено как к пулам узлов Linux и Windows.

## <a name="clean-up-resources"></a>Очистка ресурсов

В этой статье вы создали кластер AKS, включающий узлы на основе gPU. Чтобы уменьшить ненужные затраты, вы можете удалить *gpunodepool*, или весь кластер AKS.

Чтобы удалить пул узлов на основе gPU, используйте команду [удаления узлов az aks,][az-aks-nodepool-delete] как показано в следующем примере:

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

Чтобы удалить сам кластер, используйте команду [удаления группы az,][az-group-delete] чтобы удалить группу ресурсов AKS:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Следующие шаги

В этой статье вы узнали, как создавать и управлять несколькими пулями узлов в кластере AKS. Для получения дополнительной информации о том, как управлять стручками в пулах узлов, [см.][operator-best-practices-advanced-scheduler]

Для создания и использования пулов узлов контейнеров Windows Server [см.][aks-windows]

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/resource-group-using-tags.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/linux/sizes.md