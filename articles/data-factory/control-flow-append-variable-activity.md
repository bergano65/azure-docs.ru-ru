---
title: Добавление действия переменной в фабрике данных Azure | Документация Майкрософт
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
ms.openlocfilehash: 24808c9ed4bda5e8d3b0ce23ab93bc59eb260374
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141728"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Добавление действия переменной в фабрике данных Azure

Используйте добавление действия переменной, чтобы добавить значение существующему массиву переменной, определенной в конвейере Фабрики данных.

## <a name="type-properties"></a>Свойства типа

Свойство | Описание | Обязательное значение
-------- | ----------- | --------
name | Имя действия в конвейере | Да
description | Текст, описывающий действия | нет
type | Тип действия — AppendVariable | да
value | Строковый литерал или значение объекта выражения, используемое для добавление указанной переменной | да
variableName | Имя переменной будет изменено действием, в то время как переменная должна иметь тип "Массив" | да

## <a name="next-steps"></a>Следующие шаги
Дополнительные сведения о деятельности связанного потока управления, который поддерживается Фабрикой данных: 

- [Установка действия переменной](control-flow-set-variable-activity.md)
