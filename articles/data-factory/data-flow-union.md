---
title: Преобразование новой ветви сопоставления потока данных Фабрики данных Azure
description: Преобразование новой ветви сопоставления потока данных Фабрики данных Azure
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: c7c6904d4cbfc6513f37c40803dbd79875954825
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271548"
---
# <a name="azure-data-factory-mapping-data-flow-union-transformation"></a>Преобразование с объединением сопоставлений потоков данных Фабрики данных Azure

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Операция объединения группирует несколько потоков данных в один, при этом результаты команды SQL Union для таких потоков используются в качестве новых выходных данных для преобразования с объединением.

![Преобразование с объединением](media/data-flow/union.png "Объединение")

