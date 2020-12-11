---
title: Сравнение с Фабрикой данных Azure
description: Узнайте, как возможности интеграции данных Azure синапсе Analytics отличаются от функций фабрики данных Azure.
services: synapse-analytics
author: kromerm
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 12/10/2020
ms.author: makromer
ms.reviewer: jrasnick
ms.openlocfilehash: a8fd0ef006b246e30c02cfb321c72b4e070f54de
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109153"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Интеграция данных в Azure синапсе Analytics и фабрика данных Azure

В Azure синапсе Analytics возможности интеграции данных, такие как конвейеры синапсе и потоки данных, основаны на этих возможностях фабрики данных Azure. Дополнительные сведения см. в статье [что такое фабрика данных Azure](../../data-factory/introduction.md).


## <a name="available-features-in-azure-data-factory-and-azure-synapse-analytics"></a>Доступные функции в фабрике данных Azure и Azure синапсе Analytics

Ниже приведена таблица со сведениями о доступности функций.

| Категория                 | Компонент    |  Фабрика данных Azure  | Azure Synapse Analytics |
| ------------------------ | ---------- | :------------------: | :---------------------: |
| **Integration Runtime**  | Использование служб SSIS и служб SSIS Integration Runtime | ✓ | ✗ |
|                          | Поддержка Integration Runtime в разных регионах (потоки данных) | ✓ | ✗ |
|                          | Integration Runtime общий доступ | ✓<br><small>*Можно совместно использовать в разных фабриках данных* | ✗ |
|                          | Срок жизни | ✓ | ✗ |
| **Действия конвейеров** | Действие пакета служб SSIS | ✓ | ✗ |
|                          | Поддержка действий Power Query | ✓ | ✓ |
| **Коллекция шаблонов и центр знаний** | Шаблоны решений | ✓<br><small>*Коллекция шаблонов фабрики данных Azure* | ✓<br><small>*Центр знаний рабочей области синапсе* |
| **Интеграция репозитория GIT** | Интеграция с GIT | ✓ | ✓ |
| **Мониторинг**           | Мониторинг заданий Spark для потока данных | ✗ | ✓<br><small>*Использование пулов синапсе Spark* |
|                          | Интеграция с Azure Monitor | ✓ | ✗ |

> [!Note]
> **Срок жизни** — это параметр Azure Integration Runtime, который позволяет кластеру Spark *оставаться тепло* в течение определенного периода времени после выполнения потока данных.
>


## <a name="next-steps"></a>Дальнейшие действия

Приступите к работе с интеграцией данных в рабочей области синапсе, изучите прием [данных в учетную запись gen2 Azure Data Lake Storage](data-integration-data-lake.md).
