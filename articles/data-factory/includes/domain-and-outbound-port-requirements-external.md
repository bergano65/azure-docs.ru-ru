---
title: включить файл
description: включить файл
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: lle
ms.openlocfilehash: 4ef7bc7ed1ab255bffdfec10c07bc040019d9f1e
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121681"
---
| Доменные имена                  | Исходящие порты | Описание                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.core.windows.net`          | 443            | Используется локальной средой выполнения интеграции для подключения к учетной записи хранения Azure при помощи функции промежуточного копирования. |
| `*.database.windows.net`      | 1433           | Требуется только при двунаправленном копировании с участием Базы данных SQL Azure или Azure Synapse Analytics. Используйте функцию промежуточного копирования для копирования данных в Базу данных SQL или Azure Synapse Analytics, не открывая порт 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Требуется только при двунаправленном копировании с участием Azure Data Lake Store. |
