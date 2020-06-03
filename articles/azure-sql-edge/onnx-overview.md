---
title: Машинное обучение и ИИ с применением ONNX в SQL Azure для пограничных вычислений (предварительная версия)
description: Машинное обучение в SQL Azure для пограничных вычислений (предварительная версия) поддерживает модели в формате Open Neural Network Exchange (ONNX). ONNX — это открытый формат, который можно использовать для обмена моделями между различными платформами и инструментами машинного обучения.
keywords: Развертывание SQL для пограничных вычислений
services: sql-edge
ms.service: sql-edge
ms.subservice: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.date: 05/19/2020
ms.openlocfilehash: 3b36052fa3ee0d7d449be1719e67554b539d0207
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/31/2020
ms.locfileid: "84233174"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-edge-preview"></a>Машинное обучение и ИИ с применением ONNX в SQL для пограничных вычислений (предварительная версия)

Машинное обучение в SQL Azure для пограничных вычислений (предварительная версия) поддерживает модели в формате [Open Neural Network Exchange (ONNX)](https://onnx.ai/). ONNX — это открытый формат, который можно использовать для обмена моделями между различными [платформами и инструментами машинного обучения](https://onnx.ai/supported-tools).

## <a name="overview"></a>Обзор

Чтобы определить модели машинного обучения в SQL Azure для пограничных вычислений, сначала необходимо получить модель. Это может быть предварительно обученная модель или пользовательская модель, обученная выбранной платформой. SQL Azure для пограничных вычислений поддерживает формат ONNX, и вам потребуется преобразовать модель в этот формат. На точность модели это повлиять не должно, а при наличии модели ONNX вы можете развернуть ее в SQL Azure для пограничных вычислений и использовать [собственной оценки с функцией PREDICT T-SQL](/sql/advanced-analytics/sql-native-scoring/).

## <a name="get-onnx-models"></a>Получение моделей ONNX

Чтобы получить модель в формате ONNX, выполните следующие действия.

- **Службы для создания моделей**. Такие службы, как [автоматизированная функция Машинного обучения в Машинном обучении Azure](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) и [служба "Пользовательское визуальное распознавание Azure"](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) поддерживают непосредственный экспорт обученной модели в формате ONNX.

- [**Преобразование и/или экспорт имеющихся моделей**](https://github.com/onnx/tutorials#converting-to-onnx-format). В ряде платформ обучения (например, [PyTorch](https://pytorch.org/docs/stable/onnx.html), Chainer и Caffe2) предусмотрены встроенные функциональные возможности экспорта в ONNX, что позволяет сохранить обученную модель в определенной версии формата ONNX. Для платформ без встроенных возможностей экспорта существуют автономно устанавливаемые пакеты преобразователей ONNX, которые позволяют преобразовывать модели, обученные на разных платформах машинного обучения, в формат ONNX.

     **Поддерживаемые платформы**
   * [PyTorch](http://pytorch.org/docs/master/onnx.html)
   * [Tensorflow](https://github.com/onnx/tensorflow-onnx)
   * [Keras](https://github.com/onnx/keras-onnx);
   * [Scikit-learn](https://github.com/onnx/sklearn-onnx)
   * [CoreML](https://github.com/onnx/onnxmltools)
    
    Полный список поддерживаемых платформ и примеры см. в разделе [Преобразование в формат ONNX](https://github.com/onnx/tutorials#converting-to-onnx-format).

## <a name="limitations"></a>Ограничения

В настоящее время SQL Azure для пограничных вычислений поддерживает не все модели ONNX. Поддержка ограничена моделями с **числовыми типами данных**:

- [int и bigint](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql);
- [real и float](https://docs.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql).
  
Другие числовые типы можно преобразовать в поддерживаемые с помощью [CAST и CONVERT](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql).

Входные данные модели должны быть структурированы таким образом, чтобы каждый ввод данных в модель соответствовал одному столбцу в таблице. Например, если для обучения модели используется объект DataFrame Pandas, то каждая входящая порция данных должна быть отдельным столбцом в модели.

## <a name="next-steps"></a>Дальнейшие действия

- [Развертывание SQL Azure для пограничных вычислений с помощью портала Azure](deploy-portal.md)
- [Развертывание модели ONNX в SQL Azure для пограничных вычислений (предварительная версия) ](deploy-onnx.md)
