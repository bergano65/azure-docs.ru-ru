---
title: Водоемы данных с azure Data Factory
titleSuffix: Azure Machine Learning
description: Узнайте, как построить конвейер приема данных с Azure Data Factory.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 03/01/2020
ms.openlocfilehash: 5b4ed40048aab815397c9726098880b2125b732c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78274792"
---
# <a name="data-ingestion-with-azure-data-factory"></a>Водоемы данных с azure Data Factory

В этой статье вы узнаете, как построить конвейер приема данных с Azure Data Factory (ADF). Этот конвейер используется для использования данных в Azure Machine Learning. Фабрика данных Azure позволяет легко извлекать, преобразовывать и загружать (ETL) данные. После того, как данные были преобразованы и загружены в хранилище, они могут быть использованы для обучения моделей машинного обучения.

Простая трансформация данных может быть обработана с помощью наиболее местных видов деятельности и инструментов ADF, таких как [поток данных.](https://docs.microsoft.com/azure/data-factory/control-flow-execute-data-flow-activity) Когда дело доходит до более сложных сценариев, данные могут быть обработаны с некоторым пользовательским кодом. Например, Python или R-код.

Существует несколько распространенных методов использования фабрики данных Azure для преобразования данных во время приема. Каждый метод имеет плюсы и минусы, которые определяют, если это хорошо подходит для конкретного случая использования:

| Метод | Плюсы | Минусы |
| ----- | ----- | ----- |
| Функции ADF и Azure | Низкая задержка, без серверные вычисления</br>Государственные функции</br>Многоразовые функции | Только хорошо для короткой обработки |
| ADF - пользовательский компонент | Крупномасштабные параллельные вычисления</br>Подходит для тяжелых алгоритмов | Упаковка кода в исполняемый</br>Сложность обработки зависимостей и итогового |
| Блокнот ADF и Azure Databricks | Apache Spark</br>Окружающая среда родного питона | Может быть дорогим</br>Создание кластеров изначально требует времени и добавляет задержки

## <a name="adf-with-azure-functions"></a>ADF с функциями Azure

![adf-функция](media/how-to-data-ingest-adf/adf-function.png)

Функции Azure позволяют запускать небольшие фрагменты кода (функции), не беспокоясь об инфраструктуре приложений. В этом варианте данные обрабатываются с помощью пользовательского кода Python, завернутые в функцию Azure. 

Функция вызывается с [функцией функции ADF Azure Function.](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity) Этот подход является хорошим вариантом для легких преобразований данных. 

* Преимущества.
    * Данные обрабатываются на бессерверных вычислениях с относительно низкой задержкой
    * Конвейер ADF может вызывать [функцию «Прочная azure»,](/azure/azure-functions/durable/durable-functions-overview) которая может реализовать сложный поток преобразования данных 
    * Детали преобразования данных абстрагируются функцией Azure, которая может быть повторно использована и вызвана из других мест
* Недостатки.
    * Функции Azure должны быть созданы перед использованием с Помощью ADF
    * Функции Azure хороши только для короткой обработки данных

## <a name="adf-with-custom-component-activity"></a>ADF с пользовательской активностью компонента

![adf-customcomponent](media/how-to-data-ingest-adf/adf-customcomponent.png)

В этом варианте данные обрабатываются с помощью пользовательского кода Python, завернутые в исполняемый. Он вызывается с [aDF пользовательский компонент деятельности.](https://docs.microsoft.com/azure/data-factory/transform-data-using-dotnet-custom-activity) Этот подход лучше подходит для больших данных, чем предыдущий метод.

* Преимущества.
    * Данные обрабатываются в пуле [Azure Batch,](https://docs.microsoft.com/azure/batch/batch-technical-overview) который обеспечивает крупномасштабные параллельные и высокопроизводительные вычисления
    * может использоваться для запуска тяжелых алгоритмов и обработки значительных объемов данных
* Недостатки.
    * Перед использованием aDF должен быть создан пул azure Batch
    * Над инженерией, связанной с обертыванием кода Python в исполняемый. Сложность обработки зависимостей и параметров ввода/вывода

## <a name="adf-with-azure-databricks-python-notebook"></a>ADF с ноутбуком Azure Databricks Python

![adf-databricks](media/how-to-data-ingest-adf/adf-databricks.png)

[Azure Databricks](https://azure.microsoft.com/services/databricks/) — это аналитическая платформа Apache Spark в облаке Майкрософт.

В этом методе преобразование данных выполняется [блокнотом Python,](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook)работающим в кластере Azure Databricks. Это, вероятно, наиболее распространенный подход, который использует всю мощь службы Databricks Azure. Предназначен для обработки распределенных данных в масштабе.

* Преимущества.
    * Данные преобразуются в самую мощную службу обработки данных Azure, которая поддерживается средой Apache Spark
    * Поддержка Python наряду с инфраструктурой и библиотеками, включая TensorFlow, PyTorch и scikit-learn
    * Нет необходимости заворачивать код Python в функции или исполняемые модули. Код работает как есть.
* Недостатки.
    * Инфраструктура Azure Databricks должна быть создана перед использованием с Помощью ADF
    * Может быть дорогим в зависимости от конфигурации Azure Databricks
    * Для создания вычислительных кластеров из "холодного" режима требуется некоторое время, что приводит к высокой задержке решения 
    

## <a name="consuming-data-in-azure-machine-learning-pipelines"></a>Потребление данных в конвейерах машинного обучения Azure

![амл-набор данных](media/how-to-data-ingest-adf/aml-dataset.png)

Преобразованные данные из конвейера ADF сохраняются для хранения данных (например, Azure Blob). Azure Machine Learning может получить доступ к этим данным с помощью [хранилищ данных](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores) и [наборов данных.](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets)

Каждый раз, когда конвейер ADF работает, данные сохраняются в другом месте в хранилище. Чтобы передать местоположение в Azure Machine Learning, конвейер ADF вызывает конвейер Azure Machine Learning. При вызове конвейера ML местоположение данных и идентификатор запуска отправляются в качестве параметров. Затем конвейер ML может создать хранилище данных/набор данных с использованием местоположения данных. 

> [!TIP]
> Наборы данных [поддерживают версии,](https://docs.microsoft.com/azure/machine-learning/how-to-version-track-datasets)поэтому конвейер ML может зарегистрировать новую версию набора данных, которая указывает на самые последние данные из конвейера ADF.

Как только данные доступны через хранилище данных или набор данных, вы можете использовать их для обучения модели ML. Учебный процесс может быть частью того же конвейера ML, который называется от ADF. Или это может быть отдельный процесс, такой как эксперименты в блокноте Jupyter.

Поскольку наборы данных поддерживают версии, и каждый запуск от конвейера создает новую версию, легко понять, какая версия данных была использована для обучения модели.

## <a name="next-steps"></a>Дальнейшие действия

* [Выполнить блокнот Databricks на фабрике данных Azure](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook)
* [Доступ к данным в службах хранения данных Azure](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores)
* [Модели поездов с наборами данных в Машинном обучении Azure](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets)
* [DevOps для конвейера по проглатке данных](https://docs.microsoft.com/azure/machine-learning/how-to-cicd-data-ingestion)

