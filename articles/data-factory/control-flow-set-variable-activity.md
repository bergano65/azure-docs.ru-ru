---
title: Установка действия переменной в фабрике данных Azure | Документация Майкрософт
description: Узнайте, как использовать установку действия переменной, чтобы задать значение существующей переменной, определенной в конвейере Фабрики данных
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
ms.date: 10/10/2018
ms.author: douglasl
ms.openlocfilehash: ff9bfce1f9262d78ba17abdd88c481d5057d5f38
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/10/2018
ms.locfileid: "49076425"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Установка действия переменной в фабрике данных Azure

Используйте установку действия переменной, чтобы задать значение существующей переменной с типом "Строка", "Bool" или "Массив", определенной в конвейере Фабрики данных.

## <a name="type-properties"></a>Свойства типа

Свойство | ОПИСАНИЕ | Обязательно
-------- | ----------- | --------
name | Имя действия в конвейере | Yes
description | Текст, описывающий действия | Нет
Тип | Тип действия — SetVariable | Да
value | Строковый литерал или значение объекта выражения, используемое для установки указанной переменной | Да
variableName | Имя переменной, которое будет задано этим действием | Да


## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения о деятельности связанного потока управления, который поддерживается Фабрикой данных: 

- [Добавление действия переменной](control-flow-append-variable-activity.md)
