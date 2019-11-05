---
title: Машинное обучение и AI с ONNX в предварительной версии базы данных SQL Azure | Документация Майкрософт
description: Машинное обучение в предварительной версии базы данных SQL Azure для предварительного просмотра поддерживает модели в формате Open нейронной сети (ONNX). ONNX — это открытый формат, который можно использовать для обмена моделями между различными платформами и инструментами машинного обучения.
keywords: Развертывание границы базы данных SQL
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: ronychatterjee
ms.author: achatter
ms.reviewer: davidph
ms.date: 11/04/2019
ms.openlocfilehash: c4c87f7f6f8735c9a50c61b0e083c77b915e0d98
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514021"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-database-edge-preview"></a>Машинное обучение и AI с ONNX в предварительной версии базы данных SQL

Машинное обучение в предварительной версии базы данных SQL Azure для предварительного просмотра поддерживает модели в формате [Open нейронной сети (ONNX)](https://onnx.ai/) . ONNX — это открытый формат, который можно использовать для обмена моделями между различными [платформами и инструментами машинного обучения](https://onnx.ai/supported-tools).

## <a name="supported-tool-kits"></a>Поддерживаемые наборы средств

Оннксмлтулс позволяет преобразовывать модели из различных наборов средств машинного обучения в модель ONNX. В настоящее время для числовых типов данных и одиночных входных столбцов поддерживаются следующие наборы средств:

* [scikit-learn](https://github.com/onnx/sklearn-onnx);
* [Tensorflow](https://github.com/onnx/tensorflow-onnx)
* [Keras](https://github.com/onnx/keras-onnx);
* [CoreML](https://github.com/onnx/onnxmltools)
* [Spark ML (экспериментальная версия)](https://github.com/onnx/onnxmltools/tree/master/onnxmltools/convert/sparkml)
* [LightGBM](https://github.com/onnx/onnxmltools)
* [либсвм](https://github.com/onnx/onnxmltools)
* [XGBoost](https://github.com/onnx/onnxmltools)

## <a name="get-onnx-models"></a>Получение моделей ONNX

Существует несколько способов получить модель в формате ONNX:

- [ONNX Model Zoo](https://github.com/onnx/models): содержит несколько предварительно обученных моделей ONNX для различных типов задач. Вы можете скачать и использовать версии, поддерживаемые Машинное обучение Windows.

- [Собственный экспорт из платформ обучения машинного обучения](https://onnx.ai/supported-tools). несколько платформ обучения поддерживают собственные функции экспорта для ONNX, что позволяет сохранить обученную модель в определенной версии формата ONNX. Например, Chain, Caffee2 и PyTorch. Кроме того, службы, такие как [машинное обучение Azure службы](https://azure.microsoft.com/services/machine-learning-service/) и [Azure пользовательское визуальное распознавание](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) , также предоставляют собственный экспорт ONNX.

  - Сведения о том, как обучать и экспортировать модель ONNX в облаке с помощью Пользовательское визуальное распознавание, см. в разделе [учебник. Использование модели ONNX из пользовательское визуальное распознавание с помощью машинного обучения Windows (Предварительная версия)](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/custom-vision-onnx-windows-ml).

- [Преобразование существующих моделей с помощью винмлтулс](https://docs.microsoft.com/windows/ai/windows-ml/convert-model-winmltools). Этот пакет Python позволяет преобразовывать модели из нескольких форматов инфраструктуры обучения в ONNX. Вы можете указать версию ONNX, в которую требуется преобразовать модель, в зависимости от того, какие сборки Windows предназначены для приложения. Если вы не знакомы с Python, для преобразования моделей можно использовать [панель мониторинга на основе пользовательского интерфейса Windows машинное обучение](https://github.com/Microsoft/Windows-Machine-Learning/tree/master/Tools/WinMLDashboard) .

> [!IMPORTANT]
> Границы базы данных SQL Azure поддерживают не все версии ONNX. В настоящее время поддерживается только прогнозирование числовых типов данных с помощью модели ONNX.

Получив модель ONNX, вы можете развернуть ее в области базы данных SQL Azure. Затем можно использовать [собственную оценку с функцией прогнозирования T-SQL](/sql/advanced-analytics/sql-native-scoring/).

## <a name="limitations"></a>Ограничения

В настоящее время эта поддержка ограничена моделями с **числовыми типами данных**:

- [int и bigint](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql5)
- [Real и float](https://docs.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql).
  
Другие числовые типы могут быть преобразованы в поддерживаемые типы с помощью приведения и преобразования [Cast](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql)и Convert.

Входные данные модели должны быть структурированы таким образом, чтобы каждый вход модели соответствовал одному столбцу в таблице. Например, если для обучения модели используется кадр данных Pandas, то каждый вход должен быть отдельным столбцом в модели.

## <a name="next-steps"></a>Дальнейшие действия

- [Развертывание пограничных баз данных SQL с помощью портал Azure](deploy-portal.md)
- [Развертывание модели ONNX в предварительной версии базы данных SQL Azure](deploy-onnx.md)