---
title: Прием демонстрационных данных в обозреватель данных Azure
description: Узнайте, как принять (загрузить) демонстрационные данные о погоде в обозреватель данных Azure.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 08/12/2019
ms.openlocfilehash: c803de599f6be98512b15e927c6d15f1c7d95ff1
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515743"
---
# <a name="quickstart-ingest-sample-data-into-azure-data-explorer"></a>Краткое руководство. Прием демонстрационных данных в обозреватель данных Azure

В этой статье показано, как принять (загрузить) демонстрационные данные в базу данных обозревателя данных Azure. Существует [несколько способов приема данных](ingest-data-overview.md). В этой статье описан базовый метод, который подходит для тестирования.

> [!NOTE]
> У вас должны быть эти данные, если вы выполнили инструкции из статьи о [приеме данных с помощью библиотеки Python в Azure Data Explorer](python-ingest-data.md).

## <a name="prerequisites"></a>Предварительные требования

[Тестовый кластер и база данных](create-cluster-database-portal.md)

## <a name="ingest-data"></a>Прием данных

Набор демонстрационных данных **StormEvents** содержит данные о погоде из [Национальных центров Соединенных Штатов по экологической информации](https://www.ncdc.noaa.gov/stormevents/).

1. Войдите на портал [https://dataexplorer.azure.com](https://dataexplorer.azure.com).

1. В левом верхнем углу приложения выберите **Добавить кластер**.

1. В диалоговом окне **Добавить кластер** введите URL-адрес кластера в форму `https://<ClusterName>.<Region>.kusto.windows.net/`, а затем выберите **Добавить**.

1. Вставьте следующую команду и выберите **Выполнить**.

    ```Kusto
    .create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)

    .ingest into table StormEvents h'https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (ignoreFirstRecord=true)
    ```

1. После завершения приема вставьте следующий запрос, выберите запрос в окне и нажмите **Выполнить**.

    ```Kusto
    StormEvents
    | sort by StartTime desc
    | take 10
    ```
    Запрос возвращает следующие результаты из принятых демонстрационных данных.

    ![Результаты запроса](media/ingest-sample-data/query-results.png)

## <a name="next-steps"></a>Дополнительная информация

* Дополнительные сведения о методах приема данных см. в [этой статье](ingest-data-overview.md).
* [Краткое руководство по запросу данных в пользовательском веб-интерфейсе Azure Data Explorer](web-query-data.md).
* [Написание запросов](write-queries.md) с помощью языка запросов Kusto.
