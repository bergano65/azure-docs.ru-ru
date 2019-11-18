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
ms.openlocfilehash: bdb602598f3d8b4aaed5d6061542d540a82ebc75
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74114601"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-database-edge-preview"></a>Машинное обучение и AI с ONNX в предварительной версии базы данных SQL

Машинное обучение в предварительной версии базы данных SQL Azure для предварительного просмотра поддерживает модели в формате [Open нейронной сети (ONNX)](https://onnx.ai/) . ONNX — это открытый формат, который можно использовать для обмена моделями между различными [платформами и инструментами машинного обучения](https://onnx.ai/supported-tools).

## <a name="overview"></a>Обзор

Чтобы вычислять модели машинного обучения в пограничных базах данных SQL Azure, сначала необходимо получить модель. Это может быть предварительно обученная модель или пользовательская модель, обученная вашей инфраструктурой по выбору. Служба "границы базы данных SQL Azure" поддерживает формат ONNX, и вам потребуется преобразовать модель в этот формат. На точность модели не должно повлиять, и, если у вас есть модель ONNX, можно развернуть модель в области базы данных SQL Azure и использовать [собственную оценку с функцией прогнозирования T-SQL](/sql/advanced-analytics/sql-native-scoring/).

## <a name="get-onnx-models"></a>Получение моделей ONNX

Чтобы получить модель в формате ONNX, выполните следующие действия.

- **Службы создания моделей**. такие службы, как [автоматизированная функция машинное обучение в Машинное обучение Azure](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) и [Azure пользовательская служба визуального распознавания](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) , поддерживают непосредственное экспорт обученной модели в формате ONNX.

- [**Преобразование и (или) экспорт существующих моделей**](https://github.com/onnx/tutorials#converting-to-onnx-format). несколько платформ обучения (например, [PyTorch](https://pytorch.org/docs/stable/onnx.html), формирователь и Caffe2) поддерживают встроенные функции экспорта в ONNX, что позволяет сохранить обученную модель в определенной версии формата ONNX. Для платформ, которые не поддерживают собственный экспорт, существуют автономные пакеты преобразователей ONNX, которые позволяют преобразовывать модели, обученные из разных платформ машинного обучения, в формат ONNX.

     **Поддерживаемые платформы**
   * [PyTorch](http://pytorch.org/docs/master/onnx.html)
   * [Tensorflow](https://github.com/onnx/tensorflow-onnx)
   * [Keras](https://github.com/onnx/keras-onnx);
   * [Scikit-learn](https://github.com/onnx/sklearn-onnx)
   * [CoreML](https://github.com/onnx/onnxmltools)
    
    Полный список поддерживаемых платформ и примеры см. в разделе [Преобразование в формат ONNX](https://github.com/onnx/tutorials#converting-to-onnx-format).

## <a name="limitations"></a>Ограничения

В настоящее время с помощью границы базы данных SQL Azure поддерживаются не все модели ONNX. Поддержка ограничена моделями с **числовыми типами данных**:

- [int и bigint](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql)
- [Real и float](https://docs.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql).
  
Другие числовые типы можно преобразовать в поддерживаемые типы с помощью функций [CAST и Convert](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql).

Входные данные модели должны быть структурированы таким образом, чтобы каждый вход модели соответствовал одному столбцу в таблице. Например, если для обучения модели используется кадр данных Pandas, то каждый вход должен быть отдельным столбцом в модели.

## <a name="next-steps"></a>Дополнительная информация

- [Развертывание пограничных баз данных SQL с помощью портал Azure](deploy-portal.md)
- [Развертывание модели ONNX в предварительной версии базы данных SQL Azure](deploy-onnx.md)
