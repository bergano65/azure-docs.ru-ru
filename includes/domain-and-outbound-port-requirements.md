---
title: включить файл
description: включить файл
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: 6af6795fefb41f1d8f2b56e0aa1fb367fc18cee2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74559298"
---
| Имена доменов                  | Исходящие порты | Описание                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Требуется самохознядляемого времени выполнения интеграции для подключения к службам перемещения данных в Azure Data Factory. |
| `*.frontend.clouddatahub.net` | 443            | Требуется локальной среде выполнения интеграции для подключения к службе фабрики данных. |
| `download.microsoft.com`    | 443            | Требуется локальной среде выполнения интеграции для скачивания обновлений. Если у вас отключено автоматическое обновление, вы можете пропустить настройку этого домена. |
| `*.core.windows.net`          | 443            | Используется локальной средой выполнения интеграции для подключения к учетной записи хранения Azure при помощи функции [промежуточного копирования](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy). |
| `*.database.windows.net`      | 1433           | Требуется только при копировании из или в базу данных Azure S'L или хранилище данных Azure S'L и в противном случае. Используйте функцию постановочной копирования для копирования данных в базу данных S'L или хранилище данных S'L без открытия порта 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Требуется только при копировании из или в Azure Data Lake Store и в противном случае необязательно. |
