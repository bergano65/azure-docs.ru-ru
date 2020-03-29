---
title: Создание и изучение наборов данных с помощью меток
titleSuffix: Azure Machine Learning
description: Узнайте, как экспортировать метки данных из проектов маркировки Машинного обучения Azure machine Learning и использовать их для задач машинного обучения.
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.topic: how-to
ms.date: 01/21/2020
ms.openlocfilehash: 5138109de3f80d405ce95b605714b511480563f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76549493"
---
# <a name="create-and-explore-azure-machine-learning-dataset-with-labels"></a>Создание и изучение набора данных машинного обучения Azure с помощью меток

В этой статье вы узнаете, как экспортировать метки данных из проекта маркировки данных Azure Machine Learning и загрузить их в популярные форматы, такие как, панды dataframe для изучения данных или набор данных Torchvision для преобразования изображения. 

## <a name="what-are-datasets-with-labels"></a>Что такое наборы данных с метками 

Наборы данных машинного обучения Azure с метками являются [TabularDatasets](how-to-create-register-datasets.md#dataset-types) с свойством меток, мы будем называть их помеченными наборами данных. Эти конкретные типы TabularDatasets создаются только в виде результатов проектов маркировки данных Azure Machine Learning. Создайте проект маркировки данных с [помощью этих шагов.](how-to-create-labeling-projects.md) Machine Learning поддерживает проекты по маркировке данных для классификации изображений, как многофункциональных, так и многоклассных, а также идентификацию объектов вместе с ограниченной коробкой.

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure. Если у вас нет подписки Azure, создайте [бесплатную учетную запись](https://aka.ms/AMLFree) перед началом.
* [SDK Для Python,](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)или доступ к [студии машинного обучения Azure.](https://ml.azure.com/)
    * Установка пакета [лазурных данных](https://docs.microsoft.com/python/api/azureml-contrib-dataset/?view=azure-ml-py)
* Рабочая область машинного обучения. См. сведения о [создании рабочей области машинного обучения Azure](how-to-manage-workspace.md).
* Доступ к проекту маркировки данных Azure Machine Learning. Если у вас нет проекта маркировки, создайте проект с [этими шагами.](how-to-create-labeling-projects.md)

## <a name="export-data-labels"></a>Метки данных об экспорте 

При завершении проекта маркировки данных можно экспортировать данные метки из проекта маркировки. Это позволяет фиксировать как ссылку на данные и их метки, так и экспортировать их в [формате COCO](http://cocodataset.org/#format-data) или в виде набора данных Azure Machine Learning. Используйте для этого кнопку **Экспорт** на странице **Сведения о проекте** для проекта добавления меток.

### <a name="coco"></a>Коко 

 Файл COCO создается в хранилище больших двоичных объектов по умолчанию для рабочей области Машинного обучения Azure в папке *export/coco*. 

### <a name="azure-machine-learning-dataset"></a>Набор данных службы "Машинное обучение Azure"

Вы можете получить доступ к экспортируемому набору данных по машинному обучению Azure в разделе **наборов данных** студии машинного обучения Azure. Страница **«Подробности** набора данных» также предоставляет пример кода для доступа к метки от Python.

![Экспортированный набор данных](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="explore-labeled-datasets"></a>Изучение наборов помеченных данных

Загрузите наборы помеченных данных в набор данных панд или набор данных Torchvision, чтобы использовать популярные библиотеки с открытым исходным кодом для исследования данных, а также PyTorch предоставил библиотеки для преобразования изображений и обучения.

### <a name="pandas-dataframe"></a>Кадр данных Pandas

Можно загрузить помеченные наборы данных в [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) рамку `azureml-contrib-dataset` данных панд с помощью метода из класса. Установите класс со следующей командой оболочки: 

```shell
pip install azureml-contrib-dataset
```

>[!NOTE]
>Пространство имен azureml.contrib часто меняется по мере улучшения сервиса. Поэтому все, что доступно в этом пространстве имен, считается предварительными версиями компонентов, поддержка которых корпорацией Майкрософт ограничена.

Мы предлагаем следующие варианты обработки файлов для потоков файлов при преобразовании в рамку данных панд.
* Скачать: Загрузите файлы данных на локальный путь.
* Гора: Смонтируйте файлы данных к точке крепления. Mount работает только для linux-вычислений, включая ноутбук Azure Machine Learning VM и Azure Machine Learning Compute.

```Python
import azureml.contrib.dataset
from azureml.contrib.dataset import FileHandlingOption
animal_pd = animal_labels.to_pandas_dataframe(file_handling_option=FileHandlingOption.DOWNLOAD, target_path='./download/', overwrite_download=True)

import matplotlib.pyplot as plt
import matplotlib.image as mpimg

#read images from downloaded path
img = mpimg.imread(animal_pd.loc[0,'image_url'])
imgplot = plt.imshow(img)
```

### <a name="torchvision-datasets"></a>Наборы данных Torchvision

Вы можете загрузить помеченные наборы данных в набор данных `azureml-contrib-dataset` Torchvision с [помощью метода to_torchvision()](https://docs.microsoft.com/python/api/azureml-contrib-dataset/azureml.contrib.dataset.tabulardataset?view=azure-ml-py#to-torchvision--) также из класса. Чтобы использовать этот метод, необходимо установить [PyTorch.](https://pytorch.org/) 

```python
from torchvision.transforms import functional as F

# load animal_labels dataset into torchvision dataset
pytorch_dataset = animal_labels.to_torchvision()
img = pytorch_dataset[0][0]
print(type(img))

# use methods from torchvision to transform the img into grayscale
pil_image = F.to_pil_image(img)
gray_image = F.to_grayscale(pil_image, num_output_channels=3)

imgplot = plt.imshow(gray_image)
```

## <a name="next-steps"></a>Дальнейшие действия

* Для получения полного учебного образца просмотрите [набор данных с блокнотом с метками.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/labeled-datasets/labeled-datasets.ipynb)
