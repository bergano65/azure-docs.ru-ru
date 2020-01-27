---
title: Создание и изучение наборов данных с метками
titleSuffix: Azure Machine Learning
description: Узнайте, как экспортировать метки данных из Машинное обучение Azure меток проектов и использовать их для задач машинного обучения.
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.topic: how-to
ms.date: 01/21/2020
ms.openlocfilehash: 5138109de3f80d405ce95b605714b511480563f5
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76549493"
---
# <a name="create-and-explore-azure-machine-learning-dataset-with-labels"></a>Создание и изучение набора данных Машинное обучение Azure с помощью меток

В этой статье вы узнаете, как экспортировать метки данных из проекта с Машинное обучение Azureными метками данных и загрузить их в популярные форматы, такие как, кадр данных Pandas для исследования или Торчвисион набор данных для преобразования изображений. 

## <a name="what-are-datasets-with-labels"></a>Что такое наборы данных с метками 

Машинное обучение Azure наборы данных с метками [табулардатасетс](how-to-create-register-datasets.md#dataset-types) со свойством Label, мы будем ссылаться на них как на помеченные наборы данных. Эти конкретные типы Табулардатасетс создаются только в виде выходных данных Машинное обучение Azure проектов меток. Создайте проект меток данных, [выполнив следующие действия](how-to-create-labeling-projects.md). Машинное обучение поддерживает проекты меток данных для классификации изображений: с несколькими метками или с несколькими классами, а также с ограниченными полями.

## <a name="prerequisites"></a>Технические условия

* Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://aka.ms/AMLFree), прежде чем начинать работу.
* [Машинное обучение Azure пакет SDK для Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)или доступ к [машинное обучение Azure Studio](https://ml.azure.com/).
    * Установка пакета [набора данных Azure-от участников сообщества-DataSet](https://docs.microsoft.com/python/api/azureml-contrib-dataset/?view=azure-ml-py)
* Рабочая область машинного обучения. См. сведения о [создании рабочей области машинного обучения Azure](how-to-manage-workspace.md).
* Доступ к Машинное обучение Azure проекту меток данных. Если у вас нет проекта с метками, создайте его с помощью [этих шагов](how-to-create-labeling-projects.md).

## <a name="export-data-labels"></a>Экспорт меток данных 

После завершения проекта меток данных можно экспортировать данные метки из проекта меток. Это позволяет записывать как ссылку на данные, так и их метки, а также экспортировать их в [формате Coco](http://cocodataset.org/#format-data) или в виде набора данных машинное обучение Azure. Используйте для этого кнопку **Экспорт** на странице **Сведения о проекте** для проекта добавления меток.

### <a name="coco"></a>COCO 

 Файл COCO создается в хранилище больших двоичных объектов по умолчанию для рабочей области Машинного обучения Azure в папке *export/coco*. 

### <a name="azure-machine-learning-dataset"></a>Набор данных службы "Машинное обучение Azure"

Можно получить доступ к экспортированному набору данных Машинное обучение Azure в разделе **наборы** данных в машинное обучение Azure Studio. На странице **сведений о** наборе данных также приведен пример кода для доступа к меткам из Python.

![Экспортированный набор данных](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="explore-labeled-datasets"></a>Изучение наборов данных с метками

Загрузите наборы данных с метками в Pandas кадров или Торчвисион DataSet, чтобы использовать популярные библиотеки с открытым исходным кодом для просмотра данных, а также библиотеки PyTorch для преобразования изображений и обучения.

### <a name="pandas-dataframe"></a>Кадр данных Pandas

Вы можете загрузить помеченные наборы данных в Pandas, используя метод [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) из класса `azureml-contrib-dataset`. Установите класс с помощью следующей команды оболочки: 

```shell
pip install azureml-contrib-dataset
```

>[!NOTE]
>Пространство имен azureml. от участников сообщества часто меняется, так как мы работаем над улучшением службы. Поэтому все, что доступно в этом пространстве имен, считается предварительными версиями компонентов, поддержка которых корпорацией Майкрософт ограничена.

Мы предлагаем следующие варианты обработки файлов для файловых потоков при преобразовании в кадр данных Pandas.
* Скачать: Скачайте файлы данных по локальному пути.
* Подключение. Подключите файлы данных к точке подключения. Подключение работает только для вычислений на основе Linux, включая Машинное обучение Azure виртуальной машины записной книжки и Машинное обучение Azure вычислений.

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

### <a name="torchvision-datasets"></a>Наборы данных торчвисион

Вы можете загрузить помеченные наборы данных в Торчвисион DataSet с помощью метода [to_torchvision ()](https://docs.microsoft.com/python/api/azureml-contrib-dataset/azureml.contrib.dataset.tabulardataset?view=azure-ml-py#to-torchvision--) также из класса `azureml-contrib-dataset`. Чтобы использовать этот метод, необходимо установить [PyTorch](https://pytorch.org/) . 

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

* Полный обучающий пример см. в статье [набор данных с метками Записная книжка](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/labeled-datasets/labeled-datasets.ipynb) .
