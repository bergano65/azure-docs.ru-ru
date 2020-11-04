---
title: Оценка моделей машинного обучения с помощью ПРОГНОЗа
description: Узнайте, как оценить модели машинного обучения с помощью функции PREDICT T-SQL в синапсе SQL.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 07/21/2020
ms.author: anjangsh
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: a8caf6cd5072b4c098adff57194784491c92bb0a
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93325378"
---
# <a name="score-machine-learning-models-with-predict"></a>Оценка моделей машинного обучения с помощью ПРОГНОЗа

Синапсе SQL предоставляет возможность оценки моделей машинного обучения с помощью привычного языка T-SQL. С помощью [прогноза](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest)T-SQL можно перенести имеющиеся модели машинного обучения, обученные историческими данными, и оценить их в пределах защищенных границ хранилища данных. Функция PREDICT принимает в качестве входных данных модель [ONNX (открытый обмен нейронными сетями)](https://onnx.ai/) и данные. Эта функция исключает шаг перемещения ценных данных за пределы хранилища данных для оценки. Он предназначен для того, чтобы предоставить специалистам по обработке данных возможность легко развертывать модели машинного обучения с помощью знакомого интерфейса T-SQL, а также легко сотрудничать с помощью специалистов по обработке и анализу данных, работающих с правильной платформой для выполнения своих задач.

> [!NOTE]
> В настоящее время эта функция не поддерживается в пуле SQL без сервера.

Эта функция требует, чтобы модель обучена за пределами синапсе SQL. После создания модели загрузите ее в хранилище данных и оцените с помощью синтаксиса прогноза T-SQL, чтобы получить ценные сведения из данных.

![предиктовервиев](./media/sql-data-warehouse-predict/datawarehouse-overview.png)

## <a name="training-the-model"></a>Обучение модели

Синапсе SQL ждет предварительно обученную модель. При обучении модели машинного обучения, используемой для выполнения прогнозов в синапсе SQL, учитывайте следующие факторы.

- Синапсе SQL поддерживает только модели формата ONNX. ONNX — это формат модели с открытым исходным кодом, который позволяет обмениваться моделями между различными платформами для обеспечения взаимодействия. Можно преобразовать существующие модели в формат ONNX с помощью платформ, которые либо поддерживаются изначально, либо преобразуют доступные пакеты. Например, пакет [sklearn-onnx](https://github.com/onnx/sklearn-onnx) Convert scikit-учиться Models to onnx. В [репозитории ONNX GitHub](https://github.com/onnx/tutorials#converting-to-onnx-format) содержится список поддерживаемых платформ и примеры.

   Если для обучения используется [автоматизированное машинное](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml) обучение, убедитесь, что для параметра *enable_onnx_compatible_models* задано значение true, чтобы создать модель формата onnx. В [автоматизированной машинное обучение записной книжке](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) показан пример использования аутомл для создания модели машинного обучения в формате ONNX.

- Для входных данных поддерживаются следующие типы данных:
    - int, bigint, Real, float
    - char, varchar, nvarchar

- Данные оценки должны иметь тот же формат, что и обучающие данные. Сложные типы данных, такие как многомерные массивы, не поддерживаются в PREDICT. Таким образом, для обучения убедитесь, что каждый вход модели соответствует одному столбцу таблицы оценки вместо передачи одного массива, содержащего все входные данные.

- Убедитесь, что имена и типы данных для входных значений модели соответствуют именам столбцов и типам данных новых прогнозирующих данных. Визуализация модели ONNX с помощью различных доступных в Интернете средств с открытым кодом может помочь в отладке.

## <a name="loading-the-model"></a>Загрузка модели

Модель хранится в пользовательской таблице синапсе SQL в виде шестнадцатеричной строки. Дополнительные столбцы, такие как ID и Description, могут быть добавлены в таблицу модели для идентификации модели. Используйте тип varbinary (max) в качестве типа данных столбца модели. Ниже приведен пример кода для таблицы, которую можно использовать для хранения моделей:

```sql
-- Sample table schema for storing a model and related data
CREATE TABLE [dbo].[Models]
(
    [Id] [int] IDENTITY(1,1) NOT NULL,
    [Model] [varbinary](max) NULL,
    [Description] [varchar](200) NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN,
    HEAP
)
GO

```

После преобразования модели в шестнадцатеричную строку и указанную определение таблицы используйте [команду Copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) или polybase для загрузки модели в таблицу SQL синапсе. В следующем примере кода для загрузки модели используется команда Copy.

```sql
-- Copy command to load hexadecimal string of the model from Azure Data Lake storage location
COPY INTO [Models] (Model)
FROM '<enter your storage location>'
WITH (
    FILE_TYPE = 'CSV',
    CREDENTIAL=(IDENTITY= 'Shared Access Signature', SECRET='<enter your storage key here>')
)
```

## <a name="scoring-the-model"></a>Оценка модели

После загрузки модели и данных в хранилище данных используйте функцию **Predict T-SQL** для оценки модели. Убедитесь, что новые входные данные имеют тот же формат, что и обучающие данные, используемые для создания модели. Прогноз T-SQL выполняет два входа: модель и новые входные данные оценки, а также создает новые столбцы для выходных данных. Модель может быть задана в виде переменной, литерала или скалярного sub_query. Используйте [WITH common_table_expression](https://docs.microsoft.com/sql/t-sql/queries/with-common-table-expression-transact-sql?view=sql-server-ver15) , чтобы указать именованный результирующий набор для параметра данных.

В примере ниже показан пример запроса с использованием функции прогнозирования. Создается дополнительный столбец с *показателем* имени и типом данных *float* , содержащий результаты прогноза. Все столбцы входных данных и столбцы прогнозирования вывода доступны для вывода с помощью инструкции SELECT. Дополнительные сведения см. в разделе [Predict (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest).

```sql
-- Query for ML predictions
SELECT d.*, p.Score
FROM PREDICT(MODEL = (SELECT Model FROM Models WHERE Id = 1),
DATA = dbo.mytable AS d) WITH (Score float) AS p;
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о функции PREDICT см. в разделе [Predict (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest).
