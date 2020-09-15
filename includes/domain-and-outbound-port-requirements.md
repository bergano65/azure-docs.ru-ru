---
title: включить файл
description: включить файл
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: 0b2831321c9f4d855a19605c1ce5ace9fa11c88b
ms.sourcegitcommit: 1b320bc7863707a07e98644fbaed9faa0108da97
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/09/2020
ms.locfileid: "89596126"
---
| Доменные имена                  | Исходящие порты | Описание                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Требуется локальной среде выполнения интеграции для подключения к службам перемещения данных в Фабрике данных Azure. |
| `{datafactory}.{region}.datafactory.azure.net`<br> или `*.frontend.clouddatahub.net` | 443            | Требуется локальной среде выполнения интеграции для подключения к службе фабрики данных. <br>Для нового экземпляра Фабрики данных найдите полное доменное имя из ключа локальной среды IR в формате {datafactory}.{region}.datafactory.azure.net. Для старого экземпляра Фабрики данных, если полное доменное имя не отображается в ключе локальной среды IR, используйте *.frontend.clouddatahub.net. |
| `download.microsoft.com`    | 443            | Требуется локальной среде выполнения интеграции для скачивания обновлений. Если автоматическое обновление отключено, можно пропустить настройку этого домена. |
| `*.core.windows.net`          | 443            | Используется локальной средой выполнения интеграции для подключения к учетной записи хранения Azure при помощи функции [промежуточного копирования](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy). |
| `*.database.windows.net`      | 1433           | Требуется только при двунаправленном копировании с участием Базы данных SQL Azure или Azure Synapse Analytics. Используйте функцию промежуточного копирования для копирования данных в Базу данных SQL или Synapse Analytics, не открывая порт 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Требуется только при двунаправленном копировании с участием Azure Data Lake Store. |
