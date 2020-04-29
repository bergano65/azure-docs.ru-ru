---
title: Добавление действия переменной в фабрике данных Azure
description: Узнайте, как использовать добавление действия переменной, чтобы добавить значение существующему массиву переменной, определенной в конвейере Фабрики данных
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.date: 10/09/2018
ms.openlocfilehash: 5d74cd0fcd524f00d79eb3fbab386c602a413766
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81414214"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Добавление действия переменной в фабрике данных Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
Используйте добавление действия переменной, чтобы добавить значение существующему массиву переменной, определенной в конвейере Фабрики данных.

## <a name="type-properties"></a>Свойства типа

Свойство | Описание | Обязательный
-------- | ----------- | --------
name | Имя действия в конвейере | Да
description | Текст, описывающий действия | нет
type | Тип действия — AppendVariable | да
значение | Строковый литерал или значение объекта выражения, используемое для добавление указанной переменной | да
variableName | Имя переменной будет изменено действием, в то время как переменная должна иметь тип "Массив" | да

## <a name="next-steps"></a>Дальнейшие шаги
Дополнительные сведения о деятельности связанного потока управления, который поддерживается Фабрикой данных: 

- [Действие задания переменной](control-flow-set-variable-activity.md)
