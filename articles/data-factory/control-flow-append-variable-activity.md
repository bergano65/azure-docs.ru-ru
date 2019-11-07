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
ms.openlocfilehash: bcecd3411b6e6e751032f33862a671d8adbca046
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73679972"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Добавление действия переменной в фабрике данных Azure

Используйте добавление действия переменной, чтобы добавить значение существующему массиву переменной, определенной в конвейере Фабрики данных.

## <a name="type-properties"></a>Свойства типа

Свойство | Description (Описание) | Обязательно
-------- | ----------- | --------
name | Имя действия в конвейере | Да
description | Текст, описывающий действия | Нет
type | Тип действия — AppendVariable | Да
значение | Строковый литерал или значение объекта выражения, используемое для добавление указанной переменной | Да
variableName | Имя переменной будет изменено действием, в то время как переменная должна иметь тип "Массив" | Да

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о деятельности связанного потока управления, который поддерживается Фабрикой данных: 

- [Установка действия переменной](control-flow-set-variable-activity.md)
