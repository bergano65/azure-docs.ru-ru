---
title: Руководство по обучению первых моделей машинного обучения — Python
titleSuffix: Azure Machine Learning
description: В третьей части серии по началу работы с Машинным обучением Azure описано, как обучить модель машинного обучения.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: a267231dd447b114c69e6ead20c8ab5252f85d0e
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90896743"
---
# <a name="tutorial-train-your-first-machine-learning-model-part-3-of-4"></a>Руководство по обучению первых моделей машинного обучения (часть 3 из 4)

В этом учебнике описано, как обучить модель машинного обучения в Машинном обучении Azure.

Этот учебник является **третьей частью в учебной серии из четырех частей**, в рамках которой вы ознакомитесь с основами Машинного обучения Azure и выполните задачи машинного обучения на основе заданий в Azure. Этот учебник создан на основе работы, выполненной в рамках [Части 1: настройка](tutorial-1st-experiment-sdk-setup-local.md) и [Части 2: запуск Hello World](tutorial-1st-experiment-hello-world.md).

В рамках этого учебника вы выполните следующий шаг, отправив сценарий, который обучает модель машинного обучения. В этом примере показано, как Машинное обучение Azure позволяет легко добиться согласованного поведения между локальной отладкой и удаленными запусками.

Изучив этот учебник, вы:

> [!div class="checklist"]
> * создадите сценарий обучения;
> * используете Conda для определения среды Машинного обучения Azure;
> * создадите сценарий элемента управления;
> * ознакомитесь с классами Машинного обучения Azure (среда, запуск, метрики);
> * отправите и запустите свой сценарий обучения;
> * просмотрите выходные данные кода в облаке;
> * научитесь вести журнал метрик в Машинном обучении Azure;
> * просмотрите свои метрики в облаке.

## <a name="prerequisites"></a>Предварительные условия

* Выполните инструкции из [части 1](tutorial-1st-experiment-sdk-setup-local.md), если у вас еще нет рабочей области Машинного обучения Azure.
* Вводные знания о языке Python и рабочих процессах машинного обучения.
* Локальная среда разработки. Сюда входят Visual Studio Code, Jupyter или PyCharm, но этот перечень не исчерпывающий.
* Python (версии 3.5–3.7).

## <a name="create-training-scripts"></a>Создание сценариев обучения

Сначала определите архитектуру нейронной сети в файле `model.py`. Весь обучающий код будет перемещен в подкаталог `src`, включая `model.py`.

Приведенный ниже код взят из [этого вводного примера](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html) из PyTorch. Обратите внимание, что концепции Машинного обучения Azure применяются к любому коду машинного обучения, а не только к PyTorch.

```python
# tutorial/src/model.py
import torch.nn as nn
import torch.nn.functional as F


class Net(nn.Module):
    def __init__(self):
        super(Net, self).__init__()
        self.conv1 = nn.Conv2d(3, 6, 5)
        self.pool = nn.MaxPool2d(2, 2)
        self.conv2 = nn.Conv2d(6, 16, 5)
        self.fc1 = nn.Linear(16 * 5 * 5, 120)
        self.fc2 = nn.Linear(120, 84)
        self.fc3 = nn.Linear(84, 10)

    def forward(self, x):
        x = self.pool(F.relu(self.conv1(x)))
        x = self.pool(F.relu(self.conv2(x)))
        x = x.view(-1, 16 * 5 * 5)
        x = F.relu(self.fc1(x))
        x = F.relu(self.fc2(x))
        x = self.fc3(x)
        return x
```

Далее определите сценарий обучения. Этот сценарий скачивает набор данных CIFAR10 с помощью API-интерфейсов PyTorch `torchvision.dataset`, настраивает сеть, определенную в `model.py`, и обучает ее для двух эпох, используя стандартный SGD и потери с перекрестной энтропией.

Создайте сценарий `train.py` в подкаталоге `src`:

```python
# tutorial/src/train.py
import torch
import torch.optim as optim
import torchvision
import torchvision.transforms as transforms

from model import Net

# download CIFAR 10 data
trainset = torchvision.datasets.CIFAR10(
    root="./data",
    train=True,
    download=True,
    transform=torchvision.transforms.ToTensor(),
)
trainloader = torch.utils.data.DataLoader(
    trainset, batch_size=4, shuffle=True, num_workers=2
)

if __name__ == "__main__":

    # define convolutional network
    net = Net()

    # set up pytorch loss /  optimizer
    criterion = torch.nn.CrossEntropyLoss()
    optimizer = optim.SGD(net.parameters(), lr=0.001, momentum=0.9)

    # train the network
    for epoch in range(2):

        running_loss = 0.0
        for i, data in enumerate(trainloader, 0):
            # unpack the data
            inputs, labels = data

            # zero the parameter gradients
            optimizer.zero_grad()

            # forward + backward + optimize
            outputs = net(inputs)
            loss = criterion(outputs, labels)
            loss.backward()
            optimizer.step()

            # print statistics
            running_loss += loss.item()
            if i % 2000 == 1999:
                loss = running_loss / 2000
                print(f"epoch={epoch + 1}, batch={i + 1:5}: loss {loss:.2f}")
                running_loss = 0.0

    print("Finished Training")

```

Теперь у вас есть структура каталогов, описанная ниже:

```txt
tutorial
└──.azureml
|  └──config.json
└──src
|  └──hello.py
|  └──model.py
|  └──train.py
└──01-create-workspace.py
└──02-create-compute.py
└──03-run-hello.py
```

## <a name="define-a-python-environment"></a>Определение среды Python

В демонстрационных целях мы будем использовать среду Conda (шаги при использовании виртуальной среды PIP практически идентичны).

В скрытом каталоге `.azureml` создайте файл с именем `pytorch-env.yml`:

```yml
# tutorial/.azureml/pytorch-env.yml
name: pytorch-env
channels:
    - defaults
    - pytorch
dependencies:
    - python=3.6.2
    - pytorch
    - torchvision
```

Эта среда имеет все зависимости, необходимые для вашей модели и сценария обучения. Обратите внимание на отсутствие зависимости от пакета SDK Python для Машинного обучения Azure.

## <a name="test-locally"></a>Локальное тестирование

Тестирование сценария выполняется локально с помощью этой среды:

```bash
conda env create -f .azureml/pytorch-env.yml    # create conda environment
conda activate pytorch-env             # activate conda environment
python src/train.py                    # train model
```

После выполнения этого сценария данные будут загружены в каталог с именем `tutorial/data`.

## <a name="create-the-control-script"></a>Создание сценария элемента управления

Разница в приведенном ниже сценарии элемента управления и тем, который использовался для отправки "Hello World", заключается в том, что вы добавляете несколько дополнительных строк для задания среды.

Создайте файл Python в каталоге `tutorial` с именем `04-run-pytorch.py`:

```python
# tutorial/04-run-pytorch.py
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import ScriptRunConfig

if __name__ == "__main__":
    ws = Workspace.from_config()
    experiment = Experiment(workspace=ws, name='day1-experiment-train')
    config = ScriptRunConfig(source_directory='src', script='train.py', compute_target='cpu-cluster')

    # set up pytorch environment
    env = Environment.from_conda_specification(name='pytorch-env', file_path='.azureml/pytorch-env.yml')
    config.run_config.environment = env

    run = experiment.submit(config)

    aml_url = run.get_portal_url()
    print(aml_url)
```

### <a name="understand-the-code-changes"></a>Изучение изменений кода

:::row:::
   :::column span="":::
      `env = Environment.from_conda_specification( ... )`
   :::column-end:::
   :::column span="2":::
      Машинное обучение Azure предоставляет концепцию [Среды](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true), чтобы представить воспроизводимую версионную среду Python для выполнения экспериментов. Вы можете легко создать среду из локальной среды Conda или PIP.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config.run_config.environment = env`
   :::column-end:::
   :::column span="2":::
      При этом среда добавляется в [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true).
   :::column-end:::
:::row-end:::

## <a name="submit-run-to-azure-machine-learning"></a>Отправка запуска в Машинное обучение Azure

Если вы переключались на локальные среды, обязательно переключитесь обратно в среду с установленным и выполняемым пакетом SDK для Python Машинного обучения Azure:

```bash
python 04-run-pytorch.py
```

>[!NOTE] 
> При первом запуске этого сценария Машинное обучение Azure создаст образ Docker из вашей среды PyTorch. Все выполнение может занять 5–10 минут. Журналы сборки Docker можно просмотреть в Студии машинного обучения Azure: Щелкните ссылку на Студию машинного обучения, выберите вкладку "Выходные данные и журналы", а затем — `20_image_build_log.txt`.
Этот образ будет повторно использован в будущих запусках для их ускорения.

После сборки образа выберите `70_driver_log.txt`, чтобы просмотреть выходные данные вашего сценария обучения.

```txt
Downloading https://www.cs.toronto.edu/~kriz/cifar-10-python.tar.gz to ./data/cifar-10-python.tar.gz
...
Files already downloaded and verified
epoch=1, batch= 2000: loss 2.19
epoch=1, batch= 4000: loss 1.82
epoch=1, batch= 6000: loss 1.66
epoch=1, batch= 8000: loss 1.58
epoch=1, batch=10000: loss 1.52
epoch=1, batch=12000: loss 1.47
epoch=2, batch= 2000: loss 1.39
epoch=2, batch= 4000: loss 1.38
epoch=2, batch= 6000: loss 1.37
epoch=2, batch= 8000: loss 1.33
epoch=2, batch=10000: loss 1.31
epoch=2, batch=12000: loss 1.27
Finished Training
```

> [!WARNING]
> Если отображается сообщение об ошибке `Your total snapshot size exceeds the limit`, это означает, что каталог `data` находится в `source_directory`, используемом в `ScriptRunConfig`.
> Не забудьте переместить `data` за пределы `src`.

Среды можно зарегистрировать в рабочей области с помощью `env.register(ws)`, что позволяет легко предоставлять к ним общий доступ, многократно использовать и управлять их версиями. Среды упрощают воспроизведение предыдущих результатов и совместную работу с командой.

Машинное обучение Azure также поддерживает коллекцию курируемых сред. Эти среды охватывают общие сценарии машинного обучения и поддерживаются кэшированными образами Docker. Кэшированные образы Docker ускоряют первое удаленное выполнение.

Если коротко, использование зарегистрированных сред позволяет экономить время. Дополнительные сведения можно найти в [документации по средам](./how-to-use-environments.md).

## <a name="log-training-metrics"></a>Ведение журнала метрик обучения

Теперь, настроив обучение модели в Машинном обучении Azure, начните отслеживать некоторые метрики производительности.
Текущий сценарий обучения выводит метрики в терминал. Машинное обучение Azure предоставляет механизм для ведения журнала метрик с дополнительными функциональными возможностями. Добавив несколько строк кода, вы получаете возможность визуализировать метрики в студии и сравнивать метрики из нескольких запусков.

### <a name="modify-trainpy-to-include-logging"></a>Изменение `train.py` для ведения журнала

Измените сценарий `train.py`, добавив в него две строки кода:

```python
# train.py
import torch
import torch.optim as optim
import torchvision
import torchvision.transforms as transforms

from model import Net
from azureml.core import Run


# ADDITIONAL CODE: get Azure Machine Learning run from the current context
run = Run.get_context()

# download CIFAR 10 data
trainset = torchvision.datasets.CIFAR10(root='./data', train=True, download=True, transform=torchvision.transforms.ToTensor())
trainloader = torch.utils.data.DataLoader(trainset, batch_size=4, shuffle=True, num_workers=2)

if __name__ == "__main__":

    # define convolutional network
    net = Net()

    # set up pytorch loss /  optimizer
    criterion = torch.nn.CrossEntropyLoss()
    optimizer = optim.SGD(net.parameters(), lr=0.001, momentum=0.9)

    # train the network
    for epoch in range(2):

        running_loss = 0.0
        for i, data in enumerate(trainloader, 0):
            # unpack the data
            inputs, labels = data

            # zero the parameter gradients
            optimizer.zero_grad()

            # forward + backward + optimize
            outputs = net(inputs)
            loss = criterion(outputs, labels)
            loss.backward()
            optimizer.step()

            # print statistics
            running_loss += loss.item()
            if i % 2000 == 1999:
                loss = running_loss / 2000
                run.log('loss', loss) # ADDITIONAL CODE: log loss metric to Azure Machine Learning
                print(f'epoch={epoch + 1}, batch={i + 1:5}: loss {loss:.2f}')
                running_loss = 0.0

    print('Finished Training')
```

#### <a name="understand-the-additional-two-lines-of-code"></a>Сведения о дополнительных двух строках кода

В `train.py` вы обращаетесь к объекту run из _самого_ сценария обучения с помощью метода `Run.get_context()` и используете его для ведения журнала метрик:

```python
# in train.py
run = Run.get_context()

...

run.log('loss', loss)
```

Метрики в Машинном обучении Azure:

- Организованы по экспериментам и выполняются так, чтобы можно было легко отследить и сравнить метрики.
- Оснащены пользовательским интерфейсом, что позволяет визуализировать эффективность обучения в студии.
- Предназначены для масштабирования, поэтому вы можете использовать эти преимущества даже при выполнении сотен экспериментов.

### <a name="update-the-conda-environment-file"></a>Обновление файла среды Conda

Сценарий `train.py` только что принял новую зависимость от `azureml.core`. Обновите `pytorch-env.yml`, чтобы отразить это изменение:

```yaml
# tutorial/.azureml/pytorch-env.yml
name: pytorch-env
channels:
    - defaults
    - pytorch
dependencies:
    - python=3.6.2
    - pytorch
    - torchvision
    - pip
    - pip:
        - azureml-sdk
```

### <a name="submit-run-to-azure-machine-learning"></a>Отправка запуска в Машинное обучение Azure
Отправьте этот сценарий еще раз:

```bash
python 04-run-pytorch.py
```

На этот раз при посещении студии перейдите на вкладку "Метрики", на которой теперь можно увидеть обновления в реальном времени для потерь обучения модели.

:::image type="content" source="media/tutorial-1st-experiment-sdk-train/logging-metrics.png" alt-text="Диаграмма потерь обучения на вкладке метрик":::

## <a name="next-steps"></a>Следующие шаги

В этом сеансе вы обновили базовый сценарий Hello World! на более реалистичный сценарий обучения, для выполнения которого требуется определенная среда Python. Вы узнали, как использовать локальную среду Conda в облаке со средами Машинного обучение Azure. И наконец, вы узнали, как с помощью нескольких строк кода можно вести журнал метрик в Машинном обучении Azure.

Существуют и другие способы создания сред Машинного обучения Azure, в том числе [из файла requirements.txt для PIP](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true#from-pip-requirements-name--file-path-) или даже [из имеющейся локальной среды Conda](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true#from-existing-conda-environment-name--conda-environment-name-).

В следующий раз вы узнаете, как работать с данными в Машинном обучении Azure путем передачи набора данных CIFAR10 в Azure.

> [!div class="nextstepaction"]
> [Руководство. Предоставление своих данных](tutorial-1st-experiment-bring-data.md)

>[!NOTE] 
> Если вы хотите на этом завершить серию учебников, не переходя к следующему шагу, [очистите ресурсы](tutorial-1st-experiment-bring-data.md#clean-up-resources).