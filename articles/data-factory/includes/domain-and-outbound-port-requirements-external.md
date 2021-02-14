---
title: включить файл
description: включить файл
author: lrtoyou1223
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: lle
ms.openlocfilehash: d24e8957dc63024a46495e8a66bad7dbb3790f38
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100389516"
---
| Доменные имена                  | Исходящие порты | Описание                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.core.windows.net`          | 443            | Используется локальной средой выполнения интеграции для подключения к учетной записи хранения Azure при помощи функции промежуточного копирования. |
| `*.database.windows.net`      | 1433           | Требуется только при двунаправленном копировании с участием Базы данных SQL Azure или Azure Synapse Analytics. Используйте функцию промежуточного копирования для копирования данных в Базу данных SQL или Azure Synapse Analytics, не открывая порт 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Требуется только при двунаправленном копировании с участием Azure Data Lake Store. |
