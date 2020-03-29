---
title: Формат пакетного теста TSV - NA Maker
titleSuffix: Azure Cognitive Services
description: Понимание формата TSV для пакетного тестирования
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 10/24/2019
ms.author: diberry
ms.openlocfilehash: dccf034b6497651db70b6cc09fff3f1b1d645a1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73507828"
---
# <a name="batch-testing-tsv-format"></a>Формат пакетного тестирования TSV

Пакетное тестирование доступно из [исходного кода](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting) или в виде [загружаемых исполнителей на молнии.](https://aka.ms/qna_btzip) Формат команды для выполнения пакетного теста:

```console
batchtesting.exe input.tsv https://YOUR-HOST.azurewebsites.net ENDPOINT-KEY out.tsv
```

|Параметр|Ожидаемое значение|
|--|--|
|1|имя файла tsv, отформатированного с [помощью вхотого поля TSV](#tsv-input-fields)|
|2|URI для конечной точки, с ВАШИМ-HOST со страницы Публикации портала NA Maker.|
|3|ENDPOINT-KEY, найти на странице Публикации портала NA Maker.|
|4|имя файла tsv, созданного пакетным тестом для получения результатов.|

Используйте следующую информацию для понимания и реализации формата TSV для пакетного тестирования. 

## <a name="tsv-input-fields"></a>Вхотовые поля TSV

|Поля входного файла с разделением знаками табуляции|Примечания|
|--|--|
|KBID|Ваш идентификатор KB найден на странице Публикации.|
|Вопрос|Вопрос, который вводит пользователь.|
|Теги метаданных|необязательный|
|Наилучший вариант|необязательный| 
|Идентификатор ожидаемого ответа|необязательный|

![Формат ввода для файла TSV для пакетного тестирования.](media/batch-test/input-tsv-format-batch-test.png)

## <a name="tsv-output-fields"></a>Поля выхода TSV 

|Параметры файла TSV Выходного файла|Примечания|
|--|--|
|KBID|Ваш идентификатор KB найден на странице Публикации.|
|Вопрос|Вопрос, введенный из входиного файла.|
|Ответ|Верхний ответ из вашей базы знаний.|
|Идентификатор ответа|Идентификатор ответа|
|Оценка|Оценка прогноза для ответа. |
|Теги метаданных|связанный с ответом|
|Идентификатор ожидаемого ответа|факультативно (только тогда, когда дается идентификатор ожидаемого ответа)|
|Судебный ярлык|необязательные значения могут быть: правильными или неправильными (только тогда, когда будет дан ожидаемый ответ)|
