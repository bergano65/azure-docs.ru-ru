---
title: Просмотр метрик с помощью Azure Monitor
description: В этой статье показано, как отслеживать метрики с помощью портал Azureных диаграмм и Azure CLI.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: ab89c222648a66ad7451f9bb47e254c55b925630
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100590759"
---
# <a name="monitor-media-services-metrics"></a>Monitor Media Services metrics (Мониторинг метрик службы мультимедиа)

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

[Azure Monitor](../../azure-monitor/overview.md) позволяет отслеживать метрики и журналы диагностики, которые помогут понять, как работают приложения. Подробное описание этой функции и сведения о том, почему следует использовать метрики и журналы диагностики служб мультимедиа Azure, см. в статье [мониторинг метрик служб мультимедиа и журналов диагностики](media-services-metrics-diagnostic-logs.md).

Azure Monitor предоставляет несколько способов взаимодействия с метриками, включая их диаграмму на портале, доступ к ним через REST API или запросы с помощью Azure CLI. В этой статье показано, как отслеживать метрики с помощью портал Azureных диаграмм и Azure CLI.

## <a name="prerequisites"></a>Предварительные требования

- [Создание учетной записи Служб мультимедиа](./create-account-howto.md)
- Просмотр  [мониторинга метрик и журналов диагностики служб мультимедиа](media-services-metrics-diagnostic-logs.md)

## <a name="view-metrics-in-azure-portal"></a>Просмотр метрик в портал Azure

1. Войдите на портал Azure по адресу https://portal.azure.com.
1. Перейдите к учетной записи служб мультимедиа Azure и выберите **метрики**.
1. Щелкните поле **область** и выберите ресурс, который требуется отслеживать.

    Справа появится окно **Выбор области** со списком доступных ресурсов. В этом случае вы увидите:

    * &lt;Имя учетной записи служб мультимедиа&gt;
    * &lt;Имя учетной записи служб мультимедиа имя &gt; / &lt; конечной точки потоковой передачи&gt;
    * &lt;имя учетной записи хранения&gt;

    Фильтр выберите ресурс и нажмите кнопку **Применить**. Дополнительные сведения о поддерживаемых ресурсах и метриках см. в статье [мониторинг метрик служб мультимедиа](media-services-metrics-diagnostic-logs.md).

    > [!NOTE]
    > Чтобы переключиться между ресурсами, которые необходимо отслеживать, снова щелкните поле **источник** и повторите этот шаг.

1. Необязательно. присвойте диаграмме имя (измените имя, нажав карандаш вверху).
1. Добавьте метрики, которые необходимо просмотреть.
1. Вы можете закрепить диаграмму на панели мониторинга.

## <a name="view-metrics-with-azure-cli"></a>Просмотр метрик с помощью Azure CLI

Чтобы получить метрики "исходящего трафика" с Azure CLI, выполните следующую `az monitor metrics` команду:

```azurecli-interactive
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

Чтобы получить другие метрики, замените "исходящий" на имя метрики, которое вас интересует.

## <a name="see-also"></a>См. также

- [Метрики Azure Monitor](../../azure-monitor/data-platform.md)
- [Создание, Просмотр оповещений метрик и управление ими с помощью Azure Monitor](../../azure-monitor/alerts/alerts-metric.md).

## <a name="next-steps"></a>Дальнейшие шаги

[Журналы диагностики](media-services-diagnostic-logs-howto.md)
