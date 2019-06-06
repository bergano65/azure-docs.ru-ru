---
title: Использовать несколько пулов узлов в службе Azure Kubernetes (AKS)
description: Узнайте, как создавать и управлять ими несколько пулов узлов для кластера в службе Azure Kubernetes (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/17/2019
ms.author: iainfou
ms.openlocfilehash: a295dfa1f7f2c58b3e45036212434837ac4bfb4d
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475454"
---
# <a name="preview---create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Предварительный просмотр - Создание и управление несколькими пулами узла для кластера в службе Azure Kubernetes (AKS)

В службе Kubernetes Azure (AKS), узлы с такой же конфигурацией группируются в *пулы узлов*. Эти пулы узлов содержат базовые виртуальные машины под управлением приложения. Начальное количество узлов и их размера (SKU) определяются при создании кластера AKS, который создает *узел пул по умолчанию*. Для поддержки приложений с различными вычислительными или к хранилищу, можно создать пулы дополнительных узлов. Например можно используйте эти пулы дополнительный узел для предоставления GPU для ресурсоемких приложений, или доступ к хранилища SSD с высокой производительностью.

В этой статье показано, как создавать и управлять ими несколько пулов узлов в кластере AKS. Эта функция в настоящее время находится на стадии предварительной версии.

> [!IMPORTANT]
> Функции предварительной версии AKS: самообслуживания, согласиться. Они предназначены для сбора отзывов и ошибки нашего сообщества. В предварительной версии эти функции не предназначены для использования в рабочей среде. Функции в общедоступной предварительной версии подпадают под поддержки «оптимальных затрат». Помощь от групп разработчиков AKS Техническая поддержка доступна во время рабочих часов тихоокеанского часового пояса (по тихоокеанскому времени) только. Дополнительные сведения обратитесь в следующие справочные статьи:
>
> * [Политики поддержки AKS][aks-support-policies]
> * [Часто задаваемые вопросы о поддержке Azure][aks-faq]

## <a name="before-you-begin"></a>Перед началом работы

Требуется Azure CLI версии 2.0.61 или более поздней версии установлен и настроен. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Установка расширения интерфейса командной строки aks-preview

Команды CLI для создания и управления несколькими пулы узлов доступны в *предварительной версии aks* расширение интерфейса командной строки. Установка *предварительной версии aks* расширение Azure CLI с помощью [добавить расширения az] [ az-extension-add] команды, как показано в следующем примере:

```azurecli-interactive
az extension add --name aks-preview
```

> [!NOTE]
> Если вы ранее установили *предварительной версии aks* расширения, устанавливать доступные обновления с помощью `az extension update --name aks-preview` команды.

### <a name="register-multiple-node-pool-feature-provider"></a>Регистрация поставщика функции пула несколько узла

Чтобы создать кластер AKS, можно использовать несколько пулов узлов, необходимо сначала включите двух флагов компонентов в вашей подписке. Кластеры с несколькими узлами пула использовать масштабируемый набор виртуальных машин (VMSS) для управления развертыванием и конфигурацию узлов Kubernetes. Зарегистрировать *MultiAgentpoolPreview* и *VMSSPreview* флаги компонентов с помощью [az функция register] [ az-feature-register] команды, как показано на Следующий пример:

```azurecli-interactive
az feature register --name MultiAgentpoolPreview --namespace Microsoft.ContainerService
az feature register --name VMSSPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> Создается после успешной регистрации кластера AKS *MultiAgentpoolPreview* использования этой предварительной версии кластера. Чтобы продолжить создавать кластеры обычный, полностью поддерживаются, не включите функции предварительной версии на рабочие подписки. Используйте отдельный тестовый или разработка подписки Azure для тестирования функции предварительной версии.

Через несколько минут отобразится состояние *Registered* (Зарегистрировано). Состояние регистрации можно проверить с помощью команды [az feature list][az-feature-list].

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MultiAgentpoolPreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
```

Когда все будет готово, обновите регистрацию поставщика ресурсов *Microsoft.ContainerService* с помощью команды [az provider register][az-provider-register].

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>Ограничения

При создании и управление кластерами AKS, которые поддерживают несколько пулов узлов, применяются следующие ограничения:

* Несколько пулов узлов доступны только для кластеров, созданных после успешной регистрации *MultiAgentpoolPreview* и *VMSSPreview* компоненты для вашей подписки. Невозможно добавить или Управление пулами узел в существующий кластер AKS создан, прежде чем эти функции были успешно зарегистрирован.
* Невозможно удалить пул первый узел.
* Нельзя использовать надстройку маршрутизации HTTP приложения.
* Вы не можете пулы Добавление, обновление и удаление узлов, используя существующий шаблон Resource Manager, как и большинство операций. Вместо этого [использовать отдельный шаблон Resource Manager](#manage-node-pools-using-a-resource-manager-template) вносить изменения в пулы узлов в кластере AKS.
* Нельзя использовать автомасштабирования кластера (в настоящее время в предварительной версии в AKS).

Пока эта функция находится на этапе предварительной версии, применяются следующие дополнительные ограничения.

* Кластер AKS может иметь не более восьми пулов узлов.
* Кластер AKS может иметь не более 400 узлов в пулах этих восьми узлов.

## <a name="create-an-aks-cluster"></a>Создание кластера AKS

Чтобы приступить к работе, создайте кластер AKS на одном узле пула. В следующем примере используется [Создание группы az] [ az-group-create] команду, чтобы создать группу ресурсов с именем *myResourceGroup* в *eastus* регион. Кластер службы контейнеров AZURE с именем *myAKSCluster* создается с помощью [создать az aks] [ az-aks-create] команды. Объект *версия--kubernetes* из *1.12.6* позволяет увидеть, как обновить пул узлов на следующем шаге. Можно указать любой [поддерживаемая версия Kubernetes][supported-versions].

```azurecli-interactive
# Create a resource group in East US
az group create --name myResourceGroup --location eastus

# Create a basic single-node AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-vmss \
    --node-count 1 \
    --generate-ssh-keys \
    --kubernetes-version 1.12.6
```

Создание кластера занимает несколько минут.

Когда кластер будет готов, используйте [az aks get-credentials] [ az-aks-get-credentials] команду, чтобы получить учетные данные кластера для использования с `kubectl`:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>Добавьте пул узлов

Кластере, созданном на предыдущем шаге есть пул один узел. Давайте добавим второй узел пула при помощи [az aks узлов в пуле добавьте] [ az-aks-nodepool-add] команды. В следующем примере создается пул узлов с именем *mynodepool* , которое будет выполняться *3* узлов:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3
```

Чтобы просмотреть состояние пулов узла, используйте [список пула узлов az aks] [ az-aks-nodepool-list] команду и укажите имя группы и кластером ресурса:

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster -o table
```

В следующем примере выходных данных показано, что *mynodepool* успешно создан с тремя узлами в пуле узлов. При создании кластера AKS на предыдущем шаге, значение по умолчанию *nodepool1* был создан с помощью узла число *1*.

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup         VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  --------------------  ---------------
VirtualMachineScaleSets  3        110        mynodepool  1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
```

> [!TIP]
> Если не *OrchestratorVersion* или *VmSize* указывается при добавлении пул узлов, узлы создаются в зависимости от значения по умолчанию для кластера AKS. В этом примере использовалась версия Kubernetes *1.12.6* и размер узла, равный *Standard_DS2_v2*.

## <a name="upgrade-a-node-pool"></a>Обновить пул узлов

При создании кластера AKS на первом шаге, `--kubernetes-version` из *1.12.6* был указан. Выполним обновление *mynodepool* в Kubernetes *1.12.7*. Используйте [обновления пула узел az aks] [ az-aks-nodepool-upgrade] команду, чтобы обновить пуле узла, как показано в следующем примере:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version 1.12.7 \
    --no-wait
```

Список состояний пулов узел снова, используя [список пула узлов az aks] [ az-aks-nodepool-list] команды. В следующем примере показано, что *mynodepool* в *обновление* состояние *1.12.7*:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup    VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  ---------------  ---------------
VirtualMachineScaleSets  3        110        mynodepool  1.12.7                 100             Linux     Upgrading            myResourceGroup  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.12.6                 100             Linux     Succeeded            myResourceGroup  Standard_DS2_v2
```

Он занимает несколько минут, чтобы обновить узлы в указанной версии.

Рекомендуется следует обновить все пулы узлов в кластере AKS до одной и той же версии Kubernetes. Возможность обновления пулов отдельных узлов позволяет выполнять последовательное обновление и запланировать модули POD между пулами узел для поддержания доступности приложения.

## <a name="scale-a-node-pool"></a>Масштабировать пул узлов

Как приложение изменения потребностей рабочей нагрузки, может потребоваться масштабировать число узлов в пуле узлов. Количество узлов можно масштабировать вверх или вниз.

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

Чтобы увеличить количество узлов в пуле узлов, используйте [az aks узел пула масштабирования] [ az-aks-nodepool-scale] команды. В следующем примере масштабируется количество узлов в *mynodepool* для *5*:

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

Список состояний пулов узел снова, используя [список пула узлов az aks] [ az-aks-nodepool-list] команды. В следующем примере показано, что *mynodepool* в *масштабирование* состояния с новый счетчик для *5* узлов:

```console
$ az aks nodepool list -g myResourceGroupPools --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup         VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  --------------------  ---------------
VirtualMachineScaleSets  5        110        mynodepool  1.12.7                 100             Linux     Scaling              myResourceGroupPools  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
```

Он занимает несколько минут для завершения операции масштабирования.

## <a name="delete-a-node-pool"></a>Удаление пула узла

Если больше не требуется пул, можно удалить его и удалите базовые узлы виртуальной Машины. Чтобы удалить пул узлов, используйте [удаления пула узел az aks] [ az-aks-nodepool-delete] команду и укажите имя группы узлов. В следующем примере удаляется *mynoodepool* созданные на предыдущих шагах:

> [!CAUTION]
> Существуют варианты восстановления без потери данных, которая возникает при удалении пул узлов. Если модули не могут быть запланированы на другие пулы узлов, эти приложения будут недоступны. Убедитесь, что когда резервные копии данных или возможность запускать на другие пулы узлов в кластере отсутствуют в использовании приложений, не удаляйте пул узлов.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

В следующем примере выходных данных из [список пула узлов az aks] [ az-aks-nodepool-list] команда отображает, *mynodepool* в *удаление* состояния:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup         VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  --------------------  ---------------
VirtualMachineScaleSets  5        110        mynodepool  1.12.7                 100             Linux     Deleting             myResourceGroupPools  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
```

Занимает несколько минут, чтобы удалить пул узлов и узлов.

## <a name="specify-a-vm-size-for-a-node-pool"></a>Укажите размер виртуальной Машины для узлов в пуле

В предыдущих примерах, чтобы создать пул узлов размер виртуальной Машины по умолчанию использовался для узлов, созданных в кластере. Распространенный сценарий — позволяют создавать пулы узлов с возможностями и различных размеров виртуальных Машин. Например можно создать пул узлов, содержащий узлы с большим количеством ЦП, памяти или пул узлов, который обеспечивает поддержку GPU. На следующем шаге вы [taints и tolerations](#schedule-pods-using-taints-and-tolerations) определить планировщик Kubernetes, как ограничить доступ для модулей, которые могут выполняться на этих узлах.

В следующем примере, создайте пул узлов на основе графического Процессора, использующий *Standard_NC6* размер виртуальной Машины. Эти виртуальные машины на базе карты NVIDIA Tesla K80. Сведения о доступных размерах виртуальных Машин, см. в разделе [размеры виртуальных машин Linux в Azure][vm-sizes].

Создание пула узла с помощью [az aks узлов в пуле добавьте] [ az-aks-nodepool-add] еще раз выполните команду. На этот раз укажите имя *gpunodepool*и использовать `--node-vm-size` параметр для указания *Standard_NC6* размер:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

В следующем примере выходных данных из [список пула узлов az aks] [ az-aks-nodepool-list] команда отображает, *gpunodepool* — *создание* узлов с указанный *VmSize*:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name         OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup         VmSize
-----------------------  -------  ---------  -----------  ---------------------  --------------  --------  -------------------  --------------------  ---------------
VirtualMachineScaleSets  1        110        gpunodepool  1.12.6                 100             Linux     Creating             myResourceGroupPools  Standard_NC6
VirtualMachineScaleSets  1        110        nodepool1    1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
```

Он занимает несколько минут *gpunodepool* должен быть успешно создан.

## <a name="schedule-pods-using-taints-and-tolerations"></a>Расписание модулей с помощью taints и tolerations

Теперь у вас есть два пулы узлов в кластере — изначально создан пул узла по умолчанию и пула на основе графического Процессора узла. Используйте [kubectl получить узлы] [ kubectl-get] команду, чтобы просмотреть узлы в кластере. В следующем примере выходных данных показан один узел в каждом пуле узла:

```console
$ kubectl get nodes

NAME                                 STATUS   ROLES   AGE     VERSION
aks-gpunodepool-28993262-vmss000000  Ready    agent   4m22s   v1.12.6
aks-nodepool1-28993262-vmss000000    Ready    agent   115m    v1.12.6
```

Для ограничения рабочих нагрузок, которые могут выполняться на узлах, в планировщике Kubernetes используются параметры taint (отметка) и toleration (толерантность).

* **Отметка** применяется к узлу, указывая, что на него могут назначаться только определенные модули pod.
* Затем к модулям pod применяется параметр **toleration**, указывающий, что они *допускают* метку узла.

Дополнительные сведения об использовании расширенных Kubernetes запланированные функции, см. в разделе [советы и рекомендации для возможности расширенной планировщика в AKS][taints-tolerations]

В этом примере применяются нарушить проверку на основе графического Процессора узла с помощью [kubectl нарушить проверку узла] [ kubectl-taint] команды. Укажите имя узла на основе графического Процессора из выходных данных предыдущей `kubectl get nodes` команды. Нарушить проверку будет применяться в качестве *ключ: значение* и затем режим планирования. В следующем примере используется *sku = gpu* пары, а также определяет модулей имеющими *NoSchedule* возможности:

```console
kubectl taint node aks-gpunodepool-28993262-vmss000000 sku=gpu:NoSchedule
```

Следующий простой пример манифеста YAML использует toleration позволяет планировщик Kubernetes для запуска модуля NGINX на узле на основе графического Процессора. Пример более подходящим, но длительное время для выполнения задания Tensorflow от набора данных MNIST, см. в разделе [использования GPU для интенсивных вычислительных рабочих нагрузок в AKS][gpu-cluster].

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

Запланировать pod с помощью `kubectl apply -f gpu-toleration.yaml` команды:

```console
kubectl apply -f gpu-toleration.yaml
```

Занимает несколько секунд, чтобы запланировать модуль и образ NGINX. Используйте [pod описания kubectl] [ kubectl-describe] команду, чтобы просмотреть состояние pod. Следующем сокращенном примере выходных данных будет указано *sku = gpu:NoSchedule* toleration применяется. В разделе «события» планировщик назначил pod для *aks-gpunodepool-28993262-vmss000000* узел на основе графического Процессора:

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

Можно запланировать только для модулей, которые имеют этот нарушить проверку применения на узлах в *gpunodepool*. Любые другие pod будет запланировано в *nodepool1* узлов в пуле. При создании пулов дополнительный узел, можно использовать дополнительные taints и tolerations для ограничения какие-либо модулей, которые могут быть назначены на эти ресурсы узла.

## <a name="manage-node-pools-using-a-resource-manager-template"></a>Управление пулами узел, с помощью шаблона Resource Manager

При использовании шаблона Azure Resource Manager для создания и управляемые ресурсы, обычно можно обновить параметры в шаблоне и повторное развертывание для обновления ресурса. Профиль пула начальный узел с пулы узлов в AKS, нельзя обновить после создания кластера AKS. Это означает, что невозможно обновить существующий шаблон Resource Manager, внести изменения в пулах узлов и повторное развертывание. Вместо этого необходимо создать отдельный шаблон Resource Manager, который обновляет только пулы агентов для существующего кластера AKS.

Создание шаблона, такие как `aks-agentpools.json` и вставьте приведенный ниже пример манифест. Этот пример шаблона настраивает следующие параметры:

* Обновления *Linux* пул агентов с именем *myagentpool* для запуска трех узлов.
* Задает узлы в пуле узел для запуска Kubernetes версии *1.12.8*.
* Определяет размер узла как *Standard_DS2_v2*.

Измените эти значения, как требуется обновление, добавление или удаление пулов узлов при необходимости.

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
            "orchestratorVersion": "1.12.8"
      }
    }
  ]
}
```

Разверните этот шаблон с использованием [Создание развертывания группы az] [ az-group-deployment-create] команды, как показано в следующем примере. Вам будет предложено на существующие имя кластера AKS и расположение:

```azurecli-interactive
az group deployment create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

Может занять несколько минут на обновление кластера AKS в зависимости от параметров пула узла и операции, которые задаются в шаблоне Resource Manager.

## <a name="clean-up-resources"></a>Очистка ресурсов

В этой статье вы создали кластер AKS, который включает в себя узлы, на основе графического Процессора. Чтобы уменьшить излишних затрат, может потребоваться удалить *gpunodepool*, или весь кластер AKS.

Чтобы удалить пул на основе графического Процессора узла, используйте [удалить az aks nodepool] [ az-aks-nodepool-delete] команды, как показано в следующем примере:

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

Чтобы удалить сам кластер, используйте [удаление группы az] [ az-group-delete] команду, чтобы удалить группу ресурсов AKS:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы узнали, как создавать и управлять ими несколько пулов узлов в кластере AKS. Дополнительные сведения о способах управления POD, содержащихся в пулах узлов см. в разделе [советы и рекомендации для возможности расширенной планировщика в AKS][operator-best-practices-advanced-scheduler].

Чтобы создать и использовать пулы узлов контейнера Windows Server, см. в разделе [создадим контейнер Windows Server в AKS][aks-windows].

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- INTERNAL LINKS -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-nodepool-list
[az-aks-nodepool-upgrade]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-nodepool-delete
[vm-sizes]: ../virtual-machines/linux/sizes.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[gpu-cluster]: gpu-cluster.md
[az-group-delete]: /cli/azure/group#az-group-delete
[install-azure-cli]: /cli/azure/install-azure-cli
[supported-versions]: supported-kubernetes-versions.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-windows]: windows-container-cli.md
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
