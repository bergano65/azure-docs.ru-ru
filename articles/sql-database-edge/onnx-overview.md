---
title: Машинное обучение и AI с ONNX в предварительной версии базы данных SQL Azure | Документация Майкрософт
description: Машинное обучение в предварительной версии базы данных SQL Azure для предварительного просмотра поддерживает модели в формате Open нейронной сети (ONNX). ONNX — это открытый формат, который можно использовать для обмена моделями между различными платформами и инструментами машинного обучения.
keywords: Развертывание границы базы данных SQL
services: sql-database-edge
ms.service: sql-database-edge
ms.subservice: machine-learning
ms.topic: conceptual
author: ronychatterjee
ms.author: achatter
ms.reviewer: davidph
ms.date: 11/07/2019
ms.openlocfilehash: 976c849f9cb48e1c197f70d10e911216a6a7425c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822848"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-database-edge-preview"></a>Машинное обучение и AI с ONNX в предварительной версии базы данных SQL

Машинное обучение в предварительной версии базы данных SQL Azure для предварительного просмотра поддерживает модели в формате [Open нейронной сети (ONNX)](https://onnx.ai/) . ONNX — это открытый формат, который можно использовать для обмена моделями между различными [платформами и инструментами машинного обучения](https://onnx.ai/supported-tools).

## <a name="overview"></a>Обзор

Чтобы вычислять модели машинного обучения в пограничных базах данных SQL Azure, сначала необходимо получить модель. Это может быть предварительно обученная модель или пользовательская модель, обученная вашей инфраструктурой по выбору. Служба "границы базы данных SQL Azure" поддерживает формат ONNX, и вам потребуется преобразовать модель в этот формат. На точность модели не должно повлиять, и, если у вас есть модель ONNX, можно развернуть модель в области базы данных SQL Azure и использовать [собственную оценку с функцией прогнозирования T-SQL](/sql/advanced-analytics/sql-native-scoring/).

## <a name="get-onnx-models"></a>Получение моделей ONNX

Существует несколько способов получить модель в формате ONNX:

- [ONNX Model Zoo](https://github.com/onnx/models): содержит множество предварительно обученных моделей ONNX для различных типов задач, которые могут быть скачаны и готовы к использованию.

- [Собственный экспорт из платформ обучения ML](https://onnx.ai/supported-tools). несколько платформ обучения поддерживают собственные функции экспорта в ONNX, что позволяет сохранять обученную модель в определенной версии формата ONNX, включая [PyTorch](https://pytorch.org/docs/stable/onnx.html), Chain и Caffe2. Кроме того, службы создания моделей, такие как [Автоматизированная функция машинное обучение в машинное обучение Azure](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) и [Azure ПОЛЬЗОВАТЕЛЬСКАЯ служба визуального распознавания](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) обеспечивают экспорт ONNX.

- [Преобразование существующих моделей](https://github.com/onnx/tutorials#converting-to-onnx-format). для платформ, которые не поддерживают собственный экспорт, существуют автономные пакеты для преобразования моделей в формат ONNX. Примеры и учебники см. [в разделе Преобразование в формат ONNX](https://github.com/onnx/tutorials#converting-to-onnx-format). 

### <a name="supported-frameworks"></a>Поддерживаемые платформы

Преобразователи ONNX позволяют преобразовывать модели, обученные из разных платформ машинного обучения, в формат ONNX. К популярным конвертерам относятся: 

* [PyTorch](http://pytorch.org/docs/master/onnx.html)
* [Tensorflow](https://github.com/onnx/tensorflow-onnx)
* [Keras](https://github.com/onnx/keras-onnx);
* [Scikit-learn](https://github.com/onnx/sklearn-onnx)
* [CoreML](https://github.com/onnx/onnxmltools)

Полный список поддерживаемых платформ см. в разделе [Преобразование в формат ONNX](https://github.com/onnx/tutorials#converting-to-onnx-format).

## <a name="limitations"></a>Ограничения

В настоящее время с помощью границы базы данных SQL Azure поддерживаются не все модели ONNX. Поддержка ограничена моделями с **числовыми типами данных**:

- [int и bigint](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql5)
- [Real и float](https://docs.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql).
  
Другие числовые типы можно преобразовать в поддерживаемые типы с помощью функций [CAST и Convert](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql).

Входные данные модели должны быть структурированы таким образом, чтобы каждый вход модели соответствовал одному столбцу в таблице. Например, если для обучения модели используется кадр данных Pandas, то каждый вход должен быть отдельным столбцом в модели.

## <a name="next-steps"></a>Дальнейшие действия

- [Развертывание пограничных баз данных SQL с помощью портал Azure](deploy-portal.md)
- [Развертывание модели ONNX в предварительной версии базы данных SQL Azure](deploy-onnx.md)
