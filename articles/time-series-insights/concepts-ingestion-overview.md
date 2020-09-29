---
title: Общие сведения о приеме-Azure Time Series Insights Gen2 | Документация Майкрософт
description: Сведения о приеме данных в Gen2 службы "аналитика временных рядов Azure".
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/28/2020
ms.custom: seodec18
ms.openlocfilehash: 57c23ba4acdbde1a5dfac39d89a09dfcef6b25a1
ms.sourcegitcommit: a0c4499034c405ebc576e5e9ebd65084176e51e4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91460906"
---
# <a name="azure-time-series-insights-gen2-data-ingestion-overview"></a>Общие сведения о приеме данных в службе "Аналитика временных рядов Azure" 2-го поколения

Среда Gen2 "аналитика временных рядов Azure" содержит *подсистему приема* для накопления, обработки и хранения данных временных рядов. По мере поступления данных в источники событий служба "аналитика временных рядов Azure" Gen2 будет потреблять и хранить данные практически в реальном времени.

[![Общие сведения о приеме данных](media/concepts-ingress-overview/ingress-overview.png)](media/concepts-ingress-overview/ingress-overview.png#lightbox)

## <a name="ingestion-topics"></a>Темы приема

В следующих статьях подробно рассмотрена обработка данных, включая рекомендации по следующим рекомендациям.

* Ознакомьтесь с [источниками событий](./concepts-streaming-ingestion-event-sources.md) и рекомендациями по выбору отметки времени источника события.

* Проверка поддерживаемых [типов данных](./concepts-supported-data-types.md)

* Узнайте, как подсистема приема будет применять набор [правил](./concepts-json-flattening-escaping-rules.md) к свойствам JSON для создания столбцов учетной записи хранения.

* Проверьте [ограничения пропускной способности](./concepts-streaming-ingress-throughput-limits.md) среды, чтобы спланировать требования к масштабированию.

## <a name="next-steps"></a>Дальнейшие действия

* Продолжите работу, чтобы узнать больше о [источниках событий](./concepts-streaming-ingestion-event-sources.md) для среды Gen2 "аналитика временных рядов Azure".
