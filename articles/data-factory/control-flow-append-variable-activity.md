---
title: Добавление действия переменной в фабрике данных Azure | Документация Майкрософт
description: Узнайте, как использовать добавление действия переменной, чтобы добавить значение существующему массиву переменной, определенной в конвейере Фабрики данных
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: douglasl
ms.openlocfilehash: 03652ce20d82565d5714cdc43a01a9e7c3074f6a
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/10/2018
ms.locfileid: "48903975"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Добавление действия переменной в фабрике данных Azure

Используйте добавление действия переменной, чтобы добавить значение существующему массиву переменной, определенной в конвейере Фабрики данных.

## <a name="type-properties"></a>Свойства типа

Свойство | ОПИСАНИЕ | Обязательно
-------- | ----------- | --------
name | Имя действия в конвейере | Yes
description | Текст, описывающий действия | Нет
Тип | Тип действия — AppendVariable | Да
value | Строковый литерал или значение объекта выражения, используемое для добавление указанной переменной | Да
variableName | Имя переменной будет изменено действием, в то время как переменная должна иметь тип "Массив" | Да

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения о деятельности связанного потока управления, который поддерживается Фабрикой данных: 

- [Установка действия переменной](control-flow-set-variable-activity.md)
