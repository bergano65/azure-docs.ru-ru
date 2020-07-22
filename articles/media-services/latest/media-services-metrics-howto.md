---
title: Просмотр метрик с помощью Azure Monitor
description: В этой статье показано, как отслеживать метрики с помощью портал Azureных диаграмм и Azure CLI.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80382942"
---
# <a name="monitor-media-services-metrics"></a>Monitor Media Services metrics (Мониторинг метрик службы мультимедиа)

[Azure Monitor](../../azure-monitor/overview.md) позволяет отслеживать метрики и журналы диагностики, которые помогут понять, как работают приложения. Подробное описание этой функции и сведения о том, почему вы хотите использовать метрики и журналы диагностики служб мультимедиа Azure, см. в статье [мониторинг метрик служб мультимедиа и журналов диагностики](media-services-metrics-diagnostic-logs.md).

Azure Monitor предоставляет несколько способов взаимодействия с метриками, включая их диаграмму на портале, доступ к ним через REST API или запросы с помощью Azure CLI. В этой статье показано, как отслеживать метрики с помощью портал Azureных диаграмм и Azure CLI.

## <a name="prerequisites"></a>Предварительные требования

- [Создание учетной записи Служб мультимедиа](create-account-cli-how-to.md)
- Просмотр [мониторинга метрик и журналов диагностики служб мультимедиа](media-services-metrics-diagnostic-logs.md)

## <a name="view-metrics-in-azure-portal"></a>Просмотр метрик в портал Azure

1. Войдите на портал Azure по адресу https://portal.azure.com.
1. Перейдите к учетной записи служб мультимедиа Azure и выберите **метрики**.
1. Щелкните поле **ресурс** и выберите ресурс, для которого требуется отслеживать метрики.

    Справа появится окно **Выбор ресурса** со списком доступных ресурсов. В этом случае вы увидите:

    * &lt;Имя учетной записи служб мультимедиа&gt;
    * &lt;Имя учетной записи служб мультимедиа имя &gt; / &lt; конечной точки потоковой передачи&gt;
    * &lt;имя учетной записи хранения&gt;

    Выберите ресурс и нажмите кнопку **Применить**. Дополнительные сведения о поддерживаемых ресурсах и метриках см. в статье [мониторинг метрик служб мультимедиа](media-services-metrics-diagnostic-logs.md).

    ![Метрики](media/media-services-metrics/metrics02.png)

    > [!NOTE]
    > Чтобы переключиться между ресурсами, для которых требуется отслеживать метрики, снова щелкните поле **ресурса** и повторите этот шаг.
1. (Необязательно) присвойте диаграмме имя (измените имя, нажав карандаш вверху).
1. Добавьте метрики, которые необходимо просмотреть.

    ![Метрики](media/media-services-metrics/metrics03.png)
1. Вы можете закрепить диаграмму на панели мониторинга.

## <a name="view-metrics-with-azure-cli"></a>Просмотр метрик с помощью Azure CLI

Чтобы получить метрики "исходящего трафика" с Azure CLI, выполните следующую `az monitor metrics` команду:

```azurecli-interactive
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

Чтобы получить другие метрики, замените "исходящий" на имя метрики, которое вас интересует.

## <a name="see-also"></a>См. также раздел

* [Метрики Azure Monitor](../../azure-monitor/platform/data-platform.md)
* [Создание, Просмотр оповещений метрик и управление ими с помощью Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

## <a name="next-steps"></a>Дальнейшие действия

[Журналы диагностики](media-services-diagnostic-logs-howto.md)
