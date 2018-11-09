---
title: Использование GPU в службе Azure Kubernetes (AKS)
description: Узнайте, как использовать GPU для ресурсоемких вычислений или интенсивных графических рабочих нагрузок в Службе Azure Kubernetes (AKS).
services: container-service
author: lachie83
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 10/25/2018
ms.author: laevenso
ms.openlocfilehash: 683abd9bad93bff51bea84c8081d2b8f9d300cd4
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50419255"
---
# <a name="use-gpus-for-compute-intensive-workloads-on-azure-kubernetes-service-aks"></a>Использование процессоров GPU для интенсивных вычислительных рабочих нагрузок в Службе Azure Kubernetes (AKS)

Графические процессоры (GPU) обычно используются для ресурсоемких рабочих нагрузок, например графической обработки и визуализаций. AKS поддерживает создание пулов узлов с поддержкой GPU для выполнения этих ресурсоемких рабочих нагрузок в Kubernetes. Дополнительные сведения о доступных виртуальных машинах с поддержкой GPU см. в разделе [Размеры виртуальных машин, оптимизированных для GPU][gpu-skus]. Для узлов AKS мы рекомендуем использовать как минимум размер *Standard_NC6*.

> [!NOTE]
> Виртуальные машины с поддержкой GPU содержат специализированное оборудование, на которое предусмотрена более высокая цена и которое зависит от доступности в регионе. Дополнительные сведения можно получить с помощью [калькулятора цен][azure-pricing] и данных о [доступности в регионах][azure-availability].

## <a name="before-you-begin"></a>Перед началом работы

В этой статье предполагается, что у вас есть кластер AKS с узлами, поддерживающими процессоры GPU. Кластер AKS должен работать на платформе Kubernetes 1.10 или более поздней версии. Если вам нужен кластер AKS, соответствующий этим требованиям, обратитесь к первой части этой статьи, чтобы [создать кластер AKS](#create-an-aks-cluster).

Кроме того, нужно установить и настроить Azure CLI 2.0.49 или более поздней версии. Чтобы узнать версию, выполните команду  `az --version`. Если вам необходимо выполнить установку или обновление, см. статью  [Установка Azure CLI][install-azure-cli].

## <a name="create-an-aks-cluster"></a>Создание кластера AKS

Если вам нужен кластер AKS, соответствующий минимальным требованиям (узел с поддержкой GPU и Kubernetes 1.10 или более поздней версии), выполните приведенные ниже действия. Если вас уже есть кластер AKS, который отвечает этим требованиям, перейдите к следующему разделу.

Сначала создайте группу ресурсов для кластера, выполнив команду [az group create][az-group-create]. В следующем примере создается группа ресурсов *myResourceGroup* в регионе *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

Теперь выполните команду [az aks create][az-aks-create], чтобы создать кластер AKS. В следующем примере создается кластер с одним узлом размера `Standard_NC6` под управлением Kubernetes версии 1.10.8.

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-vm-size Standard_NC6 \
    --node-count 1 \
    --kubernetes-version 1.10.8
```

Получите учетные данные для кластера AKS с помощью команды [az aks get-credentials][az-aks-get-credentials].

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="confirm-that-gpus-are-schedulable"></a>Подтверждение того, что процессоры GPU доступны для планирования

Создав кластер AKS, убедитесь, что процессоры GPU доступны для планирования в Kubernetes. Сначала получите список узлов в кластере с помощью команды [kubectl get nodes][kubectl-get].

```
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-18821093-0   Ready    agent   6m    v1.10.8
```

Теперь с помощью команды [kubectl describe node][kubectl-describe] подтвердите, что процессоры GPU доступны для планирования. В разделе *Емкость* PU должен отображаться как `nvidia.com/gpu:  1`. Если вы не видите процессоры GPU, обратитесь к разделу [Устранение неполадок GPU](#troubleshoot-gpu-availability).

В следующем сокращенном примере показано, что GPU доступен на узле *aks-nodepool1-18821093-0*.

```
$ kubectl describe node aks-nodepool1-18821093-0

Name:               aks-nodepool1-18821093-0
Roles:              agent
Labels:             accelerator=nvidia

[...]

Capacity:
 cpu:                6
 ephemeral-storage:  30428648Ki
 hugepages-1Gi:      0
 hugepages-2Mi:      0
 memory:             57713824Ki
 nvidia.com/gpu:     1
 pods:               110
Allocatable:
 cpu:                5940m
 ephemeral-storage:  28043041951
 hugepages-1Gi:      0
 hugepages-2Mi:      0
 memory:             53417120Ki
 nvidia.com/gpu:     1
 pods:               110
System Info:
 Machine ID:                 688e083d19554d4a9563bd138f4ca98b
 System UUID:                08162568-B987-A84D-8865-98D6EFC64B32
 Boot ID:                    7b440249-8a96-42eb-950f-08c9a3c530b7
 Kernel Version:             4.15.0-1023-azure
 OS Image:                   Ubuntu 16.04.5 LTS
 Operating System:           linux
 Architecture:               amd64
 Container Runtime Version:  docker://1.13.1
 Kubelet Version:            v1.10.8
 Kube-Proxy Version:         v1.10.8
PodCIDR:                     10.244.0.0/24
ProviderID:                  azure:///subscriptions/19da35d3-9a1a-4f3b-9b9c-3c56ef409565/resourceGroups/MC_myGPUCluster_myGPUCluster_eastus/providers/Microsoft.Compute/virtualMachines/aks-nodepool1-18821093-0
Non-terminated Pods:         (9 in total)
  Namespace                  Name                                    CPU Requests  CPU Limits  Memory Requests  Memory Limits
  ---------                  ----                                    ------------  ----------  ---------------  -------------
  gpu-resources              nvidia-device-plugin-9cfcf              0 (0%)        0 (0%)      0 (0%)           0 (0%)

[...]
```

## <a name="run-a-gpu-enabled-workload"></a>Запуск рабочей нагрузки с поддержкой GPU

Чтобы увидеть, как используется GPU, запланируйте рабочую нагрузку с поддержкой GPU с помощью соответствующего запроса ресурса. В этом примере давайте выполним задание [Tensorflow](https://www.tensorflow.org/versions/r1.1/get_started/mnist/beginners) с [набором данных MNIST](http://yann.lecun.com/exdb/mnist/).

Создайте файл *samples-tf-mnist-demo.yaml* и вставьте в него приведенный ниже манифест YAML. Следующий манифест задания включает в себя ограничение ресурсов: `nvidia.com/gpu: 1`.

> [!NOTE]
> Если при вызове драйверов появится сообщение об ошибке несоответствия версий (например, версия драйвера CUDA недостаточна для версии среды выполнения CUDA), просмотрите матрицу совместимости драйверов nVidia: [https://docs.nvidia.com/deploy/cuda-compatibility/index.html](https://docs.nvidia.com/deploy/cuda-compatibility/index.html).

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  labels:
    app: samples-tf-mnist-demo
  name: samples-tf-mnist-demo
spec:
  template:
    metadata:
      labels:
        app: samples-tf-mnist-demo
    spec:
      containers:
      - name: samples-tf-mnist-demo
        image: microsoft/samples-tf-mnist-demo:gpu
        args: ["--max_steps", "500"]
        imagePullPolicy: IfNotPresent
        resources:
          limits:
           nvidia.com/gpu: 1
      restartPolicy: OnFailure
```

Используйте команду [kubectl apply][kubectl-apply], чтобы запустить задание. Эта команда анализирует файл манифеста и создает заданные объекты Kubernetes.

```console
kubectl apply -f samples-tf-mnist-demo.yaml
```

## <a name="view-the-status-and-output-of-the-gpu-enabled-workload"></a>Просмотр состояния и выходных данных рабочей нагрузки с поддержкой GPU

Отслеживайте ход выполнения задания с помощью команды [kubectl get jobs][kubectl-get] с аргументом `--watch`. Извлечение изображения и последующая обработка набора данных может занять несколько минут. Когда в столбце *COMPLETIONS* (Выполнение) отобразится значение *1/1*, задание будет успешно завершено.

```
$ kubectl get jobs samples-tf-mnist-demo --watch

NAME                    COMPLETIONS   DURATION   AGE

samples-tf-mnist-demo   0/1           3m29s      3m29s
samples-tf-mnist-demo   1/1   3m10s   3m36s
```

Чтобы просмотреть выходные данные рабочей нагрузки с поддержкой GPU, сначала нужно получить имена pod с помощью команды [kubectl get pods][kubectl-get].

```
$ kubectl get pods --selector app=samples-tf-mnist-demo

NAME                          READY   STATUS      RESTARTS   AGE
samples-tf-mnist-demo-smnr6   0/1     Completed   0          3m
```

Теперь выполните команду [kubectl logs][kubectl-logs], чтобы просмотреть журналы pod. В следующем примере журналов pod указано, что обнаружено соответствующее устройство GPU, `Tesla K80`. Укажите имя собственного pod.

```
$ kubectl logs samples-tf-mnist-demo-smnr6

2018-10-25 18:31:10.155010: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2018-10-25 18:31:10.305937: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties:
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: ccb6:00:00.0
totalMemory: 11.92GiB freeMemory: 11.85GiB
2018-10-25 18:31:10.305981: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: ccb6:00:00.0, compute capability: 3.7)
2018-10-25 18:31:14.941723: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.097
Accuracy at step 10: 0.6993
Accuracy at step 20: 0.8208
Accuracy at step 30: 0.8594
Accuracy at step 40: 0.8685
Accuracy at step 50: 0.8864
Accuracy at step 60: 0.901
Accuracy at step 70: 0.905
Accuracy at step 80: 0.9103
Accuracy at step 90: 0.9126
Adding run metadata for 99
Accuracy at step 100: 0.9176
Accuracy at step 110: 0.9149
Accuracy at step 120: 0.9187
Accuracy at step 130: 0.9253
Accuracy at step 140: 0.9252
Accuracy at step 150: 0.9266
Accuracy at step 160: 0.9255
Accuracy at step 170: 0.9267
Accuracy at step 180: 0.9257
Accuracy at step 190: 0.9309
Adding run metadata for 199
Accuracy at step 200: 0.9272
Accuracy at step 210: 0.9321
Accuracy at step 220: 0.9343
Accuracy at step 230: 0.9388
Accuracy at step 240: 0.9408
Accuracy at step 250: 0.9394
Accuracy at step 260: 0.9412
Accuracy at step 270: 0.9422
Accuracy at step 280: 0.9436
Accuracy at step 290: 0.9411
Adding run metadata for 299
Accuracy at step 300: 0.9426
Accuracy at step 310: 0.9466
Accuracy at step 320: 0.9458
Accuracy at step 330: 0.9407
Accuracy at step 340: 0.9445
Accuracy at step 350: 0.9486
Accuracy at step 360: 0.9475
Accuracy at step 370: 0.948
Accuracy at step 380: 0.9516
Accuracy at step 390: 0.9534
Adding run metadata for 399
Accuracy at step 400: 0.9501
Accuracy at step 410: 0.9552
Accuracy at step 420: 0.9535
Accuracy at step 430: 0.9545
Accuracy at step 440: 0.9533
Accuracy at step 450: 0.9526
Accuracy at step 460: 0.9566
Accuracy at step 470: 0.9547
Accuracy at step 480: 0.9548
Accuracy at step 490: 0.9545
Adding run metadata for 499
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы удалить связанные объекты Kubernetes, созданные в этой статье, выполните команду [kubectl delete job][kubectl delete], как показано ниже.

```console
kubectl delete jobs samples-tf-mnist-demo
```

## <a name="troubleshoot-gpu-availability"></a>Устранение неполадок доступности GPU

Если вы не видите процессоры GPU, доступные на узлах, может потребоваться развернуть DaemonSet для подключаемого модуля устройств nVidia. Этот DaemonSet запускает pod на каждом узле, чтобы предоставить необходимые драйверы для процессоров GPU.

Сначала с помощью команды [kubectl create namespace][kubectl-create] создайте пространство имен, например *gpu-resources*.

```console
kubectl create namespace gpu-resources
```

Создайте файл *nvidia-device-plugin-ds.yaml* и вставьте в него приведенный ниже манифест YAML. Обновите параметр `image: nvidia/k8s-device-plugin:1.10` в середине манифеста, чтобы указать соответствующую версию Kubernetes. Например, если кластер AKS использует Kubernetes версии 1.11, измените тег на `image: nvidia/k8s-device-plugin:1.11`.

```yaml
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
  labels:
    kubernetes.io/cluster-service: "true"
  name: nvidia-device-plugin
  namespace: gpu-resources
spec:
  template:
    metadata:
      # Mark this pod as a critical add-on; when enabled, the critical add-on scheduler
      # reserves resources for critical add-on pods so that they can be rescheduled after
      # a failure.  This annotation works in tandem with the toleration below.
      annotations:
        scheduler.alpha.kubernetes.io/critical-pod: ""
      labels:
        name: nvidia-device-plugin-ds
    spec:
      tolerations:
      # Allow this pod to be rescheduled while the node is in "critical add-ons only" mode.
      # This, along with the annotation above marks this pod as a critical add-on.
      - key: CriticalAddonsOnly
        operator: Exists
      containers:
      - image: nvidia/k8s-device-plugin:1.10 # Update this tag to match your Kubernetes version
        name: nvidia-device-plugin-ctr
        securityContext:
          allowPrivilegeEscalation: false
          capabilities:
            drop: ["ALL"]
        volumeMounts:
          - name: device-plugin
            mountPath: /var/lib/kubelet/device-plugins
      volumes:
        - name: device-plugin
          hostPath:
            path: /var/lib/kubelet/device-plugins
      nodeSelector:
        beta.kubernetes.io/os: linux
        accelerator: nvidia
```

Теперь выполните команду [kubectl apply][kubectl-apply], чтобы создать DaemonSet.

```
$ kubectl apply -f nvidia-device-plugin-ds.yaml

daemonset "nvidia-device-plugin" created
```

Еще раз выполните команду [kubectl describe node][kubectl-describe], чтобы убедиться, что теперь GPU доступен на узле.

## <a name="next-steps"></a>Дополнительная информация

Чтобы выполнять задания Apache Spark, ознакомьтесь с разделом [Запуск заданий Apache Spark в AKS][aks-spark].

Чтобы получить дополнительные сведения о выполнении рабочих нагрузок машинного обучения машины в Kubernetes, ознакомьтесь с [лабораториями Kubeflow][kubeflow-labs].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubeflow-labs]: https://github.com/Azure/kubeflow-labs
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[azure-pricing]: https://azure.microsoft.com/pricing/
[azure-availability]: https://azure.microsoft.com/global-infrastructure/services/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-spark]: spark-job.md
[gpu-skus]: ../virtual-machines/linux/sizes-gpu.md
[install-azure-cli]: /cli/azure/install-azure-cli
