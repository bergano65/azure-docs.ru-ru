---
title: Примеры записных книжек Jupyter с использованием открытого набора данных Национального управления океанических и атмосферных исследований США
titleSuffix: Azure Open Datasets
description: Воспользуйтесь примерами записных книжек Jupyter для открытых наборов данных Azure, чтобы научиться загружать открытые наборы данных и обогащать с их помощью демонстрационные данные. Для обработки данных используются такие техники, как Spark и Pandas.
ms.service: open-datasets
ms.topic: sample
author: cjgronlund
ms.author: cgronlun
ms.date: 05/02/2019
ms.openlocfilehash: c1b86199f13454f4785a6737b25e489d45dd53f8
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027550"
---
# <a name="example-jupyter-notebooks-show-how-to-enrich-data-with-open-datasets"></a>Примеры записных книжек Jupyter демонстрируют, как обогащать данные с помощью открытых наборов данных 
Примеры записных книжек Jupyter для открытых наборов данных Azure демонстрируют, как загружать отрытые наборы данных и обогащать с их помощью демонстрационные данные. Для обработки данных используются такие техники, как Apache Spark и Pandas.

>[!IMPORTANT]
>При работе в среде, отличной от Spark, открытые наборы данных позволяют скачать за раз данные определенных классов только за один месяц. Это ограничение помогает избежать ошибок памяти при использовании больших наборов данных.

## <a name="load-noaa-integrated-surface-database-isd-data"></a>Загрузка данных из базы данных Integrated Surface Database (ISD) Национального управления океанических и атмосферных исследований США (NOAA) 
|Заметки        | ОПИСАНИЕ                                    |
|----------------|------------------------------------------------|
|[Загрузка метеоданных за один последний месяц в кадр данных Pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/02-weather-to-pandas-dataframe.ipynb) | Узнайте, как загрузить хронологические метеоданные в предпочитаемый кадр данных Pandas. |
|[Загрузка одного последнего месяца метеоданных в кадр данных Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/01-weather-to-spark-dataframe.ipynb) | Узнайте, как загрузить исторические метеоданные в предпочитаемый кадр данных Spark.  |

## <a name="join-demo-data-with-noaa-isd-data"></a>Объединение демонстрационных данных с данными ISD NOAA 
|Заметки        | ОПИСАНИЕ                                    |
|----------------|------------------------------------------------|
|[Объединение демонстрационных данных с метеоданными — Pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/02-weather-join-in-pandas.ipynb) | Объедините демонстрационный набор данных о положениях датчиков за 1 месяц с метеоданными в кадре данных Pandas.  |
|[Объединение демонстрационных данных с метеоданными — Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/01-weather-join-in-spark.ipynb) | Объедините демонстрационный набор данных о положениях датчиков с метеоданными в кадре данных Spark. |

## <a name="join-nyc-taxi-data-with-noaa-isd-data"></a>Объединение данных о такси Нью-Йорка с данными ISD NOAA 
|Заметки        | ОПИСАНИЕ                                    |
|----------------|------------------------------------------------|
|[Данные о поездках в такси, обогащенные метеоданными — Pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/04-nyc-taxi-join-weather-in-pandas.ipynb) | Загрузите данные о зеленых такси в Нью-Йорке (более чем за 1 месяц) и обогатите их метеоданными в кадре данных Pandas. Этот пример переопределяет метод `get_pandas_limit` и выполняет балансировку производительности нагрузки с учетом объема данных.|
|[Данные о поездках в такси, обогащенные метеоданными — Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/03-nyc-taxi-join-weather-in-spark.ipynb) | Загрузите данные о зеленых такси в Нью-Йорке и обогатите их метеоданными в кадре данных Spark.  |

## <a name="next-steps"></a>Дополнительная информация

* [Руководство Создание регрессионной модели с помощью автоматизированного машинного обучения и открытого набора данных](tutorial-opendatasets-automl.md)
* [Пакет SDK для Python для открытых наборов данных](https://aka.ms/open-datasets-api)
* [Каталог открытых наборов данных Azure](https://azure.microsoft.com/services/open-datasets/catalog/)
