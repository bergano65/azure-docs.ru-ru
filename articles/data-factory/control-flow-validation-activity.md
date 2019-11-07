---
title: Действие проверки в фабрике данных Azure
description: Действие проверки не будет продолжать выполнение конвейера до тех пор, пока не будет выполнена проверка присоединенного набора данных с определенным критерием, указанным пользователем.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: f63c78c59d7d6be3c66ea0785389eff73e3bff60
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73678368"
---
# <a name="validation-activity-in-azure-data-factory"></a>Действие проверки в фабрике данных Azure
Вы можете использовать проверку в конвейере, чтобы убедиться, что конвейер будет продолжать выполнение только после проверки существования ссылки на присоединенный набор данных, которая соответствует заданным условиям, или достигнуто время ожидания.


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

Свойство | Description (Описание) | Допустимые значения | Обязательно
-------- | ----------- | -------------- | --------
name | Имя действия "Проверка" | string | Да |
type | Необходимо задать значение " **Проверка**". | string | Да |
dataset | Действие блокирует выполнение до тех пор, пока не будет проверена ссылка на этот набор данных и что она соответствует заданным условиям, или истекло время ожидания. Указанный набор данных должен поддерживать свойство "MinimumSize" или "Чилдитемс". | Справочник по наборам данных | Да |
timeout | Указывает время ожидания для выполнения действия. Если значение не указано, по умолчанию используется значение 7 дней ("7.00:00:00"). Формат — d. чч: мм: СС | string | Нет |
Sleep | Задержка в секундах между попытками проверки. Если значение не указано, значение по умолчанию — 10 секунд. | Целое число | Нет |
childItems | Проверяет, есть ли у папки дочерние элементы. Можно задать значение-true: проверить существование папки и наличие в ней элементов. Блокируется до тех пор, пока в папке не появится хотя бы один элемент или не будет достигнуто значение времени ожидания.-false: Убедитесь, что папка существует и пуста. Блокируется до тех пор, пока папка не будет пуста или пока не достигнет значения времени ожидания. Если значение не указано, действие блокируется до тех пор, пока папка не будет существовать или пока не истечет время ожидания. | Логический | Нет |
minimumSize | Минимальный размер файла в байтах. Если значение не указано, значение по умолчанию — 0 байт. | Целое число | Нет |


## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь с другими действиями потока управления, которые поддерживаются фабрикой данных:

- [действие условия If](control-flow-if-condition-activity.md);
- [Действие выполнения конвейера](control-flow-execute-pipeline-activity.md)
- [Действие ForEach](control-flow-for-each-activity.md)
- [Действие получения метаданных](control-flow-get-metadata-activity.md)
- [Действие поиска](control-flow-lookup-activity.md)
- [Веб-действие](control-flow-web-activity.md)
- [Действие Until](control-flow-until-activity.md)
