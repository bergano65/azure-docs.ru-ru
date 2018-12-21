---
title: Руководство. Обучение распределенной модели с использованием Horovod и Azure Batch AI | Документация Майкрософт
description: Руководство по обучению распределенной модели в Horovod с помощью службы Azure Batch AI и Azure CLI.
services: batch-ai
author: johnwu10
manager: jeconnoc
ms.service: batch-ai
ms.topic: tutorial
ms.date: 09/03/2018
ms.author: danlep
ms.custom: mvc
ROBOTS: NOINDEX
ms.openlocfilehash: 45255845d8645391ee33471830ac2ef27870a40d
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408635"
---
# <a name="tutorial-train-a-distributed-model-with-horovod"></a>Руководство. Обучение распределенной модели в Horovod

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

С помощью этого руководства вы обучите распределенную модель глубокого обучения, запуская ее параллельно на нескольких узлах в кластере Batch AI. Batch AI — это управляемая служба для обучения моделей машинного обучения и искусственного интеллекта с возможностью масштабирования в кластерах графических процессоров Azure. 

В этом руководстве описывается типичный рабочий процесс Batch AI и способы взаимодействия с ресурсами Batch AI через Azure CLI. Здесь рассматриваются такие темы:

> [!div class="checklist"]
> * настройка рабочей области, эксперимента и кластера Batch AI;
> * настройка общей папки Azure для входных и выходных данных;
> * параллелизация модели глубокого обучения с помощью Horovod;
> * отправка задания обучения;
> * Отслеживание задания
> * получение результатов обучения.

В этом руководстве модель обнаружения объектов изменена так, чтобы выполняться параллельно с [Horovod](https://github.com/uber/horovod). Для обучения модели используется [набор изображений CIFAR-10](https://www.cs.toronto.edu/~kriz/cifar.html). Задание обучения выполняется в кластере из 24 виртуальных ЦП и 4 GPU и занимает около 60 минут.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, то для работы с этим руководством вам понадобится Azure CLI 2.0.38 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="why-use-horovod"></a>Зачем использовать Horovod?

Horovod — это платформа распределенного обучения для TensorFlow, Keras и PyTorch, которая используется в этом руководстве. С помощью Horovod вы можете преобразовать скрипт обучения, предназначенный для работы на одном GPU, в новую версию, которая будет эффективно выполняться в распределенной системе, изменив всего несколько строк кода.

Помимо Horovod служба Batch AI поддерживает распределенное обучение еще на нескольких популярных платформах с открытым кодом. Обязательно ознакомьтесь с условиями лицензии для любой платформы, которую вы планируете применять для обучения моделей в рабочей среде.

## <a name="prepare-the-batch-ai-environment"></a>Подготовка среды Batch AI

### <a name="create-a-resource-group"></a>Создание группы ресурсов

С помощью команды `az group create` создайте группу ресурсов с именем `batchai.horovod` в регионе `eastus`. Эта группа ресурсов потребуется вам для развертывания ресурсов Batch AI.

```azurecli-interactive
az group create --name batchai.horovod --location eastus
```

### <a name="create-a-workspace"></a>Создание рабочей области

Создайте рабочую область Batch AI с помощью команды `az batchai workspace create`. Рабочая область — это высокоуровневая коллекция любых ресурсов Batch AI. Следующая команда создает рабочую область с именем `batchaidev` в указанной группе ресурсов.

```azurecli-interactive
az batchai workspace create --resource-group batchai.horovod --workspace batchaidev 
```

### <a name="create-an-experiment"></a>Создание эксперимента

Эксперимент Batch AI объединяет одно или несколько заданий, которые запрашиваются и управляются совместно. Следующая команда `az batchai experiment create` создает эксперимент с именем `cifar` в рабочей области и группе ресурсов.

```azurecli-interactive
az batchai experiment create --resource-group batchai.horovod --workspace batchaidev --name cifar 
```

## <a name="set-up-a-gpu-cluster"></a>Настройка кластера GPU

Теперь настройте кластер GPU для выполнения эксперимента. Служба Batch AI предоставляет гибкий набор возможностей для настройки кластеров под конкретные нужды.

Следующая команда `az batchai cluster create` создает кластер с четырьмя узлами и именем `nc6cluster` в рабочей области и группе ресурсов. По умолчанию виртуальные машины в кластере работают под управлением образа Ubuntu Server, предназначенного для размещения приложений на базе контейнеров. Узлы кластера в этом примере имеют размер `Standard_NC6`, то есть содержат по одному GPU типа NVIDIA Tesla K80.

```azurecli-interactive
az batchai cluster create --resource-group batchai.horovod --workspace batchaidev --name nc6cluster --vm-priority dedicated  --vm-size Standard_NC6 --target 4 --generate-ssh-keys
```

Выполните команду `az batchai cluster show`, чтобы просмотреть состояние кластера. Обычно полная подготовка кластера занимает несколько минут.

```azurecli-interactive
az batchai cluster show --name nc6cluster --workspace batchaidev --resource-group batchai.horovod --output table
```

На раннем этапе создания кластер находится в состоянии `resizing`. Пока изменяется состояние кластера, переходите к следующим действиям. Кластер будет готов выполнять задание обучения, когда его состояние получит значение `steady`, а состояние узлов — `idle`. Например: 

```
Name        Resource Group    Workspace    VM Size       State      Idle    Running    Preparing    Leaving    Unusable
----------  ----------------  -----------  ------------  -------  ------  ---------  -----------  ---------  ----------
nc6cluster  batchai.horovod  batchaidev   STANDARD_NC6  steady        4          0            0          0           0
```

## <a name="set-up-storage"></a>Настройка хранилища

Используйте команду `az storage account create`, чтобы создать учетную запись хранения для выходных данных обучения и скрипта обучения.

```azurecli-interactive
az storage account create --resource-group batchai.horovod --name mystorageaccount --location eastus --sku Standard_LRS
```

Создайте в учетной записи общую папку Azure с именем `myshare`, выполнив команду `az storage share create`:

```azurecli-interactive
az storage share create --name myshare --account-name mystorageaccount
```

Обычно одно и то же хранилище можно использовать в нескольких заданиях и экспериментах. Чтобы поддерживать порядок в ресурсах, создайте в общей папке отдельный каталог для хранения файлов, связанных с конкретным экспериментом. Следующая команда `az storage directory create` создает каталог с именем `cifar`.

```azurecli-interactive
az storage directory create --name cifar --share-name myshare --account-name mystorageaccount
```

Следующим шагом является подготовка собственно скрипта обучения, который вы передадите в только что созданный каталог.

## <a name="create-the-training-script"></a>Создание скрипта обучения

Для этого эксперимента вы выполните скрипт Python, дополненный несколькими изменениями для запуска модели обнаружения объектов в параллельном режиме через Horovod. В [исходной модели](https://raw.githubusercontent.com/keras-team/keras/master/examples/cifar10_cnn.py) используется Keras с серверной частью TensorFlow. 

В используемой оболочке перейдите в рабочий каталог и с помощью любого текстового редактора создайте файл с именем `cifar_cnn_distributed.py` и приведенным ниже содержимым. Изменения в исходном коде снабжены комментариями с префиксом `HOROVOD`.

```python
from __future__ import print_function
import keras
from keras.datasets import cifar10
from keras.preprocessing.image import ImageDataGenerator
from keras.models import Sequential
from keras.layers import Dense, Dropout, Activation, Flatten
from keras.layers import Conv2D, MaxPooling2D
import tensorflow as tf
import horovod.keras as hvd
import os
from keras import backend as K
import math
import argparse 

# HOROVOD: initialize Horovod.
hvd.init()

# HOROVOD: pin GPU to be used to process local rank (one GPU per process)
config = tf.ConfigProto()
config.gpu_options.allow_growth = True
config.gpu_options.visible_device_list = str(hvd.local_rank())
K.set_session(tf.Session(config=config))

batch_size = 32
num_classes = 10
# HOROVOD: adjust number of epochs based on number of GPUs.
epochs = int(math.ceil(100.0 / hvd.size()))

data_augmentation = True
num_predictions = 20
# BATCH AI: change save directory to mounted storage path
parser = argparse.ArgumentParser()
parser.add_argument("-d", "--dir", help="directory to save model to")
args = parser.parse_args()
save_dir = os.path.join(args.dir, 'saved_models')
model_name = 'keras_cifar10_trained_model.h5'

# The data, split between train and test sets:
(x_train, y_train), (x_test, y_test) = cifar10.load_data()
print('x_train shape:', x_train.shape)
print(x_train.shape[0], 'train samples')
print(x_test.shape[0], 'test samples')

# Convert class vectors to binary class matrices.
y_train = keras.utils.to_categorical(y_train, num_classes)
y_test = keras.utils.to_categorical(y_test, num_classes)

model = Sequential()
model.add(Conv2D(32, (3, 3), padding='same',
                 input_shape=x_train.shape[1:]))
model.add(Activation('relu'))
model.add(Conv2D(32, (3, 3)))
model.add(Activation('relu'))
model.add(MaxPooling2D(pool_size=(2, 2)))
model.add(Dropout(0.25))

model.add(Conv2D(64, (3, 3), padding='same'))
model.add(Activation('relu'))
model.add(Conv2D(64, (3, 3)))
model.add(Activation('relu'))
model.add(MaxPooling2D(pool_size=(2, 2)))
model.add(Dropout(0.25))

model.add(Flatten())
model.add(Dense(512))
model.add(Activation('relu'))
model.add(Dropout(0.5))
model.add(Dense(num_classes))
model.add(Activation('softmax'))

# HOROVOD: adjust learning rate based on number of GPUs.
opt = keras.optimizers.rmsprop(lr=0.0001 * hvd.size(), decay=1e-6)

# HOROVOD: add Horovod Distributed Optimizer.
opt = hvd.DistributedOptimizer(opt)

# Let's train the model using RMSprop
model.compile(loss='categorical_crossentropy',
              optimizer=opt,
              metrics=['accuracy'])

callbacks = [
    # HOROVOD: broadcast initial variable states from rank 0 to all other processes.
    # This is necessary to ensure consistent initialization of all workers when
    # training is started with random weights or restored from a checkpoint.
    hvd.callbacks.BroadcastGlobalVariablesCallback(0),
]

# HOROVOD: save checkpoints only on worker 0 to prevent other workers from corrupting them.
if hvd.rank() == 0:
    callbacks.append(keras.callbacks.ModelCheckpoint('./checkpoint-{epoch}.h5'))

x_train = x_train.astype('float32')
x_test = x_test.astype('float32')
x_train /= 255
x_test /= 255

if not data_augmentation:
    print('Not using data augmentation.')
    model.fit(x_train, y_train,
              batch_size=batch_size,
              epochs=epochs,
              validation_data=(x_test, y_test),
              shuffle=True)
else:
    print('Using real-time data augmentation.')
    # This will do preprocessing and realtime data augmentation:
    datagen = ImageDataGenerator(
        featurewise_center=False,  # set input mean to 0 over the dataset
        samplewise_center=False,  # set each sample mean to 0
        featurewise_std_normalization=False,  # divide inputs by std of the dataset
        samplewise_std_normalization=False,  # divide each input by its std
        zca_whitening=False,  # apply ZCA whitening
        zca_epsilon=1e-06,  # epsilon for ZCA whitening
        rotation_range=0,  # randomly rotate images in the range (degrees, 0 to 180)
        width_shift_range=0.1,  # randomly shift images horizontally (fraction of total width)
        height_shift_range=0.1,  # randomly shift images vertically (fraction of total height)
        shear_range=0.,  # set range for random shear
        zoom_range=0.,  # set range for random zoom
        channel_shift_range=0.,  # set range for random channel shifts
        fill_mode='nearest',  # set mode for filling points outside the input boundaries
        cval=0.,  # value used for fill_mode = "constant"
        horizontal_flip=True,  # randomly flip images
        vertical_flip=False,  # randomly flip images
        rescale=None,  # set rescaling factor (applied before any other transformation)
        preprocessing_function=None,  # set function that will be applied on each input
        data_format=None,  # image data format, either "channels_first" or "channels_last"
        validation_split=0.0)  # fraction of images reserved for validation (strictly between 0 and 1)

    # Compute quantities required for feature-wise normalization
    # (std, mean, and principal components if ZCA whitening is applied).
    datagen.fit(x_train)

    # Fit the model on the batches generated by datagen.flow().
    model.fit_generator(datagen.flow(x_train, y_train,
                                     batch_size=batch_size),
                        epochs=epochs,
                        validation_data=(x_test, y_test),
                        workers=4)

# Save model and weights
if not os.path.isdir(save_dir):
    os.makedirs(save_dir)
model_path = os.path.join(save_dir, model_name)
model.save(model_path)
print('Saved trained model at %s ' % model_path)

# Score trained model.
scores = model.evaluate(x_test, y_test, verbose=1)
print('Test loss:', scores[0])
print('Test accuracy:', scores[1])
```

Как показано в этом примере, небольшие изменения модели позволяют создать распределенное обучение на платформе Horovod. 

Имейте в виду, что этот скрипт использует относительно небольшие модель и набор данных для демонстрационных целей, поэтому распределенная модель не всегда позволяет существенно повысить производительность. Чтобы увидеть реальные преимущества от распределенного обучения, нужны намного более крупные модель и набор данных.

## <a name="upload-the-training-script"></a>Отправка скрипта обучения

Когда скрипт будет готов, отправьте его в общий каталог созданной ранее общей папки. Следующая команда `az storage file upload` передает файл из локального рабочего каталога в нужное расположение.

```azurecli-interactive
az storage file upload --path cifar --share-name myshare --source cifar_cnn_distributed.py --account-name mystorageaccount
```

## <a name="submit-the-training-job"></a>Отправка задания обучения

Завершив предыдущие шаги, создайте задание обучения. В Batch AI параметры выполнения задания определяются в файле `job.json`. В любом удобном текстовом редакторе создайте для задания файл конфигурации с именем `job.json` и следующим содержимым.

```json
{
    "$schema": "https://raw.githubusercontent.com/Azure/BatchAI/master/schemas/2018-05-01/job.json",
    "properties": {
        "nodeCount": 4,
        "horovodSettings": {
            "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/cifar/cifar_cnn_distributed.py",
            "commandLineArgs": "--dir=$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/cifar"
        },
        "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/cifar",
        "mountVolumes": {
            "azureFileShares": [
                {
                    "azureFileUrl": "https://<AZURE_BATCHAI_STORAGE_ACCOUNT>.file.core.windows.net/myshare",
                    "relativeMountPath": "myshare"
                }
            ]
        },
        "jobPreparation": {
            "commandLine": "apt update; apt install mpi-default-dev mpi-default-bin -y; pip install keras horovod"
        },
        "containerSettings": {
            "imageSourceRegistry": {
                "image": "tensorflow/tensorflow:1.8.0-gpu"
            }
        }
    }
}
```

Описание свойств:

| Свойство | ОПИСАНИЕ |
| --------- | --------- |
| `nodeCount` | Число узлов, выделяемых для задания. В нашем примере задания будут выполняться параллельно на `4` узлах. |
| `horovodSettings` | Поле `pythonScriptFilePath` определяет путь к скрипту Horovod, который мы разместили в ранее созданном каталоге `cifar`. Поле `commandLineArgs` содержит аргументы командной строки для выполнения скрипта. Для нашего эксперимента нужен только один аргумент — каталог для сохранения модели. Путь `$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare` использовался для подключения общей папки. | 
| `stdOutErrPathPrefix` | По этому пути сохраняются выходные данные и журналы задания. В нашем примере он совпадает с каталогом `cifar`. |
| `jobPreparation` | Особые указания по подготовке среды для выполнения задания. Для работы этого скрипта должны быть установлены указанные пакеты MPI и Horovod. |
| `containerSettings` | Параметры для контейнера, в котором выполняется задание. В этом задании используется контейнер Docker, созданный с помощью `tensorflow`.

Создайте задание на основе этой конфигурации с помощью команды `az batchai job create`. Следующая команда помещает в очередь новое задание с именем `cifar_distributed`, используя все настроенные ранее ресурсы. 

```azurecli-interactive
az batchai job create --cluster nc6cluster --name cifar_distributed --resource-group batchai.horovod --workspace batchaidev --experiment cifar --config-file job.json --storage-account-name mystorageaccount
```

Если узлы кластера сейчас заняты, на запуск задания потребуется некоторое время. Используйте команду `az batchai job show`, чтобы просмотреть состояние выполнения задания.

```azurecli-interactive
az batchai job show --experiment cifar --name cifar_distributed --resource-group batchai.horovod --workspace batchaidev --query "executionState"
```

### <a name="visualize-the-distributed-training"></a>Визуализация распределенного обучения

Когда начнется выполнение задания, еще раз проверьте текущее состояние узлов кластера с помощью команды `az batchai cluster show`. 

```azurecli-interactive
az batchai cluster show --name nc6cluster --workspace batchaidev --resource-group batchai.horovod --query "nodeStateCounts"
```

Ниже представлен пример выходных данных этой команды, которые демонстрируют состояние выполнения для всех четырех узлов. Такой результат показывает, что все четыре узла сейчас используются для распределенного обучения.

```
{
  "idleNodeCount": 0,
  "leavingNodeCount": 0,
  "preparingNodeCount": 0,
  "runningNodeCount": 4,
  "unusableNodeCount": 0
}
```

## <a name="monitor-the-job"></a>Отслеживание задания

### <a name="list-output-files"></a>Получение списка выходных файлов

Пока выполняется задание, используйте команду `az batchai job file list` для получения списка выходных файлов, созданных этим заданием.

```azurecli-interactive
az batchai job file list --experiment cifar --job cifar_distributed --resource-group batchai.horovod --workspace batchaidev --output table
```

Для нашего эксперимента результат будет аналогичен приведенному ниже. Общие выходные данные задания передаются в `stdout.txt`, а в `stderr.txt` сохраняются все ошибки при выполнении основного задания. Также здесь есть файлы выходных данных, ошибок и журналов подготовки заданий для каждого отдельного узла.

```
Name                                                    Type       Size  Modified
------------------------------------------------------  ------  -------  -------------------------
execution-tvm-676767296_1-20180718t174802z-p.log        file       8801  2018-07-18T22:41:28+00:00
execution-tvm-676767296_2-20180718t174802z-p.log        file      15094  2018-07-18T22:41:55+00:00
execution-tvm-676767296_3-20180718t174802z-p.log        file       8801  2018-07-18T22:41:28+00:00
execution-tvm-676767296_4-20180718t174802z-p.log        file       8801  2018-07-18T22:41:28+00:00
stderr-job_prep-tvm-676767296_1-20180718t174802z-p.txt  file        238  2018-07-18T22:41:50+00:00
stderr-job_prep-tvm-676767296_2-20180718t174802z-p.txt  file        238  2018-07-18T22:41:50+00:00
stderr-job_prep-tvm-676767296_3-20180718t174802z-p.txt  file        238  2018-07-18T22:41:50+00:00
stderr-job_prep-tvm-676767296_4-20180718t174802z-p.txt  file        238  2018-07-18T22:41:50+00:00
stderr.txt                                              file       7653  2018-07-18T22:46:32+00:00
stdout-job_prep-tvm-676767296_1-20180718t174802z-p.txt  file      13651  2018-07-18T22:41:55+00:00
stdout-job_prep-tvm-676767296_2-20180718t174802z-p.txt  file      13651  2018-07-18T22:41:54+00:00
stdout-job_prep-tvm-676767296_3-20180718t174802z-p.txt  file      13651  2018-07-18T22:41:54+00:00
stdout-job_prep-tvm-676767296_4-20180718t174802z-p.txt  file      13651  2018-07-18T22:41:55+00:00
stdout.txt                                              file    2316480  2018-07-18T22:46:32+00:00
```

### <a name="stream-an-output-file"></a>Потоковая передача выходного файла

Используйте команду `az batchai job file stream` для потоковой передачи содержимого файла. Следующий пример команды выполняет потоковую передачу журнала выходных данных основного задания.

```azurecli-interactive
az batchai job file stream --experiment cifar --file-name stdout.txt --job cifar_distributed --resource-group batchai.horovod --workspace batchaidev
```

Во время выполнения задания эта команда выполняет потоковую передачу стандартных выходных данных задания обучения. Результат ее выполнения будет иметь следующий вид.

```
...
50000 train samples
10000 test samples
Using real-time data augmentation.
Epoch 1/25


   1/1563 [..............................] - ETA: 2:42:25 - loss: 2.3334 - acc: 0.0312   1/1563 [..............................] - ETA: 2:30:42 - loss: 2.2973 - acc: 0.0938
   1/1563 [..............................] - ETA: 30:36 - loss: 2.3175 - acc: 0.1250
   1/1563 [..............................] - ETA: 2:32:58 - loss: 2.3489 - acc: 0.0625
   2/1563 [..............................] - ETA: 1:21:59 - loss: 2.3230 - acc: 0.0625

   2/1563 [..............................]   2/1563 [..............................] - ETA: 1:16:09 - loss: 2.2913 - acc: 0.0938 - ETA: 1:17:15 - loss: 2.3147 - acc: 0.0781
   2/1563 [..............................] - ETA: 16:07 - loss: 2.3678 - acc: 0.0938
   3/1563 [..............................] - ETA: 55:05 - loss: 2.3232 - acc: 0.0938  
   3/1563 [..............................] - ETA: 51:57 - loss: 2.3185 - acc: 0.1146  
   3/1563 [..............................] - ETA: 51:12 - loss: 2.3179 - acc: 0.1042  
   3/1563 [..............................] - ETA: 11:13 - loss: 2.3504 - acc: 0.0833
   4/1563 [..............................] - ETA: 39:43 - loss: 2.3224 - acc: 0.1094
   4/1563 [..............................] - ETA: 42:09 - loss: 2.3049 - acc: 0.1250
   4/1563 [..............................] - ETA: 39:15 - loss: 2.3089 - acc: 0.1094
   4/1563 [..............................] - ETA: 9:16 - loss: 2.3316 - acc: 0.1016 
   5/1563 [..............................] - ETA: 39:51 - loss: 2.3153 - acc: 0.1125
   5/1563 [..............................] - ETA: 37:58 - loss: 2.3197 - acc: 0.1125
   5/1563 [..............................] - ETA: 37:35 - loss: 2.3148 - acc: 0.1062
   5/1563 [..............................] - ETA: 13:38 - loss: 2.3263 - acc: 0.1062
   6/1563 [..............................] - ETA: 35:48 - loss: 2.3168 - acc: 0.1198

   6/1563 [..............................]   6/1563 [..............................] - ETA: 34:13 - loss: 2.3142 - acc: 0.1198 - ETA: 33:51 - loss: 2.3162 - acc: 0.1042
   6/1563 [..............................] - ETA: 13:54 - loss: 2.3225 - acc: 0.1094
   7/1563 [..............................] - ETA: 30:53 - loss: 2.3181 - acc: 0.1071

   7/1563 [..............................]   7/1563 [..............................] - ETA: 29:32 - loss: 2.3149 - acc: 0.1161 - ETA: 29:13 - loss: 2.3140 - acc: 0.0938
   7/1563 [..............................] - ETA: 12:09 - loss: 2.3174 - acc: 0.1205
   8/1563 [..............................] - ETA: 26:04 - loss: 2.3113 - acc: 0.1133
   8/1563 [..............................] - ETA: 27:15 - loss: 2.3169 - acc: 0.1133
   8/1563 [..............................] - ETA: 10:51 - loss: 2.3152 - acc: 0.1172
...
```

Скрипт выполняет 25 эпох (проходов) обучения по учебному набору данных. Этот процесс занимает примерно 60 минут. 

## <a name="retrieve-the-results"></a>Получение результатов

Если скрипт выполняется успешно, точность проверки составляет около 70–75 % и обученная модель сохраняется в общую папку по адресу `cifar/saved_models/keras_cifar10_trained_model.h5`. 

Обучение модели обычно является частью более крупного рабочего процесса. Например, готовую модель можно предоставить в другом приложении. Чтобы скачать обученную модель в локальную среду, используйте команду `az storage file download`. 

```azurecli-interactive
az storage file download --path cifar/saved_models/keras_cifar10_trained_model.h5 --share-name myshare --account-name mystorageaccount
```
## <a name="clean-up-resources"></a>Очистка ресурсов

Когда все задания завершат работу, для экономии затрат на вычислительные ресурсы уменьшите масштаб всех кластеров до `0 nodes`. Тогда плата за время простоя взиматься не будет. Для этого используйте следующую команду `az batchai cluster resize`. 

```azurecli-interactive
az batchai cluster resize --name nc6cluster --resource-group batchai.horovod --target 0 --workspace batchaidev
```

Позже вы сможете увеличить масштаб до одного или нескольких узлов, чтобы выполнить следующее задание. 

Если вы совсем не планируете использовать эту рабочую область и учетную запись хранения, удалите группу ресурсов с помощью команды `az group delete`. При удалении группы ресурсов будут удалены и все размещенные в ней ресурсы.

```azurecli-interactive
az group delete --name batchai.horovod
```

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнять такие задачи:

> [!div class="checklist"]
> * настройка рабочей области, эксперимента и кластера Batch AI;
> * настройка общей папки Azure для входных и выходных данных;
> * параллелизация модели с помощью Horovod;
> * отправка задания обучения;
> * Отслеживание задания
> * получение результатов обучения.

Примеры использования Azure Batch AI с разными платформами см. в разделе инструкций на сайте GitHub.

> [!div class="nextstepaction"]
> [Инструкции для Batch AI](https://github.com/Azure/BatchAI/tree/master/recipes)
