---
title: Развертывание экземпляров контейнеров с поддержкой GPU в Azure
description: Сведения о развертывании экземпляров контейнеров Azure для выполнения в ресурсах GPU.
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 11/29/2018
ms.author: danlep
ms.openlocfilehash: cc47ca07a843daf5cc35d23b838761166d39bdcc
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351377"
---
# <a name="deploy-container-instances-that-use-gpu-resources"></a>Развертывание экземпляров контейнеров, использующих ресурсы GPU

Чтобы выполнить определенные ресурсоемкие рабочие нагрузки в Экземплярах контейнеров Azure, разверните [группы контейнеров](container-instances-container-groups.md) с *ресурсами GPU*. Экземпляры контейнеров в группе могут получить доступ к одному графическому процессору NVIDIA Tesla или нескольким при выполнении рабочих нагрузок контейнеров, таких как CUDA и приложения глубокого обучения.

Как указано в этой статье, вы можете добавлять ресурсы GPU при развертывании группы контейнеров с использованием [файла YAML](container-instances-multi-container-yaml.md) или [шаблона Resource Manager](container-instances-multi-container-group.md).

> [!IMPORTANT]
> Сейчас эта функция доступна в предварительной версии, и применяются [некоторые ограничения](#preview-limitations). Предварительные версии предоставляются только в том случае, если вы принимаете [дополнительные условия использования][terms-of-use]. Некоторые аспекты этой функции могут быть изменены до выхода общедоступной версии.

## <a name="preview-limitations"></a>Ограничения предварительной версии

В режиме предварительной версии при использовании ресурсов GPU в группах контейнеров применяются следующие ограничения. 

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]

Со временем будут поддерживаться другие регионы.

**Поддерживаемые типы ОС**: только Linux.

**Дополнительные ограничения**: Ресурсы GPU нельзя использовать при развертывании группы контейнеров в [виртуальной сети](container-instances-vnet.md).

## <a name="about-gpu-resources"></a>Сведения о ресурсах GPU

### <a name="count-and-sku"></a>Количество и номер SKU

Чтобы использовать GPU в экземпляре контейнера, укажите *ресурс GPU* со следующими сведениями:

* **Количество**. Число GPU: **1**, **2** или **4**.
* **Номер SKU**. Номер SKU GPU: **K80**, **P100** или **V100**. Каждый номер SKU сопоставляется с графическим процессором NVIDIA Tesla в одном из следующих семейств виртуальных машин с поддержкой GPU Azure:

  | SKU | Семейство виртуальных машин |
  | --- | --- |
  | K80 | [NC](../virtual-machines/linux/sizes-gpu.md#nc-series) |
  | P100 | [NCv2](../virtual-machines/linux/sizes-gpu.md#ncv2-series) |
  | V100 | [NCv3](../virtual-machines/linux/sizes-gpu.md#ncv3-series) |

[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

При развертывании ресурсов GPU, задайте ресурсы ЦП и памяти для рабочей нагрузки, вплоть до максимального значения, показанные на приведенной выше таблице. Эти значения в данный момент больше ресурсов ЦП и памяти, доступных в группах контейнеров без ресурсы графического Процессора.  

### <a name="things-to-know"></a>Полезная информация

* **Время развертывания**. Создание группы контейнеров, содержащей ресурсы GPU, занимает **8–10 минут**. Это связано с затратой времени на подготовку и настройку виртуальной машины с поддержкой GPU в Azure. 

* **Цены**. Аналогично группам контейнеров без ресурсов GPU, Azure выставляет счета за ресурсы, потребляемые в течение *длительности использования* группы контейнеров с ресурсами GPU. Длительность использования рассчитывается с момента извлечения первого образа контейнера до момента завершения работы группы контейнеров. Она не включает время, затрачиваемое на развертывание группы контейнеров.

  Цены для групп контейнеров с ресурсами GPU выше цен для групп контейнеров без ресурсов. См. сведения о [ценах](https://azure.microsoft.com/pricing/details/container-instances/).

* **Драйверы CUDA**. Экземпляры контейнеров с ресурсами GPU предварительно снабжены драйверами NVIDIA CUDA и средами выполнения контейнеров, поэтому вы можете использовать образы контейнеров, разработанные для рабочих нагрузок CUDA.

  На этом этапе мы поддерживаем CUDA 9.0. Например можно использовать следующие базовые образы для файла Docker:
  * [NVIDIA/cuda:9.0-Base-ubuntu16.04](https://hub.docker.com/r/nvidia/cuda/)
  * [tensorflow или tensorflow: 1.12.0-GPU-py3](https://hub.docker.com/r/tensorflow/tensorflow)
    
## <a name="yaml-example"></a>Пример YAML

Чтобы добавить группы GPU, можно развернуть группу контейнеров с помощью [YAML-файла](container-instances-multi-container-yaml.md). Скопируйте следующий YAML-файл в новый файл с именем *gpu-deploy-aci.yaml*, а затем сохраните его. Этот YAML-файл создает группу контейнеров с именем *gpucontainergroup*, в которой указывается экземпляр контейнера с графическим процессором K80. Экземпляр запускает пример приложения сложения векторов CUDA. Запросов ресурсов достаточно для запуска рабочей нагрузки.

```YAML
additional_properties: {}
apiVersion: '2018-10-01'
name: gpucontainergroup
properties:
  containers:
  - name: gpucontainer
    properties:
      image: k8s-gcrio.azureedge.net/cuda-vector-add:v0.1
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
          gpu:
            count: 1
            sku: K80
  osType: Linux
  restartPolicy: OnFailure
```

Разверните группу контейнеров с помощью команды [az container create][az-container-create], указав имя YAML-файла для значения параметра `--file`. Необходимо указать имя группы ресурсов и расположение группы контейнеров, которое поддерживает ресурсы GPU, например *eastus*.  

```azurecli
az container create --resource-group myResourceGroup --file gpu-deploy-aci.yaml --location eastus
```

Развертывание занимает несколько минут. Затем контейнер запускается и выполняет операцию сложения векторов CUDA. Для просмотра выходных данных журнала выполните команду [az container logs][az-container-logs]:

```azurecli
az container logs --resource-group myResourceGroup --name gpucontainergroup --container-name gpucontainer
```

Выходные данные:

```Console
[Vector addition of 50000 elements]
Copy input data from the host memory to the CUDA device
CUDA kernel launch with 196 blocks of 256 threads
Copy output data from the CUDA device to the host memory
Test PASSED
Done
```

## <a name="resource-manager-template-example"></a>Пример шаблона Resource Manager

Чтобы развернуть группу контейнеров с ресурсами GPU, также можно воспользоваться [шаблоном Resource Manager](container-instances-multi-container-group.md). Начните с создания файла с именем `gpudeploy.json` и скопируйте в него приведенный ниже код JSON. В этом примере развертывается экземпляр контейнера с графическим процессором V100, который выполняет задание обучения [TensorFlow](https://www.tensorflow.org/versions/r1.1/get_started/mnist/beginners) для [набора данных MNIST](http://yann.lecun.com/exdb/mnist/). Запросов ресурсов достаточно для запуска рабочей нагрузки.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "gpucontainergrouprm",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "variables": {
      "containername": "gpucontainer",
      "containerimage": "microsoft/samples-tf-mnist-demo:gpu"
    },
    "resources": [
      {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2018-10-01",
        "location": "[resourceGroup().location]",
        "properties": {
            "containers": [
            {
              "name": "[variables('containername')]",
              "properties": {
                "image": "[variables('containerimage')]",
                "resources": {
                  "requests": {
                    "cpu": 4.0,
                    "memoryInGb": 12.0,
                    "gpu": {
                        "count": 1,
                        "sku": "V100"
                  }
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "restartPolicy": "OnFailure"
        }
      }
    ]
}
```

Разверните шаблон с помощью команды [az group deployment create][az-group-deployment-create]. Необходимо указать имя группы ресурсов, созданной в регионе с поддержкой ресурсов GPU, например *eastus*.

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file gpudeploy.json
```

Развертывание занимает несколько минут. Затем контейнер запустится и выполнит задание TensorFlow. Для просмотра выходных данных журнала выполните команду [az container logs][az-container-logs]:

```azurecli
az container logs --resource-group myResourceGroup --name gpucontainergrouprm --container-name gpucontainer
```

Выходные данные:

```Console
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
...
Accuracy at step 990: 0.969
Adding run metadata for 999
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Так как использование ресурсов GPU может быть дорогостоящим, следите, чтобы ваши контейнеры не работали в течение длительного времени. Отслеживайте свои контейнеры на портале Azure или проверяйте состояние группы контейнеров с помощью команды [az container show][az-container-show]. Например: 

```azurecli
az container show --resource-group myResourceGroup --name gpucontainergroup --output table
```

Когда завершите работу с созданными экземплярами контейнеров, удалите их с помощью следующих команд:

```azurecli
az container delete --resource-group myResourceGroup --name gpucontainergroup -y
az container delete --resource-group myResourceGroup --name gpucontainergrouprm -y
```

## <a name="next-steps"></a>Дальнейшие действия

* Ознакомьтесь со сведениями о развертывании группы контейнеров с помощью [файла YAML](container-instances-multi-container-yaml.md) или [шаблона Resource Manager](container-instances-multi-container-group.md).
* Ознакомьтесь со статьей [Размеры виртуальных машин, оптимизированных для GPU](../virtual-machines/linux/sizes-gpu.md).


<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
