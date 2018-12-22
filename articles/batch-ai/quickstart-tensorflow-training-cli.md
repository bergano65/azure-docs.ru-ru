---
title: Краткое руководство по Azure. Глубокое обучение с помощью Azure CLI | Документация Майкрософт
description: Краткое руководство. Процесс обучения нейронной сети глубокого обучения TensorFlow на одном GPU с использованием Batch AI и Azure CLI
services: batch-ai
documentationcenter: na
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: quickstart
ms.date: 09/03/2018
ms.author: danlep
ROBOTS: NOINDEX
ms.openlocfilehash: 979253021f5503295e0572759b510e074ceb1a6b
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408074"
---
# <a name="quickstart-train-a-deep-learning-model-with-batch-ai"></a>Краткое руководство. Обучение модели глубокого обучения с помощью Batch AI

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

В этом кратком руководстве показано, как обучить пример модели глубокого обучения на виртуальной машине с поддержкой GPU под управлением Batch AI. Batch AI — это управляемая служба, которая позволяет специалистам по обработке и анализу данных и исследователям искусственного интеллекта обучать модели искусственного интеллекта и машинного обучения в большом масштабе на кластерах виртуальных машин Azure. 

В этом примере вы с помощью Azure CLI настроите Batch AI для обучения примера нейронной сети [TensorFlow](https://www.tensorflow.org/) по [базе данных MNIST](http://yann.lecun.com/exdb/mnist/) с рукописными цифрами. По завершении работы с этим кратким руководством вы освоите ключевые понятия обучения модели машинного обучения или искусственного интеллекта с помощью Batch AI и будете готовы испытать разные модели обучения в большем масштабе.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать CLI на локальном компьютере, для выполнения инструкций из этого руководства вам потребуется Azure CLI версии 2.0.38 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli). 

В этом кратком руководстве предполагается, что вы выполняете команды в оболочке Bash через Cloud Shell или на локальном компьютере. Если вы уже завершили краткое руководство [по созданию кластера Batch AI с помощью Azure CLI](quickstart-create-cluster-cli.md), пропустите первые два шага, в которых создаются группа ресурсов и кластер Batch AI.

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью команды `az group create`. Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими. 

В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus2*. Не забудьте выбрать расположение "Восточная часть США 2" или другой регион, где уже доступна служба Batch AI. 

```azurecli-interactive 
az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-a-batch-ai-cluster"></a>Создание кластера искусственного интеллекта пакетной службы

Сначала создайте `az batchai workspace create`рабочую область*Batch AI с помощью команды*. Рабочая область нужна для того, чтобы упорядочить кластеры Batch AI и другие ресурсы.

```azurecli-interactive
az batchai workspace create \
    --workspace myworkspace \
    --resource-group myResourceGroup
```

Чтобы создать кластер Batch AI, используйте команду `az batchai cluster create`. Следующий пример создает кластер с одним узлом со следующими свойствами:

* Содержит виртуальную машину NC6 с одним GPU типа NVIDIA Tesla K80. В Azure доступно несколько размеров виртуальных машин с разными GPU типа NVIDIA.
* Работает под управлением стандартного образа Ubuntu Server, который предназначен для размещения приложений на базе контейнеров. В этом дистрибутиве можно выполнять почти любые рабочие нагрузки. 
* Добавлена учетная запись пользователя с именем *myusername* и созданы ключи SSH, если они не существовали ранее в расположении ключей по умолчанию (*~/.ssh*) в локальной среде.

```azurecli-interactive
az batchai cluster create \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --vm-size Standard_NC6 \
    --target 1 \
    --user-name myusername \
    --generate-ssh-keys
```

Выходные данные команды отображают свойства кластера. Создание и запуск узла занимают несколько минут. Чтобы просмотреть состояние кластера, выполните команду `az batchai cluster show`.

```azurecli-interactive
az batchai cluster show \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --output table
```

На раннем этапе создания кластера она возвращает примерно такой результат с состоянием кластера `resizing`:

```bash
Name       Resource Group    Workspace    VM Size       State      Idle    Running    Preparing    Leaving    Unusable
---------  ----------------  -----------  ------------  -------  ------  ---------  -----------  ---------  ----------
mycluster  myResourceGroup   myworkspace  STANDARD_NC6  resizing      0          0            0          0           0

```

Продолжайте выполнять описанные ниже шаги, чтобы отправить скрипт обучения и создать задание обучения, пока изменяется состояние кластера. Кластер будет готов выполнять задание обучения, когда его состояние получит значение `steady`, а состояние единственного узла — `Idle`.

## <a name="upload-training-script"></a>Отправка скрипта обучения

С помощью команды `az storage account create` создайте учетную запись хранения для выходных данных обучения и скрипта обучения.

```azurecli-interactive
az storage account create \
    --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus2 \
    --sku Standard_LRS
```

Создайте в учетной записи общую папку Azure с именем `myshare`, выполнив команду `az storage share create`:

```azurecli-interactive
az storage share create \
    --name myshare \
    --account-name mystorageaccount
```

Используйте команду `az storage directory create`, чтобы создать каталоги в общей папке Azure. Создайте каталог `scripts` для скрипта обучения и каталог `logs` для выходных данных обучения:

```azurecli-interactive
# Create /scripts directory in file share
az storage directory create \
    --name scripts \
    --share-name myshare \
    --account-name mystorageaccount

# Create /logs directory in file share 
az storage directory create \
    --name logs \
    --share-name myshare \
    --account-name mystorageaccount
```

В оболочке Bash создайте локальную рабочую папку и скачайте пример TensorFlow [convolutional.py](https://raw.githubusercontent.com/tensorflow/models/master/tutorials/image/mnist/convolutional.py). Этот скрипт Python обучает сверточную нейронную сеть, используя набор изображений MNIST, содержащий 60 000 написанных от руки цифр от 0 до 9. Затем он проверяет модель по набору тестовых примеров.

```bash
wget https://raw.githubusercontent.com/tensorflow/models/master/tutorials/image/mnist/convolutional.py
```

Отправьте скрипт в каталог `scripts` в общей папке, выполнив команду `az storage file upload`.

```azurecli-interactive
az storage file upload \
    --share-name myshare \
    --path scripts \
    --source convolutional.py \
    --account-name mystorageaccount
```

## <a name="submit-training-job"></a>Отправка задания обучения

Сначала создайте *эксперимент* Batch AI в рабочей области с помощью команды `az batchai experiment create`. Эксперимент — это логический контейнер для связанных заданий Batch AI.

```azurecli-interactive
az batchai experiment create \
    --name myexperiment \
    --workspace myworkspace \
    --resource-group myResourceGroup
```

В рабочей папке создайте файл конфигурации `job.json` для задания обучения с указанным ниже содержимым. Этот файл конфигурации передается при отправке задания обучения.

Файл `job.json` содержит параметры, позволяющие найти файл скрипта Python и выполнить его на узле GPU в контейнере TensorFlow. Он также указывает, куда нужно сохранять выходные файлы задания в службе хранилища Azure. `<AZURE_BATCHAI_STORAGE_ACCOUNT>` указывает, что имя учетной записи хранения будет указываться во время отправки задания.

```json
{
    "$schema": "https://raw.githubusercontent.com/Azure/BatchAI/master/schemas/2018-05-01/job.json",
    "properties": {
        "nodeCount": 1,
        "tensorFlowSettings": {
            "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/scripts/convolutional.py"
        },
        "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/logs",
        "mountVolumes": {
            "azureFileShares": [
                {
                    "azureFileUrl": "https://<AZURE_BATCHAI_STORAGE_ACCOUNT>.file.core.windows.net/myshare",
                    "relativeMountPath": "myshare"
                }
            ]
        },
        "containerSettings": {
            "imageSourceRegistry": {
                "image": "tensorflow/tensorflow:1.8.0-gpu"
            }
        }
    }
}
```

Используйте команду `az batchai job create`, чтобы отправить задание на узел, передав файл конфигурации `job.json` и имя учетной записи хранения:

```azurecli-interactive
az batchai job create \
    --name myjob \
    --cluster mycluster \
    --experiment myexperiment \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --config-file job.json \
    --storage-account-name mystorageaccount
```

Команда возвращает свойства задания, а затем выполняется в течение нескольких минут. Чтобы отслеживать ход выполнения задания, используйте команду `az batchai job file stream` для потоковой передачи файла `stdout-wk-0.txt` из стандартного каталога выходных данных на узле. Сценарий обучения создает этот файл после запуска задания.  

```azurecli-interactive
az batchai job file stream \
    --job myjob \
    --experiment myexperiment \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --file-name stdout-wk-0.txt
```

Выходные данные примера:

```
File found with URL "https://mystorageaccount.file.core.windows.net/logs/00000000-0000-0000-0000-000000000000/myResourceGroup/workspaces/myworkspace/experiments/myexperiment/jobs/myjob/<JOB_ID>/stdouterr/stdout-wk-0.txt?sv=2016-05-31&sr=f&sig=Kih9baozMao8Ugos%2FVG%2BcsVsSeY1O%2FTocCNvLQhwtx4%3D&se=2018-06-20T22%3A07%3A30Z&sp=rl". Start streaming
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting data/train-images-idx3-ubyte.gz
Extracting data/train-labels-idx1-ubyte.gz
Extracting data/t10k-images-idx3-ubyte.gz
Extracting data/t10k-labels-idx1-ubyte.gz
Initialized!
Step 0 (epoch 0.00), 14.9 ms
Minibatch loss: 8.334, learning rate: 0.010000
Minibatch error: 85.9%
Validation error: 84.6%
Step 100 (epoch 0.12), 9.7 ms
Minibatch loss: 3.240, learning rate: 0.010000
Minibatch error: 6.2%
Validation error: 7.7%
Step 200 (epoch 0.23), 8.3 ms
Minibatch loss: 3.335, learning rate: 0.010000
Minibatch error: 7.8%
Validation error: 4.5%
Step 300 (epoch 0.35), 8.3 ms
Minibatch loss: 3.157, learning rate: 0.010000
Minibatch error: 3.1%
...
Step 8500 (epoch 9.89), 8.3 ms
Minibatch loss: 1.605, learning rate: 0.006302
Minibatch error: 0.0%
Validation error: 0.9%
Test error: 0.8%
```

Потоковая передача прекращается после завершения задания. Этот пример скрипта выполняет 10 *эпох* (проходов) обучения по набору данных для обучения. В нашем случае после 10 эпох обученная модель работает с уровнем ошибки теста всего 0,8%.

## <a name="get-job-output"></a>Получение выходных данных задания

Batch AI создает для выходных данных каждого задания уникальную структуру папок в учетной записи хранения. Сохраните этот путь в переменную среды JOB_OUTPUT_PATH. После этого получите список файлов выходных данных, сохраненных в хранилище, с помощью команды `az storage file list`:

```azurecli-interactive
export JOB_OUTPUT_PATH=$(az batchai job show --name myjob --experiment myexperiment --workspace myworkspace --resource-group myResourceGroup --query jobOutputDirectoryPathSegment --output tsv)

az storage file list \
    --share-name myshare/logs \
    --account-name mystorageaccount \
    --path $JOB_OUTPUT_PATH/stdouterr \
    --output table
```

Она выводит выходные данные следующего вида:

```
Name               Content Length  Type    Last Modified
---------------  ----------------  ------  ---------------
execution.log               14866  file
stderr-wk-0.txt              1527  file
stdout-wk-0.txt             11027  file
```

Используйте команду `az storage file download`, чтобы скачать один или несколько файлов в локальную рабочую папку. Например: 

```azurecli-interactive
az storage file download \
    --share-name myshare/logs \
    --account-name mystorageaccount \
    --path $JOB_OUTPUT_PATH/stdouterr/stdout-wk-0.txt
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите продолжить изучение примеров и руководств по Batch AI, сохраните созданные с помощью этого руководства рабочую область Batch AI, кластер и учетную запись хранения. 

Плата за кластер Batch AI насчитывается только во время работы узлов. Если вы хотите сохранить конфигурацию кластера, пока нет новых заданий для выполнения в нем, уменьшите размер кластера до 0 узлов. 

```azurecli-interactive
az batchai cluster resize \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --target 0
```

Позже вы сможете увеличить масштаб до одного или нескольких узлов, чтобы выполнить следующее задание. Когда кластер станет не нужен, удалите его с помощью команды `az batchai cluster delete`:

```azurecli-interactive
az batchai cluster delete \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup
```

Если группа ресурсов Batch AI и ресурсы хранения для нее больше не нужны, их можно удалить с помощью команды `az group delete`. 

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Дополнительная информация
Из этого краткого руководства вы узнали, как с помощью Batch AI обучить пример модели глубокого обучения TensorFlow на одной виртуальной машине с GPU, используя интерфейс командной строки Azure. Дополнительные сведения об обучении распределенной модели в крупном кластере с GPU см. в следующем руководстве по Batch AI.

> [!div class="nextstepaction"]
> [Руководство по обучению распределенной модели](./tutorial-horovod-tensorflow.md)