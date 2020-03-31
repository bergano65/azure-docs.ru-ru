---
title: Поля аналитики Azure Stream JobConfig.json
description: В этой статье перечислены поддерживаемые поля для файла Аналитики потоков Azure JobConfig.json, используемого для создания заданий в Visual Studio Code.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 27ed553035ce9d7abf57ffe93078df9c17b8408c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617961"
---
# <a name="azure-stream-analytics-jobconfigjson-fields"></a>Поля аналитики Azure Stream JobConfig.json

Следующие поля поддерживаются в файле *JobConfig.json,* используемом для [создания задания Azure Stream Analytics с использованием Visual Studio Code.](quick-create-vs-code.md)

```json
{
    "DataLocale": "string",
    "OutputErrorPolicy": "string",
    "EventsLateArrivalMaxDelayInSeconds": "integer",
    "EventsOutOfOrderMaxDelayInSeconds": "integer",
    "EventsOutOfOrderPolicy": "string",
    "StreamingUnits": "integer",
    "CompatibilityLevel": "string",
    "UseSystemAssignedIdentity": "boolean",
    "GlobalStorage": {
        "AccountName": "string",
        "AccountKey": "string",
    },
    "DataSourceCredentialDomain": "string",
    "ScriptType": "string",
    "Tags": {}
}
```

|name|Тип|Обязательно|Значение|
|----|----|--------|-----|
|DataLocale|строка|нет|Локаль данных задания аналитики потока. Значение должно быть именем поддерживаемого. По умолчанию в 'en-US', если нет указано.|
|ВыходОшибкаПолитика|строка|нет|Указывает политику, применяемую к событиям, которые приходят на выходе и не могут быть записаны на внешнее хранилище из-за недостающих значений столбца (недостающие значения столбца, значения столбца неправильного типа или размера). - Остановка или падение|
|EventsLateArrivalMaxDelayInSeconds|Целое число|нет|Максимальная допустимая задержка в секундах, когда события, прибывающие с опозданием, могут быть включены. Поддерживаемый диапазон от -1 до 1814399 (20.23:59:59 дней) и -1 используется для указания ожидания на неопределенный срок. Если свойство отсутствует, оно интерпретируется как значение -1.|
|СобытияOutofOrderMaxDelayInSeconds|Целое число|нет|Максимальная допустимая задержка в секундах, когда события вне порядка могут быть скорректированы, чтобы вернуться в порядок.|
|СобытияВнеorderполитика|строка|нет|Отозначает политику, применяемую к событиям, которые приходят не в порядке в потоке событий ввода. - Отрегулируйте или упадите|
|StreamingUnits|Целое число|Да|Определяет количество потоковых единиц, которые использует работа потоковой передачи.|
|CompatibilityLevel|строка|нет|Контролирует определенное поведение выполнения задания потоковой передачи. - Приемлемые значения: "1.0", "1.1", "1.2"|
|UseSystemAssignedIdentityIdentity|Логическое|нет|Установить верно, чтобы позволить этой работе общаться с другими службами Azure, как само по себе, используя управляемый Итенотификатор активного каталога Azure.|
|GlobalStorage.AccountName|строка|нет|Глобальная учетная запись хранилища используется для хранения содержимого, связанного с работой по анализу потоков, например, для снимков справочных данных.|
|GlobalStorage.AccountKey|строка|нет|Соответствующий ключ для глобального счета хранения.|
|DataSourceCredentialDomain|строка|нет|Зарезервированное свойство для локального хранения учетных данных.|
|ScriptType|строка|Да|Зарезервировано свойство для обозначения типа этого исходного файла. Приемлемое значение "JobConfig" для JobConfig.json.|
|Теги|Пары ключей-ценностей JSON|нет|Теги — это пары имен/значений, которые позволяют классифицировать ресурсы и просматривать консолидированный биллинг, применяя один и тот же тег для нескольких ресурсов и групп ресурсов. Имена тегов нечувствительны, а значения тегов чувствительны к случаям.|

## <a name="next-steps"></a>Дальнейшие действия

* [Создание задания Azure Stream Analytics в коде Visual Studio](quick-create-vs-code.md)
* [Тестовая stream Analytics запрашивает локально с выборочными данными с помощью Visual Studio Code](visual-studio-code-local-run.md)
* [Тестовая потоковая аналитика запрашивает локально против ввода потоковой передачи с помощью Visual Studio Code](visual-studio-code-local-run-live-input.md)
*[Развертывание задания Azure Stream Analytics с помощью пакета CI/CD npm](setup-cicd-vs-code.md)