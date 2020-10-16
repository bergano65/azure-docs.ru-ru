---
title: Создание функций даты определения пользовательского интерфейса
description: Описывает функции, используемые при работе со значениями даты.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 80484fd15e51bae7036c43bd3ca8fe8167387ede
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87098192"
---
# <a name="createuidefinition-date-functions"></a>Функции даты CreateUiDefinition

Функции, используемые при работе со значениями даты.

## <a name="addhours"></a>addhours

Добавляет к указанной отметке времени целое количество часов.

В следующем примере возвращается `"1991-01-01T00:59:59.000Z"`.

```json
"[addHours('1990-12-31T23:59:59Z', 1)]"
```

## <a name="addminutes"></a>addminutes

Добавляет к указанной отметке времени целое количество минут.

В следующем примере возвращается `"1991-01-01T00:00:59.000Z"`.

```json
"[addMinutes('1990-12-31T23:59:59Z', 1)]"
```

## <a name="addseconds"></a>addseconds
Добавляет к указанной отметке времени целое количество секунд.

В следующем примере возвращается `"1991-01-01T00:00:00.000Z"`.

```json
"[addSeconds('1990-12-31T23:59:60Z', 1)]"
```

## <a name="utcnow"></a>utcnow

Возвращает строку текущей даты и времени на локальном компьютере в формате ISO 8601.

В следующем примере может вернуться `"1990-12-31T23:59:59.000Z"`.

```json
"[utcNow()]"
```

## <a name="next-steps"></a>Дальнейшие действия

* Общие сведения об Azure Resource Manager см. в [этой статье](../management/overview.md).
