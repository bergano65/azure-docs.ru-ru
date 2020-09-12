---
title: Создание и исследование наборов данных с метками
titleSuffix: Azure Machine Learning
description: Сведения о том, как экспортировать метки данных из проектов маркировки в Машинном обучении Azure и применить их для задач машинного обучения.
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 05/14/2020
ms.openlocfilehash: 1293534849c98cee51349bbefd3073cc8b94f876
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89647216"
---
# <a name="create-and-explore-azure-machine-learning-dataset-with-labels"></a>Создание и изучение набора данных с метками в Машинном обучении Azure

Из этой статьи вы узнаете, как экспортировать метки данных из проекта маркировки данных в Машинном обучении Azure и выгрузить их в популярные форматы, например в виде кадра данных Pandas для исследования или набора данных Torchvision для преобразования изображений. 

## <a name="what-are-datasets-with-labels"></a>Что такое наборы данных с метками 

В качестве наборов данных можно использовать Машинное обучение Azure наборы данных с метками. Эти конкретные типы наборов данных с помеченными данными создаются только в виде выходных данных Машинное обучение Azure проектов меток. Чтобы создать проект маркировки данных, выполните [эти шаги](how-to-create-labeling-projects.md). Решение "Машинное обучение" поддерживает проекты маркировки данных для классификации изображений (с несколькими метками или классами) и идентификация объектов (с использованием ограничивающих прямоугольников).

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://aka.ms/AMLFree), прежде чем начинать работу.
* [Пакет SDK Python для Машинного обучения Azure](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true) или доступ к [Студии машинного обучения Azure](https://ml.azure.com/).
    * Установка пакета [azure-contrib-dataset](https://docs.microsoft.com/python/api/azureml-contrib-dataset/?view=azure-ml-py&preserve-view=true).
* Рабочая область машинного обучения. См. сведения о [создании рабочей области машинного обучения Azure](how-to-manage-workspace.md).
* Доступ к проекту маркировки данных в Машинном обучении Azure. Если у вас нет проекта маркировки, создайте его по [этой инструкции](how-to-create-labeling-projects.md).

## <a name="export-data-labels"></a>Экспорт меток данных 

После завершения проекта маркировки данных вы можете экспортировать данные метки из этого проекта. Этот процесс позволяет сохранить и ссылки на данные, и присвоенные им метки в виде файла [формата COCO](http://cocodataset.org/#format-data) или набора данных Машинного обучения Azure. Используйте для этого кнопку **Экспорт** на странице **Сведения о проекте** для проекта добавления меток.

### <a name="coco"></a>COCO 

 Файл COCO создается в хранилище больших двоичных объектов по умолчанию для рабочей области Машинного обучения Azure в папке *export/coco*. 

### <a name="azure-machine-learning-dataset"></a>Набор данных службы "Машинное обучение Azure"

Получить доступ к экспортированному набору данных Машинного обучения Azure можно через раздел **Наборы данных** в студии Машинного обучения Azure. На странице **Сведения** набора данных есть пример кода для доступа к меткам из Python.

![Экспортированный набор данных](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="explore-labeled-datasets"></a>Просмотр наборов данных с метками

Вы можете выгрузить наборы данных с метками в формате кадра данных Pandas или набора данных Torchvision, чтобы применить популярные библиотеки с открытым кодом для просмотра этих данных или предоставленные библиотеки PyTorch для преобразования изображений и обучения.

### <a name="pandas-dataframe"></a>Кадр данных Pandas

Чтобы выгрузить наборы данных с метками в кадр данных Pandas, используйте метод [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#&preserve-view=trueto-pandas-dataframe-on-error--null---out-of-range-datetime--null--) из класса `azureml-contrib-dataset`. Чтобы установить этот класс, выполните следующую команду: 

```shell
pip install azureml-contrib-dataset
```

>[!NOTE]
>Пространство имен azureml.contrib часто изменяется, так как мы работаем над улучшением службы. Поэтому все, что доступно в этом пространстве имен, считается предварительными версиями компонентов, поддержка которых корпорацией Майкрософт ограничена.

Мы предлагаем следующие средства обработки файлов для создания файловых потоков при преобразовании в кадры данных Pandas.
* Скачать: скачайте файлы данных в локальный каталог.
* Подключение. Подключите файлы данных к точке подключения. Подключение работает только для вычислительных ресурсов на основе Linux, включая виртуальные машины для записных книжек Машинного обучения Azure и Вычислительную среду Машинного обучения.

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

Вы можете выгрузить наборы данных с метками в набор данных Torchvision с помощью метода [to_torchvision()](https://docs.microsoft.com/python/api/azureml-contrib-dataset/azureml.contrib.dataset.tabulardataset?view=azure-ml-py#&preserve-view=trueto-torchvision--), который также размещен в классе `azureml-contrib-dataset`. Чтобы использовать этот метод, необходимо установить [PyTorch](https://pytorch.org/). 

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

* Полный обучающий пример записной книжки для набора данных с метками см. [здесь](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/labeled-datasets/labeled-datasets.ipynb).
