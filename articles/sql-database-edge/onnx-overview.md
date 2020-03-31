---
title: Машинное обучение и ИИ с ONNX в Azure S'L База данных Edge Предварительный (ru) Документы Майкрософт
description: Машинное обучение в Azure S'L Database Edge Preview поддерживает модели в формате Open Neural Network Exchange (ONNX). ONNX — это открытый формат, который можно использовать для обмена моделями между различными рамками и инструментами машинного обучения.
keywords: развертывание края базы данных sql
services: sql-database-edge
ms.service: sql-database-edge
ms.subservice: machine-learning
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.date: 03/26/2020
ms.openlocfilehash: 7813a08b6b18e517b81e8c4bfac660d198eba7f7
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366271"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-database-edge-preview"></a>Машинное обучение и иик с ONNX в Edge базы данных S'L Предварительный

Машинное обучение в Azure S'L Database Edge Preview поддерживает модели в формате [Open Neural Network Exchange (ONNX).](https://onnx.ai/) ONNX — это открытый формат, который можно использовать для обмена моделями между [различными рамками машинного обучения и инструментами.](https://onnx.ai/supported-tools)

## <a name="overview"></a>Обзор

Чтобы сделать вывод о моделях машинного обучения в Azure S'L Database Edge, сначала необходимо получить модель. Это может быть предварительно обученная модель или пользовательская модель, обученная с помощью подборки. Azure S'L Database Edge поддерживает формат ONNX, и вам нужно будет преобразовать модель в этот формат. Точность модели не должна влиять, и как только у вас есть модель ONNX, вы можете развернуть модель в Azure S'L Database Edge и использовать [нативную скоринг с функцией PREDICT T-S'L.](/sql/advanced-analytics/sql-native-scoring/)

## <a name="get-onnx-models"></a>Получение моделей ONNX

Для получения модели в формате ONNX:

- **Службы построения моделей:** Службы [автоматического машинного обучения в Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) и служба [пользовательского зрения Azure](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) поддерживают непосредственно экспортируемую модель в формате ONNX.

- [**Преобразование и/или экспорт существующих моделей:**](https://github.com/onnx/tutorials#converting-to-onnx-format)Несколько обучаемых рамок (например, [PyTorch,](https://pytorch.org/docs/stable/onnx.html)Chainer и Caffe2) поддерживают функциональность родного экспорта в ONNX, что позволяет сохранить подготовленную модель для конкретной версии формата ONNX. Для инфраструктур, не поддерживающих родной экспорт, имеются автономные пакеты ONNX Converter, которые позволяют преобразовывать модели, подготовленные из различных инфраструктур машинного обучения, в формат ONNX.

     **Поддерживаемые платформы**
   * [PyTorch](http://pytorch.org/docs/master/onnx.html)
   * [Тенсорпоток](https://github.com/onnx/tensorflow-onnx)
   * [Keras](https://github.com/onnx/keras-onnx)
   * [Scikit-учиться](https://github.com/onnx/sklearn-onnx)
   * [CoreML](https://github.com/onnx/onnxmltools)
    
    Полный список поддерживаемых фреймворков и примеров можно найти в [формате «Преобразование» в формате ONNX.](https://github.com/onnx/tutorials#converting-to-onnx-format)

## <a name="limitations"></a>Ограничения

В настоящее время не все модели ONNX поддерживаются Azure S'L Database Edge. Поддержка ограничена моделями с **числовыми типами данных:**

- [int и bigint](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql)
- [реальные и плавать](https://docs.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql).
  
Другие числовые типы могут быть преобразованы в поддерживаемые типы с помощью [CAST и CONVERT.](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql)

Входные данные модели должны быть структурированы таким образом, чтобы каждый вход в модель соответствовал одному столбце в таблице. Например, если для обучения модели используется каркас данных панд для обучения модели, каждый вход должен быть отдельным столбцом для модели.

## <a name="next-steps"></a>Дальнейшие действия

- [Развертывание края базы данных с помощью портала Azure](deploy-portal.md)
- [Развертывание модели ONNX на edge базы данных Azure S'L](deploy-onnx.md)
