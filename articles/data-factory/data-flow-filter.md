---
title: Преобразование фильтра потока данных сопоставления фабрики данных Azure
description: Преобразование фильтра потока данных сопоставления фабрики данных Azure
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 2afe079c346a15ec212664ce022ac5e2926b12d4
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387809"
---
# <a name="azure-data-factory-filter-transformation"></a>Преобразование фильтра фабрики данных Azure



Преобразование фильтра предоставляет фильтрацию строк. Создайте выражение, которое определяет условие фильтра. Нажмите на текстовое поле, чтобы запустить построителя выражений. Создайте выражение фильтра в построителе выражений, чтобы контролировать, какие строки с текущего потока данных можно пропустить (отфильтровать) к следующему преобразованию. Преобразование фильтра следует рассматривать как предложение WHERE инструкции SQL.

## <a name="filter-on-loan_status-column"></a>Фильтр по столбцу loan_status:

```
in([‘Default’, ‘Charged Off’, ‘Fully Paid’], loan_status).
```

Примените фильтр к столбцу года в роликах фильмов.

```
year > 1980
```

## <a name="next-steps"></a>Дальнейшие действия

Попробуйте преобразование «Фильтрация столбцов», [Преобразование «выбор](data-flow-select.md) »
