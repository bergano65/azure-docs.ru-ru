---
title: включение файла
description: включение файла
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: 6af6795fefb41f1d8f2b56e0aa1fb367fc18cee2
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/27/2019
ms.locfileid: "74559298"
---
| Имена доменов                  | Исходящие порты | Описание                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Требуется для локальной среды выполнения интеграции для подключения к службам перемещения данных в фабрике данных Azure. |
| `*.frontend.clouddatahub.net` | 443            | Требуется локальной среде выполнения интеграции для подключения к службе фабрики данных. |
| `download.microsoft.com`    | 443            | Требуется локальной среде выполнения интеграции для скачивания обновлений. Если автоматическое обновление отключено, можно пропустить настройку этого домена. |
| `*.core.windows.net`          | 443            | Используется локальной средой выполнения интеграции для подключения к учетной записи хранения Azure при помощи функции [промежуточного копирования](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy). |
| `*.database.windows.net`      | 1433           | Требуется только при копировании из базы данных SQL Azure или в хранилище данных SQL Azure, а также в необязательное значение. Используйте функцию промежуточного копирования, чтобы скопировать данные в базу данных SQL или хранилище данных SQL, не открывая порт 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Требуется только при копировании из или в Azure Data Lake Store и необязательно в противном случае. |
