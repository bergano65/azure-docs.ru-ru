---
title: Выполнить действие потока данных в фабрике данных Azure | Документация Майкрософт
description: Действие execute потока данных выполняет потоки данных.
services: data-factory
documentationcenter: ''
author: kromerm
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: makromer
ms.openlocfilehash: 856f4bd9c2b04ff10ed598c5e641955e1de99398
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792418"
---
# <a name="execute-data-flow-activity-in-azure-data-factory"></a>Выполнить действие потока данных в фабрике данных Azure
Действие потока данных execute используется для выполнения потока данных ADF в конвейер debug (песочница) и активируется конвейер.

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

## <a name="syntax"></a>Синтаксис

```json
{
    "name": "MyDataFlowActivity",
    "type": "ExecuteDataFlow",
    "typeProperties": {
      "dataflow": {
         "referenceName": "dataflow1",
         "type": "DataFlowReference"
      },
        "compute": {
          "computeType": "General",
          "coreCount": 8,
      }
}

```

## <a name="type-properties"></a>Свойства типа

* ```dataflow``` имя сущности потока данных, который требуется выполнить
* ```compute``` Описывает среду выполнения Spark
* ```coreCount``` — количество ядер, чтобы назначить выполнение этого действия часть потока данных

![Выполнение потока данных](media/data-flow/activity-data-flow.png "выполнения потока данных")

### <a name="run-on"></a>Запуск на

Выберите вычислительную среду для этого выполнения потока данных. По умолчанию используется среда выполнения интеграции Azure по умолчанию автоматическое разрешение. Этот выбор будет выполняться поток данных в среде Spark в одном регионе с фабрикой данных. Тип вычисления будет кластера заданий, это означает, что вычислительная среда может занять несколько минут для запуска.

### <a name="debugging-pipelines-with-data-flows"></a>Отладка конвейеры потоков данных

![Отладка кнопку](media/data-flow/debugbutton.png "кнопку отладки")

Используйте Отладка потока данных для использования warmed кластер для тестирования потоки данных в интерактивном режиме в запуска отладки конвейера. Параметр отладки Pipleine проверка последовательностей в конвейере данных.

### <a name="compute-type"></a>Тип вычисления

Вы можете общего назначения, вычисления оптимизирован или оптимизированных для памяти, в зависимости от требований вашего потока данных.

### <a name="core-count"></a>Число ядер

Выберите количество ядер, которые нужно назначить заданию. Для небольших заданий будет лучше работать меньшего числа ядер.

### <a name="staging-area"></a>Промежуточная область

Если прием данных в хранилище данных Azure, вы должны выбрать расположение промежуточного хранения для вашей пакетной загрузки Polybase.

## <a name="parameterized-datasets"></a>Параметризованные наборы данных

Если вы используете параметризованные наборы данных, не забудьте задать значения параметров.

![Выполнение параметры потока данных](media/data-flow/params.png "параметров")

### <a name="debugging-parameterized-data-flows"></a>Отладка параметризованные потоки данных

Можно отладить только те потоки данных с параметризованные наборы данных из конвейера Debug запустить с помощью execute действие потока данных. В настоящее время сеансов интерактивной отладки в поток данных ADF не работают с параметризованные наборы данных. Выполнений конвейера и выполнения отладки будут работать с параметрами.

## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь с другими действиями потока управления, которые поддерживаются фабрикой данных: 

- [Действие условия If](control-flow-if-condition-activity.md)
- [Действие выполнения конвейера](control-flow-execute-pipeline-activity.md)
- [Действие ForEach](control-flow-for-each-activity.md)
- [Действие получения метаданных](control-flow-get-metadata-activity.md)
- [Действие поиска](control-flow-lookup-activity.md)
- [Веб-действие](control-flow-web-activity.md)
- [Действие Until](control-flow-until-activity.md)
