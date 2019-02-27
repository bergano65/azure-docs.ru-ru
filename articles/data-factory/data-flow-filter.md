---
title: Преобразование фильтра сопоставления потока данных Фабрики данных Azure
description: Преобразование фильтра сопоставления потока данных Фабрики данных Azure
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: b7e7b123560aae3a2d3086c8536969297d31f7ba
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271409"
---
# <a name="azure-data-factory-mapping-data-flow-filter-transformation"></a>Преобразование фильтра сопоставления потока данных Фабрики данных Azure

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Преобразование фильтра предоставляет фильтрацию строк. Создайте выражение, которое определяет условие фильтра. Нажмите на текстовое поле, чтобы запустить построителя выражений. Создайте выражение фильтра в построителе выражений, чтобы контролировать, какие строки с текущего потока данных можно пропустить (отфильтровать) к следующему преобразованию.

Например, примените фильтр к столбцу loan_status.

```
in([‘Default’, ‘Charged Off’, ‘Fully Paid’], loan_status).
```

Примените фильтр к столбцу года в роликах фильмов.

```
year > 1980
```
