---
title: Проверка работоспособности кластера обозревателя данных Azure
description: В этой статье описаны действия, позволяющие проверить работоспособность кластера обозревателя данных Azure.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: d07873b34a41ff20b5007a88743f6b150d4d8a3d
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50212834"
---
# <a name="check-the-health-of-an-azure-data-explorer-cluster"></a>Проверка работоспособности кластера обозревателя данных Azure

На работоспособность кластера обозревателя данных Azure влияют несколько факторов, включая ЦП, память и дисковую подсистему. В этой статье показаны некоторые основные шаги, которые можно выполнить для оценки работоспособности кластера.

1. Войдите на портал [https://dataexplorer.azure.com](https://dataexplorer.azure.com).

1. На панели слева выберите свой кластер и выполните следующую команду:

    ```Kusto
    .show diagnostics
    | project IsHealthy
    ```
    Результат 1 означает работоспособность, а результат 0 — неработоспособность.

1. Войдите на [портал Azure](https://portal.azure.com) и перейдите в свой кластер.

1. В разделе **Мониторинг** выберите **Метрики**, а затем **Проверка активности**, как показано ниже. Результат, приближенный к 1, означает, что кластер работоспособен.

    ![Метрика проверки активности кластера](media/check-cluster-health/portal-metrics.png)

1. Можно добавить на диаграмму другие метрики. Выберите диаграмму и нажмите **Добавить метрику**. Выберите другую метрику. В этом примере показана метрика **ЦП**.

    ![Добавление метрики](media/check-cluster-health/add-metric.png)

1. Если вам нужна помощь в диагностике проблем с работоспособностью кластера, создайте запрос в службу поддержки на [портале Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).