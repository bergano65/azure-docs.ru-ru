---
title: Глубокое обучение & платформах искусственного интеллекта
titleSuffix: Azure Data Science Virtual Machines
description: Платформы и средства глубокого обучения на виртуальной машине Azure для обработки и анализа данных, включая TensorFlow, PyTorch, keras, Caffe, MXNet, хоровод, Theano, chaining и др.
keywords: средства анализа и обработки данных, виртуальная машина для анализа и обработки данных, средства для анализа и обработки данных, анализ и обработка данных Linux
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/11/2019
ms.author: vijetaj
ms.openlocfilehash: de407f4b4de58abebf87b82ff6f76342a0e9f7e0
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575045"
---
# <a name="deep-learning-and-ai-frameworks"></a>Платформы глубокого обучения и искусственного интеллекта
Виртуальная машина для обработки и [анализа данных](https://aka.ms/dsvm) (DSVM) поддерживает несколько платформ глубокого обучения для создания приложений искусственного интеллекта (ии) с прогнозной аналитикой и функциями, такими как понимание изображений и языков.

Платформы глубокого обучения, доступные в DSVM, включают:

+ TensorFlow
+ PyTorch
+ Keras
+ Caffe
+ Caffe2
+ Torch
+ Сервер модели MXNet
+ MXNet
+ Horovod
+ Theano
+ Chainer
+ Deep Water
+ NVIDIA DIGITS
+ nvidia-smi
+ Сервер модели TensorFlow
+ TensorRT
+ Microsoft Cognitive Toolkit

|Средства&nbsp;DL&nbsp;наDSVM&nbsp;|Windows|Linux|Примечания&nbsp;об использовании|
|---------|:-------------------:|:------------------:|-----|
|[TensorFlow](https://www.tensorflow.org/); | Да (Windows 2016) | Да |Устанавливается в Python 3,5 в [Linux и windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) и Python 3,6 в [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). Примеры записных книжек Jupyter включены в DSVM.<br/><br/>**Чтобы запустить его,** сделайте следующее:<br/>* Терминал: активируйте правильную среду, а затем запустите Python. <br/> Jupyter подключитесь к [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) или [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), затем откройте каталог образцов TensorFlow.  |
|[PyTorch](https://pytorch.org/)| Нет | Да |Устанавливается в [Python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition). Включены образцы записных книжек Jupyter, а примеры находятся в папке/dsvm/Samples/pytorch.    <br/><br/>**Запуск**<br/>* Терминал: активируйте правильную среду, а затем запустите Python.<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux): Подключитесь, а затем откройте каталог PyTorch для примеров.  |
|[Keras](https://keras.io/);| Да | Да |API устанавливается в Python 3,5 в [Linux и windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) и Python 3,6 в [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). [Примеры](https://github.com/fchollet/keras/tree/master/examples)<br/><br/>**Чтобы запустить его,** сделайте следующее:<br/>* Терминал: активируйте правильную среду, а затем запустите Python. <br/> Jupyter загрузите образцы из расположения GitHub, подключитесь к [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) или [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), а затем откройте каталог образцов. |
|[Caffe](https://github.com/caffe2/caffe2) | Нет |Да (Ubuntu)|Caffe устанавливается в `/opt/caffe`.   Примеры приведены в `/opt/caffe/examples`. <br/><br/>**Чтобы запустить его**, используйте X2Go для входа на виртуальную машину, а затем запустите новый терминал и введите:<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>Откроется новое окно браузера с примерами записных книжек. Двоичные файлы устанавливаются в каталог /opt/caffe/build/install/bin.<br/><br/>Для установленной версии Caffe требуется Python 2.7, и она не будет работать с компонентом Python 3.5, активируемым по умолчанию. Чтобы переключиться на Python 2,7 `source activate root` , выполните команду, чтобы переключить среду Anaconda.|
|[Caffe2](https://github.com/caffe2/caffe2) | Нет |Да (Ubuntu)|Caffe2 устанавливается в среду [conda с Python 2.7 (в корень)](dsvm-languages.md#python-linux-and-windows-server-2012-edition). Источник данных находится в `/opt/caffe2`.<br/>Примеры записных книжек поставляются с JupyterHub.<br/><br/>**Чтобы запустить его,** сделайте следующее:<br/>* В окне терминала активируйте [корневую среду Python](dsvm-languages.md#python-linux-and-windows-server-2012-edition), запустите Python и импортируйте Caffe2. <br/> * В JupyterHub подключитесь к [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), а затем перейдите в каталог Caffe2 к примерам записных книжек. Для некоторых записных книжек требуется корневой каталог Caffe2, чтобы их можно было задать в коде Python. Введите /opt/caffe2. |
|[Torch](http://torch.ch/) | Нет |Да (Ubuntu)|Torch устанавливается в `/dsvm/tools/torch`. PyTorch устанавливается в среде Python 2.7 (_корневая среда_), а также в Python 3.5 (_py35_). Примеры Torch в `/dsvm/samples/torch` и PyTorch `/dsvm/samples/pytorch`. |
|[MXNet](https://mxnet.io/) | Да (Windows 2016) | Да|MXNet устанавливается в `C:\dsvm\tools\mxnet` в Windows и `/dsvm/tools/mxnet` в Linux. Привязки Python устанавливаются с Python 3.5 на [Linux и Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition), а с Python 3.6 на [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). Привязки R также устанавливаются в Ubuntu.<br/><br/>Сюда входят примеры записных книжек Jupyter. <br/><br/>**Чтобы запустить его,** сделайте следующее:<br/>Терминалов Активируйте правильную среду, а затем запустите Python. <br/> Jupyter подключитесь к [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) или [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), затем откройте каталог образцов mxnet.|
|[Сервер моделей MXNet](https://github.com/awslabs/mxnet-model-server) | Нет | Да |Сервер для создания конечных точек HTTP для моделей MXNet и ONNX. _mxnet-model-server_ доступен в терминале. Примеры на [странице сервера модели MXNet](https://github.com/awslabs/mxnet-model-server).|
|[хоровод](https://github.com/uber/horovod) | Нет | Да (Ubuntu) |Распределенная платформа глубокого обучения для TensorFlow. Horovod устанавливается с Python 3.5 на [Ubuntu](dsvm-languages.md#python-linux-and-windows-server-2012-edition).  [См. примеры](https://github.com/uber/horovod/tree/master/examples)<br/><br/>**Чтобы запустить его**, активируйте нужную среду в терминале, а затем запустите Python. |
|[Theano](https://github.com/Theano/Theano) | Нет | Да (Ubuntu) |Theano устанавливается в среде Python 2.7 (_корневая среда_), а также в Python 3.5 (_py35_).<br/><br/>**Чтобы запустить его,** сделайте следующее: <br/>Терминалов Активируйте нужную версию Python (root или PY35), запустите Python, а затем импортируйте theano.<br/>Jupyter Выберите ядро Python 2,7 или 3,5, а затем импортируйте theano.  <br/>Чтобы обойти недавно выявленную ошибку MKL, необходимо сначала установить потоковый уровень MKL.<br/><br/>_export MKL_THREADING_LAYER=GNU_|
|[Chainer](https://chainer.org/) |Нет | Да |Chainer устанавливается с [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition). ChainerRL и ChainerCV также устанавливаются. <br/><br/>Примеры записных книжек поставляются с JupyterHub.<br/><br/>**Чтобы запустить его,** сделайте следующее: <br/>Терминалов Активируйте среду [python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) , запустите _Python_, а затем импортируйте формирователь цепочки. <br/> JupyterHub [Подключитесь к JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), а затем перейдите в каталог Chain, чтобы найти примеры записных книжек.|
|[Цифры NVidia](https://github.com/NVIDIA/DIGITS) | Нет | Да (Ubuntu) |Система глубокого обучения от NVIDIA для быстрого обучения моделей глубокого обучения. DIGITS устанавливается в `/dsvm/tools/DIGITS` и доступна в виде службы, которая также называется _digits_.  <br/><br/>**Чтобы запустить его,** сделайте следующее: <br/>Войдите на виртуальную машину с помощью X2Go. Запустите службу ```sudo systemctl start digits```в терминале. <br/><br/>Запуск службы займет около одной минуты. Запустите браузер и перейдите по адресу `http://localhost:5000`. Обратите внимание, что номер порта не обеспечивает безопасный вход в систему и не должен использоваться за пределами виртуальной машины.|
|[CUDA, cuDNN, драйвер NVIDIA](https://developer.nvidia.com/cuda-toolkit) |Да | Да | |
|nvidia-smi|Да | Да |Средство NVIDIA для запроса действий GPU. _nvidia-smi_ можно найти в системном пути. <br/><br/>Откройте командную строку (в Windows) или терминал (в Linux), а затем запустите _nvidia-smi_.|
|[TensorFlow Serving](https://www.tensorflow.org/serving/) | Нет | Да |Сервер для вывода в модели TensorFlow. _tensorflow_model_server_ доступен в терминале. Примеры можно найти в [Интернете](https://www.tensorflow.org/serving/).|
|[тенсоррт](https://developer.nvidia.com/tensorrt) |  Нет | Да (Ubuntu) |Сервер вывода для глубокого обучения NVIDIA. TensorRT устанавливается как пакет _apt_. Примеры можно найти в [Интернете](https://docs.nvidia.com/deeplearning/sdk/tensorrt-developer-guide/index.html#samples).|
|[Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/)|Да | Да | Устанавливается в Python 3,5 в [Linux и windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) и Python 3,6 в [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). Примеры записных книжек Jupyter включены в DSVM. <br/><br/>**Чтобы запустить его,** сделайте следующее: <br/>Терминалов Активируйте правильную среду и запустите Python. <br/>Jupyter подключитесь к [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) или [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), затем откройте каталог образцов CNTK. |
|Deep Water|Нет | Да (Ubuntu) |Платформа глубокого обучения для H2O, глубокая вода установлена в [Python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) , а также доступна `/dsvm/tools/deep_water`в. Примеры записных книжек поставляются с JupyterHub. Для работы Deep Water требуется CUDA 8 с cuDNN 5.1. Этот вариант не совпадает с путем к библиотеке, заданным по умолчанию, так как другие платформы глубокого обучения используют CUDA 9 и cuDNN 7. Чтобы использовать CUDA 8 + cuDNN 5.1 для Deep Water, необходимо следующее:<br/><br/>```export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:${LD_LIBRARY_PATH}```<br/>```export CUDA_ROOT=/usr/local/cuda-8.0```<br/><br/>Для использования Deep Water необходимо следующее:<br/>* Терминал: активируйте среду [python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) , а затем запустите _Python_. <br/>* JupyterHub: [подключитесь к JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), а затем перейдите в каталог deep_water, чтобы найти примеры записных книжек.|
