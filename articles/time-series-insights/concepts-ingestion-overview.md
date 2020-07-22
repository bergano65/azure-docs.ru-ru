---
title: Обзор приема-служба "аналитика временных рядов Azure" | Документация Майкрософт
description: Сведения о приеме данных в службе "аналитика временных рядов Azure".
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/03/2020
ms.custom: seodec18
ms.openlocfilehash: ecee6817b58639d1b60264c818ab9517e5d133e0
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86050113"
---
# <a name="azure-time-series-insights-data-ingestion-overview"></a>Обзор приема данных службы "аналитика временных рядов Azure"

Среда службы "аналитика временных рядов Azure" содержит *подсистему приема* для накопления, обработки и хранения данных временных рядов. По мере поступления данных в источники событий служба "аналитика временных рядов Azure" будет потреблять и хранить данные практически в реальном времени.

[![Обзор приема](media/concepts-ingress-overview/ingress-overview.png)](media/concepts-ingress-overview/ingress-overview.png#lightbox)

## <a name="ingestion-topics"></a>Темы приема

В следующих статьях подробно рассмотрена обработка данных, включая рекомендации по следующим рекомендациям.

* Ознакомьтесь с [источниками событий](concepts-streaming-ingestion-event-sources.md) и рекомендациями по выбору отметки времени источника события.

* Проверка поддерживаемых [типов данных](concepts-supported-data-types.md)

* Узнайте, как подсистема приема будет применять набор [правил](./concepts-json-flattening-escaping-rules.md) к свойствам JSON для создания столбцов учетной записи хранения.

* Проверьте [ограничения пропускной способности](concepts-streaming-throughput-limitations.md) среды, чтобы спланировать требования к масштабированию.

## <a name="next-steps"></a>Дальнейшие шаги

* Продолжайте работу, чтобы узнать больше о [источниках событий](concepts-streaming-ingestion-event-sources.md) для среды службы "аналитика временных рядов Azure". 
