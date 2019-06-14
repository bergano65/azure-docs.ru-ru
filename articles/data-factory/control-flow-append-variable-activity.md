---
title: Добавление действия переменной в фабрике данных Azure | Документация Майкрософт
description: Узнайте, как использовать добавление действия переменной, чтобы добавить значение существующему массиву переменной, определенной в конвейере Фабрики данных
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/09/2018
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: a5efe946000eb00e65d314ae53d7136761e2109d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60557228"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Добавление действия переменной в фабрике данных Azure

Используйте добавление действия переменной, чтобы добавить значение существующему массиву переменной, определенной в конвейере Фабрики данных.

## <a name="type-properties"></a>Свойства типа

Свойство | ОПИСАНИЕ | Обязательно для заполнения
-------- | ----------- | --------
name | Имя действия в конвейере | Да
description | Текст, описывающий действия | Нет
type | Тип действия — AppendVariable | Да
value | Строковый литерал или значение объекта выражения, используемое для добавление указанной переменной | Да
variableName | Имя переменной будет изменено действием, в то время как переменная должна иметь тип "Массив" | Да

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о деятельности связанного потока управления, который поддерживается Фабрикой данных: 

- [Установка действия переменной](control-flow-set-variable-activity.md)
