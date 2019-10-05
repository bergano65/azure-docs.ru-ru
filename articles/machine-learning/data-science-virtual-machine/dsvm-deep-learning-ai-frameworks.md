---
title: Глубокое обучение & платформах искусственного интеллекта
titleSuffix: Azure Data Science Virtual Machine
description: Доступные платформы и средства глубокого обучения на виртуальной машине Azure для обработки и анализа данных.
keywords: средства анализа и обработки данных, виртуальная машина для анализа и обработки данных, средства для анализа и обработки данных, анализ и обработка данных Linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: conceptual
ms.date: 10/1/2019
ms.openlocfilehash: db95788b0f2c041157bdc16000d0328c042e86d5
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973692"
---
# <a name="deep-learning-and-ai-frameworks-for-the-azure-data-science-vm"></a>Платформы глубокого обучения и искусственного интеллекта для виртуальной машины Azure для обработки и анализа данных
Платформы глубокого обучения в DSVM перечислены ниже.

## <a name="caffehttpsgithubcombvlccaffe"></a>[Caffe](https://github.com/BVLC/caffe)

|    |           |
| ------------- | ------------- |
| Поддерживаемые версии | |
| Поддерживаемые выпуски DSVM      | Linux (Ubuntu)     |
| Настройка и установка на DSVM  | Caffe устанавливается в `/opt/caffe`.   Примеры находятся в `/opt/caffe/examples`.|
| Как запустить      | Используйте X2Go для входа в виртуальную машину, а затем запустите новый терминал и введите следующее:<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>Откроется новое окно браузера с примерами записных книжек. Двоичные файлы устанавливаются в каталог /opt/caffe/build/install/bin.<br/><br/>Для установленной версии Caffe требуется Python 2,7 и не будет работать с Python 3,5, которая активируется по умолчанию. Чтобы переключиться на Python 2,7, выполните `source activate root`, чтобы перейти в среду Anaconda.|    

## <a name="caffe2httpsgithubcomcaffe2caffe2"></a>[Caffe2](https://github.com/caffe2/caffe2)

|    |           |
| ------------- | ------------- |
| Поддерживаемые версии | |
| Поддерживаемые выпуски DSVM      | Linux (Ubuntu)     |
| Настройка и установка на DSVM  | Caffe2 устанавливается в среде [Python 2,7 (root) conda. |
| Как запустить      | Терминалов Запустите Python и импортируйте Caffe2. <br/> JupyterHub [Подключитесь к JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), а затем перейдите в каталог Caffe2, чтобы найти примеры записных книжек. Для некоторых записных книжек требуется корневой каталог Caffe2, чтобы их можно было задать в коде Python. Введите /opt/caffe2. |

## <a name="chainerhttpschainerorg"></a>[Chainer](https://chainer.org/)

|    |           |
| ------------- | ------------- |
| Поддерживаемые версии | 5,2 |
| Поддерживаемые выпуски DSVM      | Linux (Ubuntu)     |
| Настройка и установка на DSVM  | Формирователь цепочки устанавливается в Python 3,5. |
| Как запустить      | Терминалов Активируйте среду Python 3,5, выполните `python`, а затем `import chainer`. <br/> JupyterHub [Подключитесь к JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), а затем перейдите в каталог Chain, чтобы найти примеры записных книжек.| 

## <a name="cuda-cudnn-nvidia-driverhttpsdevelopernvidiacomcuda-toolkit"></a>[CUDA, cuDNN, драйвер NVIDIA](https://developer.nvidia.com/cuda-toolkit)

|    |           |
| ------------- | ------------- |
| Поддерживаемые версии | 10.0.130|
| Поддерживаемые выпуски DSVM      | Windows и Linux   |
| Настройка и установка на DSVM  |_nvidia-smi_ можно найти в системном пути.  |
| Как запустить      | Откройте командную строку (в Windows) или терминал (в Linux), а затем запустите _NVIDIA-SMI_. |


## <a name="horovodhttpsgithubcomuberhorovod"></a>[хоровод](https://github.com/uber/horovod)

|    |           |
| ------------- | ------------- |
| Поддерживаемые версии | 0.16.1|
| Поддерживаемые выпуски DSVM      | Linux (Ubuntu)   |
| Настройка и установка на DSVM  | Хоровод устанавливается в Python 3,5 |
| Как запустить      | Активируйте нужную среду в терминале, а затем запустите Python. |

## <a name="kerashttpskerasio"></a>[Keras](https://keras.io/);

|    |           |
| ------------- | ------------- |
| Поддерживаемые версии | 2.2.4 |
| Поддерживаемые выпуски DSVM      | Windows и Linux   |
| Настройка и установка на DSVM  | Keras устанавливается в Python 3,6 в Windows и в Python 3,5 в Linux |
| Как запустить      | Активируйте нужную среду в терминале, а затем запустите Python. |

## <a name="microsoft-cognitive-toolkit-cntkhttpsdocsmicrosoftcomcognitive-toolkit"></a>[Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/)

|    |           |
| ------------- | ------------- |
| Поддерживаемые версии | 2.5.1 |
| Поддерживаемые выпуски DSVM      | Windows и Linux   |
| Настройка и установка на DSVM  | CNTK устанавливается в Python 3,6 в [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition) и в Python 3,5 в [Linux](./dsvm-languages.md#python-linux-edition)) |
| Как запустить      | Терминалов Активируйте правильную среду и запустите Python. <br/>Jupyter Подключитесь к [Jupyter](provision-vm.md) или [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), а затем откройте каталог CNTK для примеров. |

## <a name="mxnethttpsmxnetapacheorg"></a>[MXNet](https://mxnet.apache.org/)
|    |           |
| ------------- | ------------- |
| Поддерживаемые версии | 1.3.0 |
| Поддерживаемые выпуски DSVM      | Windows и Linux   |
| Настройка и установка на DSVM  | MXNet устанавливается в `C:\dsvm\tools\mxnet` в Windows и `/dsvm/tools/mxnet` в Ubuntu. Привязки Python устанавливаются в Python 3,6 в [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition) и в Python 3,5 в [Linux](./dsvm-languages.md#python-linux-edition)) привязки R также входят в DSVM Ubuntu. |
| Как запустить      | Терминалов Активируйте правильную среду conda, а затем запустите `import mxnet`. <br/>Jupyter Подключитесь к [Jupyter](provision-vm.md#access-the-dsvm) или [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), а затем откройте каталог `mxnet` для примеров. |

## <a name="mxnet-model-serverhttpsgithubcomawslabsmxnet-model-serverquick-start"></a>[Сервер моделей MXNet](https://github.com/awslabs/mxnet-model-server#quick-start)

|    |           |
| ------------- | ------------- |
| Поддерживаемые версии | 1.0.1 |
| Поддерживаемые выпуски DSVM      | Windows и Linux   |
| Настройка и установка на DSVM  | MXNet Model Server устанавливается в Python 3,6 в [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition) и в Python 3,5 в [Linux](./dsvm-languages.md#python-linux-edition)). |
| Как запустить      | Терминалов Запустите `sudo systemctl stop jupyterhub`, чтобы сначала запустить службу JupyterHub, так как оба прослушивают один и тот же порт. Затем активируйте правильную среду conda и запустите `mxnet-model-server --start --models squeezenet=https://s3.amazonaws.com/model-server/model_archive_1.0/squeezenet_v1.1.mar`. |

## <a name="nvidia-system-management-interface-nvidia-smihttpsdevelopernvidiacomnvidia-system-management-interface"></a>[Интерфейс управления системами NVidia (NVIDIA-SMI)](https://developer.nvidia.com/nvidia-system-management-interface)

|    |           |
| ------------- | ------------- |
| Поддерживаемые версии |  |
| Поддерживаемые выпуски DSVM      | Windows и Linux   |
| Для чего? | Средство NVIDIA для выполнения запросов активности GPU |
| Настройка и установка на DSVM  | `nvidia-smi` находится в системном пути. |
| Как запустить      | На виртуальной машине **с графическим процессором**откройте командную строку (в Windows) или терминал (в Linux), а затем запустите `nvidia-smi`. |

## <a name="pytorchhttpspytorchorg"></a>[PyTorch](https://pytorch.org/)

|    |           |
| ------------- | ------------- |
| Поддерживаемые версии | 1.2.0 |
| Поддерживаемые выпуски DSVM      | Linux |
| Настройка и установка на DSVM  | Устанавливается в [Python 3,5](dsvm-languages.md#python-linux-edition). Включены образцы записных книжек Jupyter, а примеры находятся в папке/dsvm/Samples/pytorch. |
| Как запустить      | Терминалов Активируйте правильную среду, а затем запустите Python.<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine): Подключитесь, а затем откройте каталог PyTorch для примеров.  |

## <a name="tensorflowhttpswwwtensorfloworg"></a>[TensorFlow](https://www.tensorflow.org/);

|    |           |
| ------------- | ------------- |
| Поддерживаемые версии | 1,13 |
| Поддерживаемые выпуски DSVM      | Windows, Linux |
| Настройка и установка на DSVM  | Устанавливается в Python 3,5 в [Linux](dsvm-languages.md#python-linux-edition) и Python 3,6 в [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition) |
| Как запустить      | Терминалов Активируйте правильную среду, а затем запустите Python. <br/> Jupyter Подключитесь к [Jupyter](provision-vm.md) или [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), а затем откройте каталог TensorFlow для примеров.   |

## <a name="tensorflow-servinghttpswwwtensorfloworgserving"></a>[TensorFlow Serving](https://www.tensorflow.org/serving/)

|    |           |
| ------------- | ------------- |
| Поддерживаемые версии | 1,12 |
| Поддерживаемые выпуски DSVM      | Linux |
| Настройка и установка на DSVM  | tensorflow_model_server доступен в окне терминала. |
| Как запустить      |  Примеры можно найти в [Интернете](https://www.tensorflow.org/serving/).   |


## <a name="theanohttpsgithubcomtheanotheano"></a>[Theano](https://github.com/Theano/Theano)

|    |           |
| ------------- | ------------- |
| Поддерживаемые версии | 1.0.3 |
| Поддерживаемые выпуски DSVM      | Linux |
| Настройка и установка на DSVM  |Theano устанавливается в среде Python 2,7 (_root_) и в окружении Python 3,5 (_PY35_). |
| Как запустить      |  Терминалов Активируйте нужную версию Python (root или PY35), запустите Python, а затем импортируйте Theano.<br/>Jupyter Выберите ядро Python 2,7 или 3,5, а затем импортируйте Theano.  <br/>Чтобы обойти недавную ошибку математической библиотеки ядра (MKL), необходимо сначала установить MKL потоковый уровень следующим образом:<br/><br/>`export MKL_THREADING_LAYER=GNU`  |