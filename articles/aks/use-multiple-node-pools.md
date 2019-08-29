---
title: Использование пулов нескольких узлов в службе Kubernetes Azure (AKS)
description: Узнайте, как создать несколько пулов узлов для кластера в службе Kubernetes Azure (AKS) и управлять ими.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 08/9/2019
ms.author: mlearned
ms.openlocfilehash: caeb89332bd46b4f0cf2d0f9e5654aebca4d765d
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147265"
---
# <a name="preview---create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Предварительная версия. Создание пулов нескольких узлов для кластера в службе Kubernetes Azure (AKS) и управление ими

В службе Azure Kubernetes Service (AKS) узлы одной и той же конфигурации группируются в *Пулы узлов*. Эти пулы узлов содержат базовые виртуальные машины, на которых работают ваши приложения. Начальное число узлов и их размер (SKU) определяются при создании кластера AKS, который создает *пул узлов по умолчанию*. Для поддержки приложений, имеющих различные требования к вычислению или хранению, можно создать дополнительные пулы узлов. Например, используйте эти дополнительные пулы узлов, чтобы предоставить графические процессоры для приложений с большим объемом вычислений или доступ к высокопроизводительному хранилищу SSD.

> [!NOTE]
> Эта функция обеспечивает более высокий контроль над созданием нескольких пулов узлов и управлением ими. В результате для создания, обновления и удаления требуются отдельные команды. Ранее кластерные операции `az aks create` с `az aks update` помощью или использовали API манажедклустер и были единственным вариантом изменения плоскости управления и пула с одним узлом. Эта функция предоставляет отдельный набор операций для пулов агентов через API ажентпул и требует использования `az aks nodepool` набора команд для выполнения операций в пуле отдельных узлов.

В этой статье показано, как создать несколько пулов узлов и управлять ими в кластере AKS. Эта функция в настоящее время находится на стадии предварительной версии.

> [!IMPORTANT]
> Функции предварительной версии AKS — это самостоятельная служба. Предварительные версии предоставляются "как есть" и "как есть" и исключаются из соглашений об уровне обслуживания и ограниченной гарантии. Предварительные версии AKS частично покрываются службой поддержки клиентов на основе лучших усилий. Таким образом, эти функции не предназначены для использования в рабочей среде. Дополнительные сведения об отсутствии см. в следующих статьях поддержки:
>
> * [Политики поддержки AKS][aks-support-policies]
> * [Часто задаваемые вопросы о поддержке Azure][aks-faq]

## <a name="before-you-begin"></a>Перед началом работы

Требуется Azure CLI версии 2.0.61 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Установка расширения интерфейса командной строки aks-preview

Для использования нескольких пулов узлов требуется расширение CLI *AKS-Preview* версии 0.4.1 или более поздней. Установите расширение Azure CLI *AKS-Preview* с помощью команды [AZ Extension Add][az-extension-add] , а затем проверьте наличие доступных обновлений с помощью команды [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-multiple-node-pool-feature-provider"></a>Регистрация поставщика функций пула узлов с несколькими узлами

Чтобы создать кластер AKS, который может использовать несколько пулов узлов, сначала включите два флага компонентов в подписке. Кластеры пулов с несколькими узлами используют масштабируемый набор виртуальных машин (VMSS) для управления развертыванием и конфигурацией узлов Kubernetes. Зарегистрируйте флаги компонентов *мултиажентпулпревиев* и *вмсспревиев* с помощью команды [AZ Feature Register][az-feature-register] , как показано в следующем примере:

> [!CAUTION]
> Регистрация компонента в подписке в данный момент невозможна. После включения функций предварительной версии можно использовать значения по умолчанию для всех кластеров AKS, созданных в подписке. Не включайте предварительные версии функций в производственных подписках. Используйте отдельную подписку для тестирования функций предварительной версии и сбора отзывов.

```azurecli-interactive
az feature register --name MultiAgentpoolPreview --namespace Microsoft.ContainerService
az feature register --name VMSSPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> Любой кластер AKS, созданный после успешной регистрации *мултиажентпулпревиев* , будет использовать эту предварительную версию кластера. Чтобы продолжить создание обычных, полностью поддерживаемых кластеров, не включайте предварительные версии функций в производственных подписках. Используйте отдельную тестовую или новую подписку Azure для тестирования предварительных версий функций.

Через несколько минут отобразится состояние *Registered* (Зарегистрировано). Проверить состояние регистрации можно с помощью команды [AZ Feature List][az-feature-list] .

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MultiAgentpoolPreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
```

Когда все будет готово, обновите регистрацию поставщика ресурсов *Microsoft. ContainerService* с помощью команды [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>Ограничения

При создании кластеров AKS, поддерживающих несколько пулов узлов, и управлении ими действуют следующие ограничения.

* Пулы с несколькими узлами доступны только для кластеров, созданных после успешной регистрации компонентов *мултиажентпулпревиев* и *вмсспревиев* для вашей подписки. Вы не можете добавлять пулы узлов или управлять ими с помощью существующего кластера AKS, созданного до успешной регистрации этих компонентов.
* Пул первого узла удалить нельзя.
* Не удается использовать надстройку маршрутизации приложений HTTP.
* Вы не можете добавлять, обновлять и удалять пулы узлов с помощью существующего шаблона диспетчер ресурсов, как и в случае с большинством операций. Вместо этого [Используйте отдельный шаблон диспетчер ресурсов](#manage-node-pools-using-a-resource-manager-template) для внесения изменений в пулы узлов в кластере AKS.

Хотя эта функция находится на этапе предварительной версии, применяются следующие дополнительные ограничения.

* Кластер AKS может иметь не более восьми пулов узлов.
* Кластер AKS может содержать не более 400 узлов в восьми пулах узлов.
* Все пулы узлов должны находиться в одной подсети.

## <a name="create-an-aks-cluster"></a>Создание кластера AKS

Чтобы приступить к работе, создайте кластер AKS с одним пулом узлов. В следующем примере используется команда [AZ Group Create][az-group-create] для создания группы ресурсов с именем *myResourceGroup* в регионе *eastus* . После этого кластер AKS с именем *myAKSCluster* создается с помощью команды [AZ AKS Create][az-aks-create] . A *--kubernetes-версия* *1.13.10* используется для демонстрации обновления пула узлов на следующем шаге. Можно указать любую [поддерживаемую версию Kubernetes][supported-versions].

```azurecli-interactive
# Create a resource group in East US
az group create --name myResourceGroup --location eastus

# Create a basic single-node AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-vmss \
    --node-count 2 \
    --generate-ssh-keys \
    --kubernetes-version 1.13.10
```

Создание кластера занимает несколько минут.

> [!NOTE]
> Чтобы обеспечить надежное функционирование кластера, необходимо запустить по крайней мере 2 узла в пуле узлов по умолчанию, так как базовые системные службы работают в этом пуле узлов.

Когда кластер будет готов, выполните команду [AZ AKS Get-Credential][az-aks-get-credentials] , чтобы получить учетные данные кластера для использования с `kubectl`:

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
> Если *орчестраторверсион* или *VmSize* не указаны при добавлении пула узлов, узлы создаются на основе значений по умолчанию для кластера AKS. В этом примере это был Kubernetes версии *1.13.10* и размером узла *Standard_DS2_v2*.

## <a name="upgrade-a-node-pool"></a>Обновление пула узлов
 
> [!NOTE]
> Операции обновления и масштабирования в кластере или пуле узлов являются взаимоисключающими. Нельзя одновременно обновлять и масштабировать кластер или пул узлов. Вместо этого каждый тип операции должен быть завершен в целевом ресурсе до следующего запроса к этому же ресурсу. Дополнительные сведения см. в нашем [руководство по устранению неполадок](https://aka.ms/aks-pending-upgrade).

Когда кластер AKS был создан на первом шаге, `--kubernetes-version` был указан параметр *1.13.10* . Это задает версию Kubernetes как для плоскости управления, так и для начального пула узлов. Существуют различные команды для обновления Kubernetes версии плоскости управления и пула узлов. Команда используется для обновления плоскости управления, `az aks nodepool upgrade` а используется для обновления пула отдельных узлов. `az aks upgrade`

> [!NOTE]
> Версия образа ОС пула узлов привязана к Kubernetes версии кластера. Обновления образа ОС будут получаться только после обновления кластера.

Давайте выполним обновление *минодепул* до Kubernetes *1.13.10*. Используйте команду [AZ AKS node][az-aks-nodepool-upgrade] для обновления пула узлов, как показано в следующем примере:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version 1.13.10 \
    --no-wait
```

> [!Tip]
> Чтобы обновить плоскость управления до *1.14.6*, выполните команду `az aks upgrade -k 1.14.6`.

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

Рекомендуется обновить все пулы узлов в кластере AKS до одной версии Kubernetes. Возможность обновления индивидуальных пулов узлов позволяет выполнять последовательное обновление и распланировать модули Pod между пулами узлов, чтобы поддерживать время работы приложения в указанных выше ограничениях.

> [!NOTE]
> Kubernetes использует стандартную схему управления версиями [семантического управления версиями](https://semver.org/) . Номер версии выражается в виде *x. y. z*, где *x* — основная версия, *y* — дополнительный номер версии, а *z* — версия исправления. Например, в версии *1.12.6*1 — основной номер версии, 12 — дополнительный номер версии, а 6 — версия исправления. Kubernetes версия плоскости управления, а также начальный пул узлов задается во время создания кластера. При добавлении в кластер для всех дополнительных пулов узлов устанавливается версия Kubernetes. Версии Kubernetes могут отличаться между пулами узлов, а также между пулом узлов и плоскостью управления, однако применяются следующие ограничения.
> 
> * Версия пула узлов должна иметь ту же основную версию, что и плоскость управления.
> * Версия пула узлов может быть одной из вспомогательных версий меньше версии плоскости управления.
> * Версия пула узлов может быть любой версией исправления, если выполнены другие два ограничения.
> 
> Чтобы обновить Kubernetes версию плоскости управления, используйте `az aks upgrade`. Если в кластере имеется только один пул узлов, `az aks upgrade` команда также обновит версию пула узлов Kubernetes.

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

AKS предлагает отдельную функцию в предварительной версии для автоматического масштабирования пулов узлов с помощью функции, которая называется автомасштабированием [кластера](cluster-autoscaler.md). Эта функция является надстройкой AKS, которую можно включить для каждого пула узлов с уникальными минимальным и максимальным числом масштабируемых счетчиков на пул узлов. Узнайте, как [использовать Автомасштабирование кластера на пул узлов](cluster-autoscaler.md#use-the-cluster-autoscaler-with-multiple-node-pools-enabled).

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

В следующем примере создайте пул узлов на основе GPU, который использует размер виртуальной машины *Standard_NC6* . Эти виртуальные машины работают на базе карты NVIDIA Tesla K80. Дополнительные сведения о доступных размерах виртуальных машин см. [в статье размеры виртуальной машины Linux в Azure][vm-sizes].

Создайте пул узлов с помощью команды [AZ AKS node Pool Add][az-aks-nodepool-add] . На этот раз укажите имя *гпунодепул*и используйте `--node-vm-size` параметр, чтобы указать размер *Standard_NC6* :

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

В этом примере примените таинт к узлу на основе GPU с помощью команды [kubectl таинт node][kubectl-taint] . Укажите имя узла на основе GPU из выходных данных предыдущей `kubectl get nodes` команды. Таинт применяется в качестве *ключа: value* , а затем — параметра планирования. В следующем примере используется пара *SKU = GPU* и определяются модули Pod, в противном случае — возможность непланирования:

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

Запланируйте модуль с `kubectl apply -f gpu-toleration.yaml` помощью команды:

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

При использовании шаблона Azure Resource Manager для создания и управления ресурсами, как правило, можно обновить параметры в шаблоне и повторно развернуть, чтобы обновить ресурс. При использовании пулов узлов в AKS профиль пула узлов не может быть обновлен после создания кластера AKS. Такое поведение означает, что вы не можете обновить существующий шаблон диспетчер ресурсов, внести изменения в пулы узлов и повторно развернуть. Вместо этого необходимо создать отдельный шаблон диспетчер ресурсов, который обновляет только Пулы агентов для существующего кластера AKS.

Создайте шаблон, например `aks-agentpools.json` , и вставьте следующий пример манифеста. В этом примере шаблона настраиваются следующие параметры.

* Обновляет пул агентов *Linux* с именем *мяжентпул* для запуска трех узлов.
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

> [!NOTE]
> Во время предварительной версии существует ограничение на использование этой функции с *Load Balancer (цен. Категория "Стандартный") SKU в AKS (Предварительная версия)* из-за возможных конфликтов правил подсистемы балансировки нагрузки с подготовкой виртуальной машины. В предварительной версии используйте *базовый Load Balancer SKU* , если необходимо назначить общедоступный IP-адрес для каждого узла.

Узлы AKS не нуждаются в собственных общедоступных IP-адресах для обмена данными. Однако некоторые сценарии могут потребовать, чтобы узлы в пуле узлов имели собственные общедоступные IP-адреса. В качестве примера можно привести игры, в которых консоль должна установить прямое подключение к облачной виртуальной машине для снижения числа прыжков. Для этого можно зарегистрировать отдельную предварительную версию функции, общедоступный IP-адрес узла (Предварительная версия).

```azurecli-interactive
az feature register --name NodePublicIPPreview --namespace Microsoft.ContainerService
```

После успешной регистрации разверните шаблон Azure Resource Manager, следуя тем же инструкциям, что и [выше](#manage-node-pools-using-a-resource-manager-template) , и добавьте следующее логическое значение свойства "енабленодепублиЦип" в ажентпулпрофилес. Задайте для `true` этого параметра значение по умолчанию, `false` если оно не указано. Это свойство только для создания, для которого требуется минимальная версия API 2019-06-01. Это можно применить к пулам узлов Linux и Windows.

```
"agentPoolProfiles":[  
    {  
      "maxPods": 30,
      "osDiskSizeGB": 0,
      "agentCount": 3,
      "agentVmSize": "Standard_DS2_v2",
      "osType": "Linux",
      "vnetSubnetId": "[parameters('vnetSubnetId')]",
      "enableNodePublicIP":true
    }
```

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

## <a name="next-steps"></a>Следующие шаги

В этой статье вы узнали, как создавать и администрировать несколько пулов узлов в кластере AKS. Дополнительные сведения об управлении модулями Pod в пулах узлов см. в разделе рекомендации [по использованию расширенных функций планировщика в AKS][operator-best-practices-advanced-scheduler].

Сведения о создании и использовании пулов узлов контейнера Windows Server см. [в разделе Создание контейнера Windows Server в AKS][aks-windows].

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
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
