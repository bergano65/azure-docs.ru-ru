---
title: Глубокое обучение & платформах искусственного интеллекта
titleSuffix: Azure Data Science Virtual Machine
description: Доступные платформы и средства глубокого обучения на виртуальной машине Azure для обработки и анализа данных, включая TensorFlow, PyTorch, keras, Caffe, MXNet, хоровод, Theano, chaining и др.
keywords: средства анализа и обработки данных, виртуальная машина для анализа и обработки данных, средства для анализа и обработки данных, анализ и обработка данных Linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 08/11/2019
ms.openlocfilehash: f71a2cc5c0a430037b429aac4f3ec4dc5f5f933d
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208105"
---
# <a name="deep-learning-and-ai-frameworks-for-azure-data-science-vm"></a>Платформы глубокого обучения и искусственного интеллекта для виртуальной машины Azure для обработки и анализа данных
Виртуальная машина для обработки и [анализа данных](https://aka.ms/dsvm) (DSVM) поддерживает несколько платформ глубокого обучения для создания приложений искусственного интеллекта (ии) с прогнозной аналитикой и функциями, такими как понимание изображений и языков.

Платформы глубокого обучения, доступные через DSVM, включают:

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

|&nbsp;Средства&nbsp;глубокогообученияна&nbsp;DSVM|Windows|Linux|Примечания&nbsp;об использовании|
|---------|-------------------|------------------|-----|
|[TensorFlow](https://www.tensorflow.org/); | Да (Windows 2016) | Да |Устанавливается в Python 3,5 в [Linux и windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) и Python 3,6 в [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). Примеры записных книжек Jupyter включены в DSVM.<br/><br/>**Чтобы запустить его,** сделайте следующее:<br/>Терминалов Активируйте правильную среду, а затем запустите Python. <br/> Jupyter Подключитесь к [Jupyter](provision-vm.md) или [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), а затем откройте каталог TensorFlow для примеров.  |
|[PyTorch](https://pytorch.org/)| Нет | Да |Устанавливается в [Python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition). Включены образцы записных книжек Jupyter, а примеры находятся в папке/dsvm/Samples/pytorch.    <br/><br/>**Чтобы запустить его,** сделайте следующее:<br/>Терминалов Активируйте правильную среду, а затем запустите Python.<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux): Подключитесь, а затем откройте каталог PyTorch для примеров.  |
|[Keras](https://keras.io/);| Да | Да |API устанавливается в Python 3,5 в [Linux и windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) и в Python 3,6 в [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). [См. примеры](https://github.com/fchollet/keras/tree/master/examples).<br/><br/>**Чтобы запустить его,** сделайте следующее:<br/>Терминалов Активируйте правильную среду, а затем запустите Python. <br/> Jupyter Скачайте образцы из расположения GitHub, подключитесь к [Jupyter](provision-vm.md) или [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), а затем откройте пример каталога. |
|[Caffe](https://github.com/caffe2/caffe2) | Нет |Да (Ubuntu)|Caffe устанавливается в `/opt/caffe`.   Примеры приведены в `/opt/caffe/examples`. <br/><br/>**Чтобы запустить его**, используйте X2Go для входа в виртуальную машину, а затем запустите новый терминал и введите следующее:<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>Откроется новое окно браузера с примерами записных книжек. Двоичные файлы устанавливаются в каталог /opt/caffe/build/install/bin.<br/><br/>Для установленной версии Caffe требуется Python 2,7 и не будет работать с Python 3,5, которая активируется по умолчанию. Чтобы переключиться на Python 2,7 `source activate root` , выполните команду, чтобы переключиться в среду Anaconda.|
|[Caffe2](https://github.com/caffe2/caffe2) | Нет |Да (Ubuntu)|Caffe2 устанавливается в среду [conda с Python 2.7 (в корень)](dsvm-languages.md#python-linux-and-windows-server-2012-edition). Источник данных находится в `/opt/caffe2`.<br/>Примеры записных книжек поставляются с JupyterHub.<br/><br/>**Чтобы запустить его,** сделайте следующее:<br/>Терминалов Активируйте [корневую среду Python](dsvm-languages.md#python-linux-and-windows-server-2012-edition), запустите Python и импортируйте Caffe2. <br/> JupyterHub [Подключитесь к JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), а затем перейдите в каталог Caffe2, чтобы найти примеры записных книжек. Для некоторых записных книжек требуется корневой каталог Caffe2, чтобы их можно было задать в коде Python. Введите /opt/caffe2. |
|[Torch](http://torch.ch/) | Нет |Да (Ubuntu)|Torch устанавливается в `/dsvm/tools/torch`. PyTorch устанавливается в среде Python 2.7 (_корневая среда_), а также в Python 3.5 (_py35_). Примеры Torch в `/dsvm/samples/torch` и PyTorch `/dsvm/samples/pytorch`. |
|[MXNet](https://mxnet.io/) | Да (Windows 2016) | Да|MXNet устанавливается в `C:\dsvm\tools\mxnet` в Windows и `/dsvm/tools/mxnet` в Linux. Привязки Python устанавливаются с Python 3.5 на [Linux и Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition), а с Python 3.6 на [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). Привязки R также устанавливаются в Ubuntu.<br/><br/>Сюда входят примеры записных книжек Jupyter. <br/><br/>**Чтобы запустить его,** сделайте следующее:<br/>Терминалов Активируйте правильную среду, а затем запустите Python. <br/> Jupyter Подключитесь к [Jupyter](provision-vm.md) или [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), а затем откройте каталог mxnet для примеров.|
|[Сервер моделей MXNet](https://github.com/awslabs/mxnet-model-server) | Нет | Да |Сервер для создания конечных точек HTTP для моделей MXNet и ONNX. _Mxnet-Model-Server_ доступен в терминале. Примеры на [странице сервера модели MXNet](https://github.com/awslabs/mxnet-model-server).|
|[хоровод](https://github.com/uber/horovod) | Нет | Да (Ubuntu) |Распределенная платформа глубокого обучения для TensorFlow. Horovod устанавливается с Python 3.5 на [Ubuntu](dsvm-languages.md#python-linux-and-windows-server-2012-edition).  [См. примеры](https://github.com/uber/horovod/tree/master/examples).<br/><br/>**Чтобы запустить его**, активируйте нужную среду в терминале, а затем запустите Python. |
|[Theano](https://github.com/Theano/Theano) | Нет | Да (Ubuntu) |Theano устанавливается в среде Python 2,7 (_root_) и в окружении Python 3,5 (_PY35_).<br/><br/>**Чтобы запустить его,** сделайте следующее: <br/>Терминалов Активируйте нужную версию Python (root или PY35), запустите Python, а затем импортируйте Theano.<br/>Jupyter Выберите ядро Python 2,7 или 3,5, а затем импортируйте Theano.  <br/>Чтобы обойти недавную ошибку математической библиотеки ядра (MKL), необходимо сначала установить MKL потоковый уровень следующим образом:<br/><br/>`export MKL_THREADING_LAYER=GNU`|
|[Chainer](https://chainer.org/) |Нет | Да |Chainer устанавливается с [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition). ChainerRL и ChainerCV также устанавливаются. <br/><br/>Примеры записных книжек поставляются с JupyterHub.<br/><br/>**Чтобы запустить его,** сделайте следующее: <br/>Терминалов Активируйте среду [python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) , запустите _Python_, а затем импортируйте формирователь цепочки. <br/> JupyterHub [Подключитесь к JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), а затем перейдите в каталог Chain, чтобы найти примеры записных книжек.|
|[Цифры NVidia](https://github.com/NVIDIA/DIGITS) | Нет | Да (Ubuntu) |Система глубокого обучения от NVIDIA для быстрого обучения моделей глубокого обучения. Цифры устанавливаются в `/dsvm/tools/DIGITS` и доступны в виде службы с именем _digits_.  <br/><br/>**Чтобы запустить его,** сделайте следующее: <br/>Войдите на виртуальную машину с помощью X2Go. Запустите службу в терминале, выполнив ```sudo systemctl start digits```. <br/><br/>Запуск службы займет около одной минуты. Откройте веб-браузер и перейдите на страницу `http://localhost:5000`. Обратите внимание, что номер порта не обеспечивает безопасный вход в систему и не должен использоваться за пределами виртуальной машины.|
|[CUDA, cuDNN, драйвер NVIDIA](https://developer.nvidia.com/cuda-toolkit) |Да | Да | |
|nvidia-smi|Да | Да |Инструмент NVIDIA для запроса действий GPU; _NVIDIA — SMI_ можно найти по системному пути. <br/><br/>Откройте командную строку (в Windows) или терминал (в Linux), а затем запустите _NVIDIA-SMI_.|
|[TensorFlow Serving](https://www.tensorflow.org/serving/) | Нет | Да |Сервер для вывода в модели TensorFlow; tensorflow_model_server доступен в окне терминала. Примеры можно найти в [Интернете](https://www.tensorflow.org/serving/).|
|[тенсоррт](https://developer.nvidia.com/tensorrt) |  Нет | Да (Ubuntu) |Сервер вывода с поддержкой глубокого обучения от NVIDIA. TensorRT устанавливается как пакет _apt_. Примеры можно найти в [Интернете](https://docs.nvidia.com/deeplearning/sdk/tensorrt-developer-guide/index.html#samples).|
|[Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/)|Да | Да | Устанавливается в Python 3,5 в [Linux и windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) и Python 3,6 в [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). Примеры записных книжек Jupyter включены в DSVM. <br/><br/>**Чтобы запустить его,** сделайте следующее: <br/>Терминалов Активируйте правильную среду и запустите Python. <br/>Jupyter Подключитесь к [Jupyter](provision-vm.md) или [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), а затем откройте каталог CNTK для примеров. |
|Deep Water|Нет | Да (Ubuntu) |Платформа глубокого обучения для H2O — это глубокая вода, установленная в [Python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) , также `/dsvm/tools/deep_water`доступна в. Примеры записных книжек поставляются с JupyterHub. Для работы Deep Water требуется CUDA 8 с cuDNN 5.1. По умолчанию это не путь к библиотеке, так как в других платформах глубокого обучения используются CUDA 9 и cuDNN 7. Чтобы использовать, установите CUDA 8 + cuDNN 5,1 для глубокой воды:<br/><br/>```export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:${LD_LIBRARY_PATH}```<br/>```export CUDA_ROOT=/usr/local/cuda-8.0```<br/><br/>Для использования Deep Water необходимо следующее:<br/>Терминалов Активируйте среду [python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) , а затем запустите _Python_. <br/>JupyterHub [Подключитесь к JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), а затем перейдите в каталог deep_water, чтобы найти примеры записных книжек.|
