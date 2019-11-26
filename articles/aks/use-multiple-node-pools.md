---
title: Использование пулов нескольких узлов в службе Kubernetes Azure (AKS)
description: Узнайте, как создать несколько пулов узлов для кластера в службе Kubernetes Azure (AKS) и управлять ими.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 08/9/2019
ms.author: mlearned
ms.openlocfilehash: c48bcab0a3d009b186832a6b728597f03788a7cd
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382990"
---
# <a name="create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Создание нескольких пулов узлов для кластера в службе Kubernetes Azure (AKS) и управление ими

В службе Azure Kubernetes Service (AKS) узлы одной и той же конфигурации группируются в *Пулы узлов*. Эти пулы узлов содержат базовые виртуальные машины, на которых работают ваши приложения. Начальное число узлов и их размер (SKU) определяются при создании кластера AKS, который создает *пул узлов по умолчанию*. Для поддержки приложений, имеющих различные требования к вычислению или хранению, можно создать дополнительные пулы узлов. Например, используйте эти дополнительные пулы узлов, чтобы предоставить графические процессоры для приложений с большим объемом вычислений или доступ к высокопроизводительному хранилищу SSD.

> [!NOTE]
> Эта функция обеспечивает более высокий контроль над созданием нескольких пулов узлов и управлением ими. В результате для создания, обновления и удаления требуются отдельные команды. Ранее кластерные операции с помощью `az aks create` или `az aks update` использовали API Манажедклустер и были единственным вариантом изменения плоскости управления и пула с одним узлом. Эта функция предоставляет отдельный набор операций для пулов агентов через API Ажентпул и требует использования набора команд `az aks nodepool` для выполнения операций в пуле отдельных узлов.

В этой статье показано, как создать несколько пулов узлов и управлять ими в кластере AKS.

## <a name="before-you-begin"></a>Перед началом работы

Требуется Azure CLI версии 2.0.76 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][install-azure-cli].

## <a name="limitations"></a>Ограничения

При создании кластеров AKS, поддерживающих несколько пулов узлов, и управлении ими действуют следующие ограничения.

* Нельзя удалить пул узлов по умолчанию (First).
* Не удается использовать надстройку маршрутизации приложений HTTP.
* Кластер AKS должен использовать подсистему балансировки нагрузки уровня "Стандартный" для использования нескольких пулов узлов. Эта функция не поддерживается для подсистем балансировки нагрузки уровня "базовый".
* Кластер AKS должен использовать масштабируемые наборы виртуальных машин для узлов.
* Нельзя добавлять или удалять пулы узлов с помощью существующего шаблона диспетчер ресурсов, как в случае с большинством операций. Вместо этого [Используйте отдельный шаблон диспетчер ресурсов](#manage-node-pools-using-a-resource-manager-template) для внесения изменений в пулы узлов в кластере AKS.
* Имя пула узлов может содержать только буквы в нижнем регистре и должно начинаться с буквы в нижнем регистре. Для пулов узлов Linux длина должна составлять от 1 до 12 символов. для пулов узлов Windows длина должна составлять от 1 до 6 символов.
* Кластер AKS может иметь не более восьми пулов узлов.
* Кластер AKS может содержать не более 400 узлов в восьми пулах узлов.
* Все пулы узлов должны находиться в одной подсети.

## <a name="create-an-aks-cluster"></a>Создание кластера AKS

Чтобы приступить к работе, создайте кластер AKS с одним пулом узлов. В следующем примере используется команда [AZ Group Create][az-group-create] для создания группы ресурсов с именем *myResourceGroup* в регионе *eastus* . После этого кластер AKS с именем *myAKSCluster* создается с помощью команды [AZ AKS Create][az-aks-create] . A *--kubernetes-версия* *1.13.10* используется для демонстрации обновления пула узлов на следующем шаге. Можно указать любую [поддерживаемую версию Kubernetes][supported-versions].

> [!NOTE]
> Номер SKU *базовой* подсистемы балансировки нагрузки **не поддерживается** при использовании нескольких пулов узлов. По умолчанию кластеры AKS создаются с номером SKU *стандартной* подсистемы балансировки нагрузки Azure CLI и портал Azure.

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
    --kubernetes-version 1.13.10 \
    --load-balancer-sku standard
```

Создание кластера занимает несколько минут.

> [!NOTE]
> Чтобы обеспечить надежное функционирование кластера, необходимо запустить по крайней мере 2 узла в пуле узлов по умолчанию, так как базовые системные службы работают в этом пуле узлов.

Когда кластер будет готов, используйте команду [AZ AKS Get-Credential][az-aks-get-credentials] , чтобы получить учетные данные кластера для использования с `kubectl`.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>Добавление пула узлов

Кластер, созданный на предыдущем шаге, имеет один пул узлов. Добавим пул второго узла с помощью команды [AZ AKS нодепул Add][az-aks-nodepool-add] . В следующем примере создается пул узлов с именем *минодепул* , который запускает *3* узла:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --kubernetes-version 1.12.7
```

> [!NOTE]
> Имя пула узлов должно начинаться с буквы в нижнем регистре и может содержать только буквенно-цифровые символы. Для пулов узлов Linux длина должна составлять от 1 до 12 символов. для пулов узлов Windows длина должна составлять от 1 до 6 символов.

Чтобы просмотреть состояние пулов узлов, используйте команду [AZ AKS node Pool List][az-aks-nodepool-list] и укажите группу ресурсов и имя кластера:

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster
```

В следующем примере выходных данных показано, что *минодепул* успешно создан с тремя узлами в пуле узлов. При создании кластера AKS на предыдущем шаге создается *nodepool1* по умолчанию с числом узлов, равным *2*.

```console
$ az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.12.7",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.13.10",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

> [!TIP]
> Если *орчестраторверсион* или *VmSize* не указаны при добавлении пула узлов, узлы создаются на основе значений по умолчанию для кластера AKS. В этом примере это была Kubernetes версия *1.13.10* и размер узла *Standard_DS2_v2*.

## <a name="upgrade-a-node-pool"></a>Обновление пула узлов
 
> [!NOTE]
> Операции обновления и масштабирования в кластере или пуле узлов не могут выполняться одновременно, если была возвращена ошибка. Вместо этого каждый тип операции должен быть завершен в целевом ресурсе до следующего запроса к этому же ресурсу. Дополнительные сведения см. в нашем [руководство по устранению неполадок](https://aka.ms/aks-pending-upgrade).

Когда кластер AKS был изначально создан на первом шаге, был указан `--kubernetes-version` *1.13.10* . Это задание версии Kubernetes для плоскости управления и пула узлов по умолчанию. Команды в этом разделе объясняют, как обновить отдельный пул узлов.

Связь между обновлением Kubernetes версии плоскости управления и пулом узлов описывается в [разделе ниже](#upgrade-a-cluster-control-plane-with-multiple-node-pools).

> [!NOTE]
> Версия образа ОС пула узлов привязана к Kubernetes версии кластера. Обновления образа ОС будут получаться только после обновления кластера.

Так как в этом примере есть два пула узлов, для обновления пула узлов необходимо использовать команду [AZ AKS нодепул Upgrade][az-aks-nodepool-upgrade] . Давайте выполним обновление *минодепул* до Kubernetes *1.13.10*. Используйте команду [AZ AKS нодепул Upgrade][az-aks-nodepool-upgrade] , чтобы обновить пул узлов, как показано в следующем примере:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version 1.13.10 \
    --no-wait
```

Снова перечислите состояние пулов узлов с помощью команды [AZ AKS node Pool List][az-aks-nodepool-list] . В следующем примере показано, что *минодепул* находится в состоянии *обновления* до *1.13.10*:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.13.10",
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
    "orchestratorVersion": "1.13.10",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Обновление узлов до указанной версии занимает несколько минут.

Рекомендуется обновить все пулы узлов в кластере AKS до одной версии Kubernetes. Поведением `az aks upgrade` по умолчанию является обновление всех пулов узлов вместе с плоскостью управления для достижения этого выравнивания. Возможность обновления индивидуальных пулов узлов позволяет выполнять последовательное обновление и распланировать модули Pod между пулами узлов, чтобы поддерживать время работы приложения в указанных выше ограничениях.

## <a name="upgrade-a-cluster-control-plane-with-multiple-node-pools"></a>Обновление плоскости управления кластера с несколькими пулами узлов

> [!NOTE]
> Kubernetes использует стандартную схему управления версиями [семантического управления версиями](https://semver.org/) . Номер версии выражается в виде *x. y. z*, где *x* — основная версия, *y* — дополнительный номер версии, а *z* — версия исправления. Например, в версии *1.12.6*1 — основной номер версии, 12 — дополнительный номер версии, а 6 — версия исправления. Kubernetes версия плоскости управления и пул начального узла устанавливаются во время создания кластера. При добавлении в кластер для всех дополнительных пулов узлов устанавливается версия Kubernetes. Версии Kubernetes могут отличаться между пулами узлов, а также между пулом узлов и плоскостью управления.

Кластер AKS содержит два объекта ресурсов кластера с связанными версиями Kubernetes.

1. Версия Kubernetes плоскости управления кластером.
2. Пул узлов с версией Kubernetes.

Плоскость управления сопоставляется с одним или несколькими пулами узлов. Поведение операции обновления зависит от того, какая команда Azure CLI используется.

Обновление плоскости управления AKS требует использования `az aks upgrade`. Это обновляет версию плоскости управления и все пулы узлов в кластере. 

При выдаче команды `az aks upgrade` с флагом `--control-plane-only` обновляется только плоскость управления кластером. Ни один из связанных пулов узлов в кластере не изменился.

Для обновления отдельных пулов узлов необходимо использовать `az aks nodepool upgrade`. Это обновляет только пул целевых узлов с указанной версией Kubernetes

### <a name="validation-rules-for-upgrades"></a>Правила проверки для обновлений

Допустимые обновления Kubernetes для плоскости управления и пулов узлов кластера проверяются следующими наборами правил.

* Правила для допустимых версий для обновления пулов узлов:
   * Версия пула узлов должна иметь ту же *основную* версию, что и плоскость управления.
   * *Дополнительная* версия пула узлов должна быть в пределах двух *промежуточных* версий версии плоскости управления.
   * Версия пула узлов не может быть больше, чем `major.minor.patch` версия элемента управления.

* Правила для отправки операции обновления:
   * Нельзя перейти на более раннюю плоскость управления или Kubernetes версию пула узлов.
   * Если не указана версия Kubernetes пула узлов, поведение зависит от используемого клиента. Объявление в шаблонах диспетчер ресурсов приводит к переходу к существующей версии, определенной для пула узлов, если она используется, если не задана ни одна версия плоскости управления для возврата.
   * Можно обновить или масштабировать плоскость управления или пул узлов в определенный момент времени, но нельзя одновременно отправлять несколько операций в одну плоскость управления или ресурс пула узлов.

## <a name="scale-a-node-pool-manually"></a>Масштабирование пула узлов вручную

По мере изменения требований к рабочей нагрузке приложения может потребоваться масштабирование количества узлов в пуле узлов. Количество узлов можно масштабировать вверх или вниз.

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

Чтобы масштабировать количество узлов в пуле узлов, используйте команду [AZ AKS node Pool Scale][az-aks-nodepool-scale] . В следующем примере показано масштабирование количества узлов в *минодепул* до *5*:

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

Снова перечислите состояние пулов узлов с помощью команды [AZ AKS node Pool List][az-aks-nodepool-list] . В следующем примере показано, что *минодепул* находится в состоянии *масштабирования* с новым числом узлов, равным *5* .

```console
$ az aks nodepool list -g myResourceGroupPools --cluster-name myAKSCluster

[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.13.10",
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
    "orchestratorVersion": "1.13.10",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Выполнение операции масштабирования займет несколько минут.

## <a name="scale-a-specific-node-pool-automatically-by-enabling-the-cluster-autoscaler"></a>Автоматическое масштабирование определенного пула узлов путем включения автомасштабирования кластера

AKS предлагает отдельную функцию автоматического масштабирования пулов узлов с помощью функции, которая называется [автомасштабированием кластера](cluster-autoscaler.md). Эту функцию можно включить для каждого пула узлов с уникальными минимальным и максимальным числом масштабируемых счетчиков на пул узлов. Узнайте, как [использовать Автомасштабирование кластера на пул узлов](cluster-autoscaler.md#use-the-cluster-autoscaler-with-multiple-node-pools-enabled).

## <a name="delete-a-node-pool"></a>Удаление пула узлов

Если пул больше не нужен, его можно удалить и удалить базовые узлы виртуальных машин. Чтобы удалить пул узлов, выполните команду [AZ AKS node Pool Delete][az-aks-nodepool-delete] и укажите имя пула узлов. В следующем примере удаляется *минудепул* , созданный на предыдущих шагах.

> [!CAUTION]
> Отсутствуют варианты восстановления для потери данных, которые могут возникнуть при удалении пула узлов. Если не удается запланировать модули Pod в других пулах узлов, эти приложения недоступны. Убедитесь, что пул узлов не удаляется, когда используемые в нем приложения не имеют резервных копий данных или не могут выполняться на других пулах узлов в кластере.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

Следующий пример выходных данных команды [AZ AKS node Pool List][az-aks-nodepool-list] показывает, что *минодепул* находится в состоянии *удаления* :

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.13.10",
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
    "orchestratorVersion": "1.13.10",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Удаление узлов и пула узлов занимает несколько минут.

## <a name="specify-a-vm-size-for-a-node-pool"></a>Указание размера виртуальной машины для пула узлов

В предыдущих примерах для создания пула узлов использовался размер виртуальной машины по умолчанию для узлов, созданных в кластере. Более распространенный сценарий — создание пулов узлов с различными размерами и возможностями виртуальных машин. Например, можно создать пул узлов, содержащий узлы с большими объемами ресурсов ЦП или памяти, или пул узлов, который обеспечивает поддержку GPU. На следующем шаге вы [используете таинтс и](#schedule-pods-using-taints-and-tolerations) допуски, чтобы сообщить планировщику Kubernetes, как ограничить доступ к модулям Pod, которые могут выполняться на этих узлах.

В следующем примере создайте пул узлов на основе GPU, который использует *Standard_NC6* размер виртуальной машины. Эти виртуальные машины работают на базе карты NVIDIA Tesla K80. Дополнительные сведения о доступных размерах виртуальных машин см. [в статье размеры виртуальной машины Linux в Azure][vm-sizes].

Создайте пул узлов с помощью команды [AZ AKS node Pool Add][az-aks-nodepool-add] . На этот раз укажите имя *гпунодепул*и используйте параметр `--node-vm-size`, чтобы указать размер *Standard_NC6* :

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

В следующем примере выходных данных команды [AZ AKS node Pool List][az-aks-nodepool-list] показано, что *гпунодепул* *создает* узлы с указанным *VmSize*:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "gpunodepool",
    "orchestratorVersion": "1.13.10",
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
    "orchestratorVersion": "1.13.10",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Для успешного создания *гпунодепул* потребуется несколько минут.

## <a name="schedule-pods-using-taints-and-tolerations"></a>Планирование модулей Pod с помощью таинтс и допусков

Теперь у вас есть два пула узлов в кластере — пул узлов по умолчанию изначально создан и пул узлов на основе GPU. Используйте команду [kubectl Get Nodes][kubectl-get] , чтобы просмотреть узлы в кластере. В следующем примере выходных данных показаны узлы:

```console
$ kubectl get nodes

NAME                                 STATUS   ROLES   AGE     VERSION
aks-gpunodepool-28993262-vmss000000  Ready    agent   4m22s   v1.13.10
aks-nodepool1-28993262-vmss000000    Ready    agent   115m    v1.13.10
```

Для ограничения рабочих нагрузок, которые могут выполняться на узлах, в планировщике Kubernetes используются параметры taint (отметка) и toleration (толерантность).

* **Отметка** применяется к узлу, указывая, что на него могут назначаться только определенные модули pod.
* Затем к модулям pod применяется параметр **toleration**, указывающий, что они *допускают* метку узла.

Дополнительные сведения о том, как использовать расширенные функции Kubernetes Schedule, см. в статье рекомендации [по использованию расширенных функций планировщика в AKS][taints-tolerations] .

В этом примере примените таинт к узлу на основе GPU с помощью команды [kubectl таинт node][kubectl-taint] . Укажите имя узла на основе GPU из выходных данных предыдущей команды `kubectl get nodes`. Таинт применяется в качестве *ключа: value* , а затем — параметра планирования. В следующем примере используется пара *SKU = GPU* и определяются модули Pod, в противном случае — возможность *непланирования* :

```console
kubectl taint node aks-gpunodepool-28993262-vmss000000 sku=gpu:NoSchedule
```

В следующем примере манифеста YAML используется отклонения, позволяющие планировщику Kubernetes запускать модуль NGINX на узле на основе GPU. Более подходящий, но пример с большим временем выполнения задания Tensorflow для набора данных MNIST см. [в разделе Использование GPU для рабочих нагрузок с большим количеством вычислений в AKS][gpu-cluster].

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

Запланируйте модуль Pod с помощью команды `kubectl apply -f gpu-toleration.yaml`:

```console
kubectl apply -f gpu-toleration.yaml
```

Планирование Pod и извлечение образа NGINX займет несколько секунд. Чтобы просмотреть состояние Pod, используйте команду [kubectl описание Pod][kubectl-describe] . В следующем сжатом примере выходных данных показано, что применяется отклонения *SKU = GPU:* with. В разделе событий планировщик назначил модулю Pod узел *AKS-гпунодепул-28993262-vmss000000* на основе GPU:

```console
$ kubectl describe pod mypod

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

На узлах в *гпунодепул*можно планировать только модули Pod, к которым применен этот таинт. Любой другой модуль будет запланирован в пуле узлов *nodepool1* . Если вы создаете дополнительные пулы узлов, вы можете использовать дополнительные таинтс и допуски, чтобы ограничить возможности планирования модулей в этих ресурсах узла.

## <a name="manage-node-pools-using-a-resource-manager-template"></a>Управление пулами узлов с помощью шаблона диспетчер ресурсов

При использовании шаблона Azure Resource Manager для создания и управления ресурсами, как правило, можно обновить параметры в шаблоне и повторно развернуть, чтобы обновить ресурс. При использовании пулов узлов в AKS профиль пула узлов не может быть обновлен после создания кластера AKS. Такое поведение означает, что вы не можете обновить существующий шаблон диспетчер ресурсов, внести изменения в пулы узлов и повторно развернуть. Вместо этого необходимо создать отдельный шаблон диспетчер ресурсов, который обновляет только пулы узлов для существующего кластера AKS.

Создайте шаблон, например `aks-agentpools.json`, и вставьте следующий пример манифеста. В этом примере шаблона настраиваются следующие параметры.

* Обновляет пул узлов *Linux* с именем *мяжентпул* для запуска трех узлов.
* Задает узлы в пуле узлов для запуска Kubernetes версии *1.13.10*.
* Определяет размер узла как *Standard_DS2_v2*.

Измените эти значения, чтобы обновить, добавить или удалить пулы узлов по мере необходимости:

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
            "aks": "2019-04-01"
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
            "apiVersion": "2019-04-01",
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
                "orchestratorVersion": "1.13.10"
            }
        }
    ]
}
```

Разверните этот шаблон с помощью команды [AZ Group Deployment Create][az-group-deployment-create] , как показано в следующем примере. Появится запрос на ввод имени и расположения существующего кластера AKS:

```azurecli-interactive
az group deployment create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

Обновление кластера AKS может занять несколько минут в зависимости от параметров пула узлов и операций, определенных в шаблоне диспетчер ресурсов.

## <a name="assign-a-public-ip-per-node-in-a-node-pool"></a>Назначение общедоступного IP-адреса для каждого узла в пуле узлов

> [!WARNING]
> При предварительном просмотре назначения общедоступного IP-адреса на узел он не может использоваться с *Load Balancer (цен. Категория "Стандартный") SKU в AKS* из-за возможных правил подсистемы балансировки нагрузки, конфликтующих с подготовкой виртуальной машины. В предварительной версии необходимо использовать *базовый Load Balancer SKU* , если необходимо назначить общедоступный IP-адрес для каждого узла.

Узлы AKS не нуждаются в собственных общедоступных IP-адресах для обмена данными. Однако некоторые сценарии могут потребовать, чтобы узлы в пуле узлов имели собственные общедоступные IP-адреса. В качестве примера можно привести игры, в которых консоль должна установить прямое подключение к облачной виртуальной машине для снижения числа прыжков. Для этого можно зарегистрировать отдельную предварительную версию функции, общедоступный IP-адрес узла (Предварительная версия).

```azurecli-interactive
az feature register --name NodePublicIPPreview --namespace Microsoft.ContainerService
```

После успешной регистрации разверните шаблон Azure Resource Manager, следуя тем же инструкциям, что и [выше](#manage-node-pools-using-a-resource-manager-template) , и добавьте свойство логического значения `enableNodePublicIP` в ажентпулпрофилес. Установите значение `true` как по умолчанию, если оно задано как `false`, если оно не указано. Это свойство только для создания, для которого требуется минимальная версия API 2019-06-01. Это можно применить к пулам узлов Linux и Windows.

## <a name="clean-up-resources"></a>Очистка ресурсов

В этой статье вы создали кластер AKS, содержащий узлы на основе GPU. Чтобы уменьшить излишнюю стоимость, можно удалить *гпунодепул*или весь кластер AKS.

Чтобы удалить пул узлов на основе GPU, используйте команду [AZ AKS нодепул Delete][az-aks-nodepool-delete] , как показано в следующем примере:

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

Чтобы удалить сам кластер, выполните команду [AZ Group Delete][az-group-delete] , чтобы удалить группу ресурсов AKS:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Дополнительная информация

В этой статье вы узнали, как создавать и администрировать несколько пулов узлов в кластере AKS. Дополнительные сведения об управлении модулями Pod в пулах узлов см. в разделе рекомендации [по использованию расширенных функций планировщика в AKS][operator-best-practices-advanced-scheduler].

Сведения о создании и использовании пулов узлов контейнера Windows Server см. [в разделе Создание контейнера Windows Server в AKS][aks-windows].

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- INTERNAL LINKS -->
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-list
[az-aks-nodepool-upgrade]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-delete
[vm-sizes]: ../virtual-machines/linux/sizes.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[gpu-cluster]: gpu-cluster.md
[az-group-delete]: /cli/azure/group#az-group-delete
[install-azure-cli]: /cli/azure/install-azure-cli
[supported-versions]: supported-kubernetes-versions.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-windows]: windows-container-cli.md
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
