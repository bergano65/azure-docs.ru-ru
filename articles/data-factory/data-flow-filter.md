---
title: Преобразование фильтра сопоставления потока данных Фабрики данных Azure
description: Преобразование фильтра сопоставления потока данных Фабрики данных Azure
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: e0b41850c149ff7095333cf77b780dec1f03b882
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66234418"
---
# <a name="azure-data-factory-filter-transformation"></a>Преобразование фильтра фабрики данных Azure

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Преобразование фильтра предоставляет фильтрацию строк. Создайте выражение, которое определяет условие фильтра. Нажмите на текстовое поле, чтобы запустить построителя выражений. Создайте выражение фильтра в построителе выражений, чтобы контролировать, какие строки с текущего потока данных можно пропустить (отфильтровать) к следующему преобразованию. Например, преобразование фильтра в предложении WHERE инструкции SQL.

## <a name="filter-on-loanstatus-column"></a>Отфильтруйте столбец loan_status:

```
in([‘Default’, ‘Charged Off’, ‘Fully Paid’], loan_status).
```

Примените фильтр к столбцу года в роликах фильмов.

```
year > 1980
```

## <a name="next-steps"></a>Дальнейшие действия

Столбец фильтрации преобразования, попробуйте [выберите преобразование](data-flow-select.md)
