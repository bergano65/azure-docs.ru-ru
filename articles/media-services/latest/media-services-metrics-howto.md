---
title: Просмотр метрик с помощью Azure Monitor
description: В этой статье показано, как отслеживать метрики с помощью диаграмм портала Azure и Azure CLI.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: c230e1e950bb924631032940642a6202acf4ade8
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382942"
---
# <a name="monitor-media-services-metrics"></a>Monitor Media Services metrics (Мониторинг метрик службы мультимедиа)

[Azure Monitor](../../azure-monitor/overview.md) позволяет отслеживать метрики и диагностические журналы, которые помогают понять, как работают приложения. Подробное описание этой функции и подробные данные об этой функции и о [Monitor Media Services metrics and diagnostic logs](media-services-metrics-diagnostic-logs.md)том, почему вы хотите использовать метрики и журналы диагностики Media Services Azure Media, см.

Azure Monitor предоставляет несколько способов взаимодействия с метриками, включая их составление диаграмм на портале, доступ к ним через REST API или запрос с помощью Azure CLI. В этой статье показано, как отслеживать метрики с помощью диаграмм портала Azure и Azure CLI.

## <a name="prerequisites"></a>Предварительные требования

- [Создание учетной записи медиа-услуг](create-account-cli-how-to.md)
- Обзор [монитора медиа-услуг метрики и диагностические журналы](media-services-metrics-diagnostic-logs.md)

## <a name="view-metrics-in-azure-portal"></a>Просмотр метрик на портале Azure

1. Войдите на портал Azure по адресу https://portal.azure.com.
1. Перейдите к учетной записи Медиа-службы Azure и выберите **метрики.**
1. Нажмите на поле **RESOURCE** и выберите ресурс, для которого вы хотите отслеживать метрики.

    **Окно ресурса Select** отображается справа со списком доступных для вас ресурсов. В этом случае вы видите:

    * &lt;Имя учетной записи медиа-услуг&gt;
    * &lt;&gt;/Имя&lt;потоковой передачи учетной записи media Services&gt;
    * &lt;имя учетной записи хранения&gt;

    Выберите ресурс и нажмите **Применить**. Подробную информацию об поддерживаемых ресурсах и метриках можно узнать на [примере мониторинга медиаслужбы.](media-services-metrics-diagnostic-logs.md)

    ![Метрики](media/media-services-metrics/metrics02.png)

    > [!NOTE]
    > Чтобы переключаться между ресурсами, для которых требуется отслеживать метрики, нажмите на поле **RESOURCE** снова и повторите этот шаг.
1. (По желанию) дайте диаграмме имя (отсчеуте имя, нажав карандаш в верхней части).
1. Добавьте метрики, которые вы хотите просмотреть.

    ![Метрики](media/media-services-metrics/metrics03.png)
1. Можно прикрепить диаграмму к панели мониторинга.

## <a name="view-metrics-with-azure-cli"></a>Просмотр метрик с помощью Azure CLI

Чтобы получить метрики "Egress" с помощью `az monitor metrics` Azure CLI, вы запустите следующую команду:

```azurecli-interactive
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

Чтобы получить другие метрики, замените "Egress" на имя метрики, которое вас интересует.

## <a name="see-also"></a>См. также

* [Метрика azure Monitor](../../azure-monitor/platform/data-platform.md)
* [Создавайте, просматривайте и управляйте метрическими оповещениями с помощью Azure Monitor.](../../azure-monitor/platform/alerts-metric.md)

## <a name="next-steps"></a>Дальнейшие действия

[Диагностические журналы](media-services-diagnostic-logs-howto.md)
