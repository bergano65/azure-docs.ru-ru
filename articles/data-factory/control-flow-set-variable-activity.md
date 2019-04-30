---
title: Установка действия переменной в фабрике данных Azure | Документация Майкрософт
description: Узнайте, как использовать установку действия переменной, чтобы задать значение существующей переменной, определенной в конвейере Фабрики данных
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/10/2018
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: 71abfdff629f36b278488851b546c7371353a4d9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60767971"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Установка действия переменной в фабрике данных Azure

Используйте установку действия переменной, чтобы задать значение существующей переменной с типом "Строка", "Bool" или "Массив", определенной в конвейере Фабрики данных.

## <a name="type-properties"></a>Свойства типа

Свойство | ОПИСАНИЕ | Обязательно для заполнения
-------- | ----------- | --------
name | Имя действия в конвейере | Да
description | Текст, описывающий действия | нет
тип | Тип действия — SetVariable | да
value | Строковый литерал или значение объекта выражения, используемое для установки указанной переменной | да
variableName | Имя переменной, которое будет задано этим действием | да


## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о деятельности связанного потока управления, который поддерживается Фабрикой данных: 

- [Добавление действия переменной](control-flow-append-variable-activity.md)
