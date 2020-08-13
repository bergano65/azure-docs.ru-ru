---
title: Создание наборов данных с помощью открытых наборов данных Azure
titleSuffix: Azure Open Datasets
description: Узнайте, как создать набор данных Машинное обучение Azure из набора открытых данных Azure.
ms.service: open-datasets
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.date: 08/05/2020
ms.custom: how-to, tracking-python
ms.openlocfilehash: e6f3a541f1e9dbca2c9949fb0c5cde28cd43e8e5
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2020
ms.locfileid: "88183065"
---
# <a name="create-azure-machine-learning-datasets-from-azure-open-datasets"></a>Создание наборов данных Машинное обучение Azure из открытых наборов данных Azure
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Из этой статьи вы узнаете, как создать набор данных Машинное обучение Azure из [Azure Open](https://azure.microsoft.com/services/open-datasets/) DataSets для доступа к данным в локальных и удаленных экспериментах. 

Создавая [набор данных машинное обучение Azure](../machine-learning/how-to-create-register-datasets.md), вы создаете ссылку на расположение источника данных вместе с копией его метаданных. Поскольку данные остаются в существующем расположении, не взимается дополнительная плата за хранение, и не риску непреднамеренного изменения исходных источников данных. Кроме того, наборы данных отложенно оцениваются, что помогает в производительности рабочих процессов.
 
Чтобы узнать, где находятся наборы данных в общем рабочем процессе доступа к данным Машинное обучение Azure, ознакомьтесь со статьей [безопасный доступ к](../machine-learning/concept-data.md#data-workflow) данным.


Открытые наборы данных находятся в облаке на Microsoft Azure и включены в [пакет SDK для машинное обучение Azure Python](#create-datasets-with-the-sdk) и в [машинное обучение Azure Studio](#create-datasets-with-the-studio).

## <a name="why-use-azure-open-datasets"></a>Зачем использовать открытые наборы данных Azure? 

Открытые наборы данных Azure — это проверенные общедоступные наборы данных, которые можно использовать для добавления функций конкретных сценариев в решения машинного обучения для создания более точных моделей. Наборы данных включают открытые данные о погоде, численности населения, праздниках, общественной безопасности и расположениях, которые помогают вам обучать модели машинного обучения и обогащать прогностические решения. 

Дополнительные сведения см. в разделе [что представляют собой открытые наборы данных?](overview-what-are-open-datasets.md)

## <a name="prerequisites"></a>Предварительные требования

Для создания наборов данных и работы с ними требуется:

* Подписка Azure. Если у вас еще нет подписки Azure, создайте бесплатную учетную запись, прежде чем начать работу. Попробуйте [бесплатную или платную версию Машинного обучения Azure](https://aka.ms/AMLFree).

* [Рабочая область машинное обучение Azure](../machine-learning/how-to-manage-workspace.md).

* [Установленный пакет SDK для машинное обучение Azure для Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), включающий пакет azureml-DataSets.

    * Создание [машинное обучение Azure вычислительного экземпляра](../machine-learning/concept-compute-instance.md#managing-a-compute-instance), который представляет собой полностью настроенную и управляемую среду разработки, включающую интегрированные записные книжки и уже установленный пакет SDK.

    **OR**

    * Поработайте с собственной записной книжкой Jupyter и установите пакет SDK самостоятельно с помощью [этих инструкций](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

> [!NOTE]
> Некоторые классы наборов данных имеют зависимости от пакета [azureml-](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) DataMarket, который совместим только с 64-разрядным Python. Для пользователей Linux эти классы поддерживаются только в следующих дистрибутивах: Red Hat Enterprise Linux (7, 8), Ubuntu (14,04, 16,04, 18,04), Fedora (27, 28), Debian (8, 9) и CentOS (7).

## <a name="create-datasets-with-the-sdk"></a>Создание наборов данных с помощью пакета SDK

Чтобы создать наборы данных с помощью открытых классов наборов данных в пакете SDK для Машинное обучение Azure Python, убедитесь, что пакет установлен с помощью `pip install azureml-opendatasets` . Каждый дискретный набор данных представлен своим собственным классом в пакете SDK, а некоторые классы доступны как `TabularDataset` , `FileDataset` или оба. Полный список классов см. в [справочной документации](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py) .

Некоторые классы можно извлечь как `TabularDataset` или `FileDataset` , что позволяет напрямую управлять файлами и/или скачивать их. Другие классы могут получить набор данных **только** с помощью одной `get_tabular_dataset()` функции или `get_file_dataset()` . В следующем образце кода приведено несколько примеров этих типов классов.

```python
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()

from azureml.opendatasets import Diabetes

# Diabetes class can return ONLY TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```

При регистрации набора данных, созданного из открытых наборов данных, данные не загружаются немедленно, но доступ к ним будет осуществляться позже при запросе (например, во время обучения) из центрального хранилища.

## <a name="create-datasets-with-the-studio"></a>Создание наборов данных с помощью студии

Наборы данных можно также создавать из открытых наборов данных с помощью [машинное обучение Azure Studio](https://ml.azure.com).

1. В рабочей области выберите вкладку **наборы данных** в разделе **активы**. В раскрывающемся меню **CREATE DataSet (создание набора данных** ) выберите пункт **из открытых наборов**.

    ![Открытие набора данных с помощью пользовательского интерфейса](./media/how-to-create-dataset-from-open-dataset/open-datasets-1.png)

1. Выберите набор данных, выбрав его плитку. (Можно выполнить фильтрацию с помощью панели поиска.) Нажмите кнопку **Далее**.

    ![Выбор набора данных](./media/how-to-create-dataset-from-open-dataset/open-datasets-2.png)

1. Выберите имя, под которым будет зарегистрирован набор данных, и при необходимости отфильтруйте данные с помощью доступных фильтров. В этом случае для набора данных " **общедоступные праздники** " вы фильтруете период времени в один год и код страны только в США. Нажмите кнопку **создания**.

    ![Установка параметров набора данных и создание набора данных](./media/how-to-create-dataset-from-open-dataset/open-datasets-3.png)

    Набор данных теперь доступен в рабочей области в разделе **наборы данных**. Его можно использовать точно так же, как и другие созданные наборы данных.


## <a name="access-datasets-for-your-experiments"></a>Доступ к наборам данных для экспериментов

Используйте свои наборы данных в экспериментах машинного обучения для обучения моделей ML. [Узнайте больше об обучении с наборами данных](../machine-learning/how-to-train-with-datasets.md).

## <a name="example-notebooks"></a>Примеры записных книжек

Примеры и демонстрации функций открытых наборов данных см. в этих [записных книжках](https://github.com/Azure/OpenDatasetsNotebooks/tree/master/tutorials).

## <a name="next-steps"></a>Дальнейшие действия

* [Обучение модели с помощью наборов данных](../machine-learning/how-to-train-with-datasets.md).

* [Создайте набор данных машинного обучения Azure](../machine-learning/how-to-create-register-datasets.md).



