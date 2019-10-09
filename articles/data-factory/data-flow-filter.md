---
title: Преобразование фильтра сопоставления потока данных Фабрики данных Azure
description: Преобразование фильтра сопоставления потока данных Фабрики данных Azure
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: dd29753ff14d16081a46eebbc2ea02d94e5985f1
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029359"
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

## <a name="next-steps"></a>Следующие шаги

Попробуйте преобразование «Фильтрация столбцов», [Преобразование «выбор](data-flow-select.md) »
