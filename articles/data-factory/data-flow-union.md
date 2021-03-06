---
title: Сопоставление преобразования «объединение потока данных»
description: Преобразование новой ветви потока данных сопоставления фабрики данных Azure
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 02/12/2019
ms.openlocfilehash: adba1eb61676dbebcb356490b14b279ebe69c644
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930151"
---
# <a name="azure-data-factory-mapping-data-flow-union-transformation"></a>Преобразование объединения потока данных в фабрике данных Azure

Операция объединения группирует несколько потоков данных в один, при этом результаты команды SQL Union для таких потоков используются в качестве новых выходных данных для преобразования с объединением. Вся схема из каждого входного потока будет объединена в потоке данных без необходимости использовать ключ объединения.

Можно объединить n-число потоков в таблице параметров, щелкнув значок "+" рядом с каждой настроенной строкой, включая исходные данные, а также потоки из существующих преобразований в потоке данных.

![Преобразование «объединение»](media/data-flow/union.png "Объединение множеств")

В этом случае можно объединить разнородные метаданные из нескольких источников (в этом примере три разных исходных файла) и объединить их в один поток:

![Обзор преобразования «объединение»](media/data-flow/union111.png "Union 1")

Чтобы добиться этого, добавьте дополнительные строки в параметры объединения, включив все добавляемые источники. Для общего уточняющего запроса или ключа подключения не требуется:

![Параметры преобразования «объединение»](media/data-flow/unionsettings.png "Параметры объединения")

Если вы установили преобразование SELECT после объединения, вы сможете переименовать перекрывающиеся поля или поля, которые не были именованы в источниках без заголовков. Щелкните "проверить", чтобы увидеть в этом примере метаданные объединения с 132 столбцами Total из трех различных источников:

![Завершение преобразования Union](media/data-flow/union333.png "Объединение 3")

## <a name="name-and-position"></a>Имя и расположение

При выборе значения "Union by name" каждое значение столбца будет перебрасываться в соответствующий столбец из каждого источника с новой Объединенной схемой метаданных.

При выборе "объединить по положению" каждое значение столбца будет помещено в исходную позицию из каждого соответствующего источника, что приведет к постановке в новый объединенный поток данных, в котором данные из каждого источника добавляются в один и тот же поток:

![Выходные данные объединения](media/data-flow/unionoutput.png "Выходные данные объединения")

## <a name="next-steps"></a>Дальнейшие действия

Изучите аналогичные преобразования, включая [соединение](data-flow-join.md) и [существование](data-flow-exists.md).
