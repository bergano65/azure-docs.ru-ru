---
title: Обзор соединителя фабрики данных Azure
description: Узнайте о поддерживаемых соединителях в фабрике данных.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/08/2020
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: f22b4bec17abe95be2a17d3e0e3c41fab3483fe0
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2020
ms.locfileid: "86224827"
---
# <a name="azure-data-factory-connector-overview"></a>Обзор соединителя фабрики данных Azure

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Фабрика данных Azure поддерживает следующие хранилища данных и форматы посредством копирования, потока данных, поиска, получения метаданных и удаления действий. Щелкните каждое хранилище данных, чтобы узнать о поддерживаемых возможностях и соответствующих конфигурациях.

## <a name="supported-data-stores"></a>Поддерживаемые хранилища данных

[!INCLUDE [Connector overview](../../includes/data-factory-v2-connector-overview.md)]

## <a name="supported-file-formats"></a>Поддерживаемые типы файлов

Фабрика данных Azure поддерживает следующие форматы файлов. Дополнительные сведения о параметрах на основе форматирования см. в каждой статье.

- [Формат Avro](format-avro.md)
- [Двоичный формат](format-binary.md)
- [Формат Common Data Model](format-common-data-model.md)
- [Формат текста с разделителями](format-delimited-text.md)
- [Разностный формат](format-delta.md)
- [Формат Excel](format-excel.md)
- [Формат JSON](format-json.md)
- [Формат ORC](format-orc.md)
- [Формат Parquet](format-parquet.md)

## <a name="next-steps"></a>Дальнейшие действия

- [Действие копирования](copy-activity-overview.md)
- [Поток данных для сопоставления](concepts-data-flow-overview.md)
- [Действие поиска](control-flow-lookup-activity.md)
- [Действие получения метаданных](control-flow-get-metadata-activity.md)
- [Удалить действие](delete-activity.md)
