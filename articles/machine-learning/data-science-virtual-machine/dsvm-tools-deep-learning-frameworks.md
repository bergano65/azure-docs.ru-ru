---
title: Рамки глубокого обучения & ИИ
titleSuffix: Azure Data Science Virtual Machine
description: Доступные рамки глубокого обучения и инструменты на виртуальной машине Azure Data Science.
keywords: средства анализа и обработки данных, виртуальная машина для анализа и обработки данных, средства для анализа и обработки данных, анализ и обработка данных Linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: d8a5cf428f41b130e6faf68ac87a075c15211099
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270073"
---
# <a name="deep-learning-and-ai-frameworks-for-the-azure-data-science-vm"></a>Платформы глубокого обучения и ИИ для VM Azure Data Science
Ниже перечислены рамки глубокого обучения на DSVM.

## <a name="caffe"></a>[Caffe](https://github.com/BVLC/caffe)

|    |           |
| ------------- | ------------- |
| Версия (ы) поддерживается | |
| Поддерживаемые издания DSVM      | Linux (Ubuntu)     |
| Настройка и установка на DSVM  | Caffe устанавливается в `/opt/caffe`.   Образцы `/opt/caffe/examples`находятся в .|
| Как запустить его      | используйте X2Go, чтобы войти в в ваш VM, а затем запустить новый терминал и ввести следующее:<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>Откроется новое окно браузера с примерами записных книжек. Двоичные файлы устанавливаются в каталог /opt/caffe/build/install/bin.<br/><br/>Установленная версия Caffe требует Python 2.7 и не будет работать с Python 3.5, который активируется по умолчанию. Чтобы переключиться на Python `source activate root` 2.7, запустите, чтобы переключиться на среду Anaconda.|    

## <a name="caffe2"></a>[Caffe2](https://github.com/caffe2/caffe2)

|    |           |
| ------------- | ------------- |
| Версия (ы) поддерживается | |
| Поддерживаемые издания DSVM      | Linux (Ubuntu)     |
| Настройка и установка на DSVM  | Caffe2 устанавливается в среде конды «Python 2.7 ( root). |
| Как запустить его      | Терминал: Запуск Python и импорт Caffe2. <br/> JupyterHub: [Подключитесь к JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), а затем перейдите в каталог Caffe2, чтобы найти образцы ноутбуков. Для некоторых записных книжек требуется корневой каталог Caffe2, чтобы их можно было задать в коде Python. Введите /opt/caffe2. |

## <a name="chainer"></a>[Chainer](https://chainer.org/)

|    |           |
| ------------- | ------------- |
| Версия (ы) поддерживается | 5,2 |
| Поддерживаемые издания DSVM      | Linux (Ubuntu)     |
| Настройка и установка на DSVM  | Chainer устанавливается с Python 3.5. |
| Как запустить его      | Терминал: Активируйте среду Python 3.5, запустите, `python`а затем `import chainer`. <br/> JupyterHub: [Подключитесь к JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), а затем перейдите в каталог Chainer, чтобы найти образцы ноутбуков.| 

## <a name="cuda-cudnn-nvidia-driver"></a>[CUDA, cuDNN, драйвер NVIDIA](https://developer.nvidia.com/cuda-toolkit)

|    |           |
| ------------- | ------------- |
| Версия (ы) поддерживается | 10.0.130|
| Поддерживаемые издания DSVM      | Windows и Linux   |
| Настройка и установка на DSVM  |_nvidia-smi_ можно найти в системном пути.  |
| Как запустить его      | Откройте запрос команды (на Windows) или терминал (на Linux), а затем запустите _nvidia-smi._ |


## <a name="horovod"></a>[Horovod](https://github.com/uber/horovod)

|    |           |
| ------------- | ------------- |
| Версия (ы) поддерживается | 0.16.1|
| Поддерживаемые издания DSVM      | Linux (Ubuntu)   |
| Настройка и установка на DSVM  | Горвод установлен в Python 3.5 |
| Как запустить его      | Активируйте правильную среду в терминале, а затем запустите Python. |

## <a name="keras"></a>[Keras](https://keras.io/)

|    |           |
| ------------- | ------------- |
| Версия (ы) поддерживается | 2.2.4 |
| Поддерживаемые издания DSVM      | Windows и Linux   |
| Настройка и установка на DSVM  | Keras установлен в Python 3.6 на Windows и в Python 3.5 в Linux |
| Как запустить его      | Активируйте правильную среду в терминале, а затем запустите Python. |

## <a name="microsoft-cognitive-toolkit-cntk"></a>[Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/)

|    |           |
| ------------- | ------------- |
| Версия (ы) поддерживается | 2.5.1 |
| Поддерживаемые издания DSVM      | Windows и Linux   |
| Настройка и установка на DSVM  | CNTK установлен в Python 3.6 на [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) и в Python 3.5 на [Linux](./dsvm-tools-languages.md#python-linux-edition)) |
| Как запустить его      | Терминал: Активируйте правильную среду и запустите Python. <br/>Jupyter: Подключитесь к [Jupyter](provision-vm.md) или [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), а затем откройте каталог CNTK для образцов. |

## <a name="mxnet"></a>[MXNet](https://mxnet.apache.org/)
|    |           |
| ------------- | ------------- |
| Версия (ы) поддерживается | 1.3.0 |
| Поддерживаемые издания DSVM      | Windows и Linux   |
| Настройка и установка на DSVM  | MXNet устанавливается `C:\dsvm\tools\mxnet` на `/dsvm/tools/mxnet` Windows и на Ubuntu. Привязки Python установлены в Python 3.6 на [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) и в Python 3.5 на [Linux](./dsvm-tools-languages.md#python-linux-edition)) R привязки также включены в Ubuntu DSVM. |
| Как запустить его      | Терминал: Активируйте правильную среду conda, затем запустите. `import mxnet` <br/>Jupyter: Подключитесь к [Jupyter](provision-vm.md#access-the-dsvm) или [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), а затем откройте `mxnet` каталог для образцов. |

## <a name="mxnet-model-server"></a>[Сервер модели MXNet](https://github.com/awslabs/mxnet-model-server#quick-start)

|    |           |
| ------------- | ------------- |
| Версия (ы) поддерживается | 1.0.1 |
| Поддерживаемые издания DSVM      | Windows и Linux   |
| Настройка и установка на DSVM  | MXNet Модельный сервер установлен в Python 3.6 на [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) и в Python 3.5 на [Linux](./dsvm-tools-languages.md#python-linux-edition)) |
| Как запустить его      | Терминал: `sudo systemctl stop jupyterhub` Выполнить, чтобы остановить jupyterHub службы во-первых, потому что оба слушают на том же порту. Затем активируйте правильную среду конды и забеги`mxnet-model-server --start --models squeezenet=https://s3.amazonaws.com/model-server/model_archive_1.0/squeezenet_v1.1.mar` |

## <a name="nvidia-system-management-interface-nvidia-smi"></a>[Интерфейс управления системой NVidia (nvidia-smi)](https://developer.nvidia.com/nvidia-system-management-interface)

|    |           |
| ------------- | ------------- |
| Версия (ы) поддерживается |  |
| Поддерживаемые издания DSVM      | Windows и Linux   |
| Для чего она нужна? | Средство NVIDIA для выполнения запросов активности GPU |
| Настройка и установка на DSVM  | `nvidia-smi`находится на пути системы. |
| Как запустить его      | На виртуальной машине **с графическим процессором,** открыть запрос команды (на Windows) `nvidia-smi`или терминал (на Linux), а затем запустить . |

## <a name="pytorch"></a>[PyTorch](https://pytorch.org/)

|    |           |
| ------------- | ------------- |
| Версия (ы) поддерживается | 1.2.0 (Ubuntu 16.04, Windows 2016), 1.4.0 (Ubuntu 18.04, Windows 2019) |
| Поддерживаемые издания DSVM      | Linux |
| Настройка и установка на DSVM  | Устанавливается в [Python 3.5](dsvm-tools-languages.md#python-linux-edition). Пример ы Jupyter ноутбуки включены, и образцы в /dsvm / образцы / pytorch. |
| Как запустить его      | Терминал: Активируйте правильную среду, а затем запустите Python.<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine): Подключите, а затем открыть каталог PyTorch для образцов.  |

## <a name="tensorflow"></a>[TensorFlow](https://www.tensorflow.org/)

|    |           |
| ------------- | ------------- |
| Версия (ы) поддерживается | 1.13 |
| Поддерживаемые издания DSVM      | Windows, Linux |
| Настройка и установка на DSVM  | Устанавливается в Python 3.5 на [Linux](dsvm-tools-languages.md#python-linux-edition) и Python 3.6 на [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) |
| Как запустить его      | Терминал: Активируйте правильную среду, а затем запустите Python. <br/> Jupyter: Подключитесь к [Jupyter](provision-vm.md) или [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), а затем откройте каталог TensorFlow для образцов.   |

## <a name="tensorflow-serving"></a>[TensorFlow Serving](https://www.tensorflow.org/serving/)

|    |           |
| ------------- | ------------- |
| Версия (ы) поддерживается | 1.12 |
| Поддерживаемые издания DSVM      | Linux |
| Настройка и установка на DSVM  | tensorflow_model_server доступен в терминале. |
| Как запустить его      |  Примеры можно найти в [Интернете](https://www.tensorflow.org/serving/).   |


## <a name="theano"></a>[Theano](https://github.com/Theano/Theano)

|    |           |
| ------------- | ------------- |
| Версия (ы) поддерживается | 1.0.3 |
| Поддерживаемые издания DSVM      | Linux |
| Настройка и установка на DSVM  |Theano устанавливается в Python 2.7 _(корень),_ и в Python 3.5 _(py35)_ окружающей среды. |
| Как запустить его      |  Терминал: Активируйте нужную версию Python (корень или py35), запустите Python, а затем импортируйте Theano.<br/>Jupyter: Выберите ядро Python 2.7 или 3.5, а затем импортируйте Theano.  <br/>Чтобы обойти недавнюю ошибку в библиотеке ядра математики (MKL), необходимо сначала установить слой резьбы MKL следующим образом:<br/><br/>`export MKL_THREADING_LAYER=GNU`  |