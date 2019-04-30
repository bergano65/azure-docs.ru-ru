---
title: Преобразование фильтра сопоставления потока данных Фабрики данных Azure
description: Преобразование фильтра сопоставления потока данных Фабрики данных Azure
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: d1751c47ad4507260d9f8d6ea44fcb32ed0e7338
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61347435"
---
# <a name="azure-data-factoryfilter-transformation"></a>Преобразование FactoryFilter данных Azure

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
