---
title: Активность проверки в фабрике данных Azure | Документация Майкрософт
description: Активность проверки не производит выполнение конвейера, пока не проверяет вложенного набора данных с определенным критериям, которые пользователь указывает.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: shlo
ms.openlocfilehash: 46447bdbea93d1f99c5682cf878c2035e6f49b78
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58523031"
---
# <a name="validation-activity-in-azure-data-factory"></a>Активность проверки в фабрике данных Azure
Проверку можно использовать в конвейере для конвейера только по-прежнему выполнения после проверки вложенного существует ссылка на набор данных, что он соответствует заданным условиям или достигнуто время ожидания.


## <a name="syntax"></a>Синтаксис

```json

{
    "name": "Validation_Activity",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_File",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "minimumSize": 20
    }
},
{
    "name": "Validation_Activity_Folder",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_Folder",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "childItems": true
    }
}

```


## <a name="type-properties"></a>Свойства типа

Свойство | ОПИСАНИЕ | Допустимые значения | Обязательно для заполнения
-------- | ----------- | -------------- | --------
name | Имя действия «Проверка» | Строка | Yes |
Тип | Должно быть присвоено **проверки**. | Строка | Yes |
dataset | Действие будет блокировать выполнение до проверки ссылку на этот набор данных существует и что он отвечает указанным критериям, или достигнуто время ожидания. Набор данных должен поддерживать свойство «MinimumSize» или «ChildItems». | Ссылка на набор данных | Yes |
timeout | Указывает время ожидания для выполнения действия. Если значение не указано, значение по умолчанию — 7 дней («7.00:00:00»). Формат: д.чч:мм:сс | Строка | Нет  |
sleep | Задержка в секундах между попытками проверки. Если значение не указано, значение по умолчанию — 10 секунд. | Целое число  | Нет  |
childItems | Проверяет, если она содержит дочерние элементы. Может быть установлен в значение true: Проверьте, что папка существует и что у него есть элементы. Блокирует работу до хотя бы один элемент находится в папке или при достижении значения времени ожидания.-false: Проверьте, что папка существует и что он пуст. Блокируется до папка является пустым или до времени ожидания будет достигнуто значение. Если значение не задано, действие будет заблокирована, пока папка существует, или не истечет время ожидания. | Логическое | Нет  |
Минимальный размер | Минимальный размер файла в байтах. Если значение не указано, значение по умолчанию — 0 байт | Целое число  | Нет  |


## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь с другими действиями потока управления, которые поддерживаются фабрикой данных:

- [Действие условия If](control-flow-if-condition-activity.md)
- [Действие выполнения конвейера](control-flow-execute-pipeline-activity.md)
- [Действие ForEach](control-flow-for-each-activity.md)
- [Действие получения метаданных](control-flow-get-metadata-activity.md)
- [Действие поиска](control-flow-lookup-activity.md)
- [Веб-действие](control-flow-web-activity.md)
- [Действие Until](control-flow-until-activity.md)
