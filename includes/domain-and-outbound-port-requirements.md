---
title: включение файла
description: включение файла
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: f634ebb4d8c3f443b5d843b0093c955b384a9f5f
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72285616"
---
| Имена доменов                  | Исходящие порты | Описание                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Требуется локальной среде выполнения интеграции для подключения к службам перемещения данных в фабрике данных. |
| `*.frontend.clouddatahub.net` | 443            | Требуется локальной среде выполнения интеграции для подключения к службе фабрики данных. |
| `download.microsoft.com`    | 443            | Требуется локальной среде выполнения интеграции для скачивания обновлений. Если вы отключили автоматическое обновление, можете пропустить этот шаг. |
| `*.core.windows.net`          | 443            | Используется локальной средой выполнения интеграции для подключения к учетной записи хранения Azure при помощи функции [промежуточного копирования](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy). |
| `*.database.windows.net`      | 1433           | (Необязательно.) Требуется, если выполняется копирование из базы данных SQL Azure или хранилища данных SQL Azure или в них. Используйте функцию промежуточного копирования, чтобы копировать данные в базу данных SQL Azure или хранилище данных SQL Azure без необходимости открывать порт 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | (Необязательно.) Требуется, если выполняется копирование из репозитория Azure Data Lake Store или в него. |
