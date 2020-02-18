---
title: Шаблон. Параметры в определении политики
description: Этот шаблон Политики Azure предоставляет пример использования параметров в определении политики.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 4921bb216ef67b561bc8986cf48239e6448beafc
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170182"
---
# <a name="azure-policy-pattern-parameters"></a>Шаблон Политики Azure: параметры

Определение политики можно сделать динамическим, чтобы уменьшить количество определений политик, необходимых для использования [параметров](../concepts/definition-structure.md#parameters). Параметр определяется во время назначения политики. Параметры имеют набор предварительно определенных свойств, описывающих параметр и его использование.

## <a name="sample-1-string-parameters"></a>Пример 1 Параметры строки

Это определение политики использует два параметра: **tagName** и **tagValue**, чтобы задать, что именно будет искать назначение политики на ресурсах. Этот формат позволяет использовать политику для любого количества сочетаний имен тегов и значений тегов, но поддерживает только одно определение политики.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json":::

### <a name="sample-1-explanation"></a>Пример 1 Объяснение

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json" range="8-13":::

В этой части определения политики параметр **tagName** определен как _строковый_ и для его использования предоставляется описание.

Затем параметр используется в блоке **policyRule.if**, чтобы сделать политику динамической. Здесь оно используется для определения вычисляемого поля, которое представляет собой тег со значением **tagName**.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json" range="22-27" highlight="3":::

## <a name="sample-2-array-parameters"></a>Пример 2 Параметры массива

Это определение политики использует один параметр, **listOfBandwidthinMbps**, чтобы проверить, настроено ли для ресурса контура Express Route параметр пропускной способности на одно из утвержденных значений. Если оно не совпадает, то создание или обновление ресурса [отклоняется](../concepts/effects.md#deny)

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json":::

### <a name="sample-2-explanation"></a>Пример 2 Объяснение

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json" range="6-12":::

В этой части определения политики параметр **listOfBandwidthinMbps** определяется как _массив_ и для его использования предоставляется описание. В качестве _массива_ он имеет несколько значений для сопоставления.

Затем параметр используется в блоке **policyRule.if**. В качестве параметра _массива_ следует использовать 
[условия](../concepts/definition-structure.md#conditions) _массива_ **in** или **notIn**.
Здесь он используется для псевдонима **serviceProvider.bandwidthInMbps** как одно из определенных значений.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json" range="21-24" highlight="3":::

## <a name="next-steps"></a>Дальнейшие действия

- Ознакомьтесь с другими [шаблонами и встроенными определениями](./index.md).
- Изучите статью о [структуре определения Политики Azure](../concepts/definition-structure.md).
- Изучите [сведения о действии политик](../concepts/effects.md).