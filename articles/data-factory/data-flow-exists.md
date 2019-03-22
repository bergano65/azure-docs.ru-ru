---
title: Существующее преобразование сопоставления потока данных Фабрики данных Azure
description: Существующее преобразование сопоставления потока данных Фабрики данных Azure
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 18d8a0e231e8b4dbe33911dd6267966674366904
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56734495"
---
# <a name="azure-data-factory-mapping-data-flow-exists-transformation"></a>Существующее преобразование сопоставления потока данных Фабрики данных Azure

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Существующее преобразование является преобразованием фильтрации строк, которое останавливает или пропускает строки через поток данных. Существующее преобразование похоже на ```SQL WHERE EXISTS``` и ```SQL WHERE NOT EXISTS```. После выполнения преобразования фильтра итоговые строки потока данных будут включены во все строки, в которых значения столбцов из источника 1 существуют в источнике 2 или не существуют в источнике 2.

![Exists settings](Существующие параметры)(media/data-flow/exsits.png "exists 1")

Чтобы выполнить сравнение значений для потока данных между 1 и 2 потоками существующего преобразования, необходимо выбрать второй источник.

Из 1 и 2 источника выберите столбец, значения которого необходимо проверить на существование или отсутствие.
