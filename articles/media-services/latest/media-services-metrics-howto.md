---
title: Просмотр метрик с помощью Azure Monitor
description: В этой статье показано, как выполнять мониторинг метрик с помощью диаграмм Azure портала и Azure CLI.
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
ms.openlocfilehash: d331dc4eb0c6668426e1ab1a01a1dd1dcebe0c85
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795822"
---
# <a name="monitor-media-services-metrics"></a>Мониторинг метрик службы мультимедиа 

[Azure Monitor](../../azure-monitor/overview.md) включает мониторинг метрик и журналов диагностики, которые помогут вам понять, как выполняемых приложений. Подробное описание этого компонента и почему нужно использовать службы мультимедиа Azure метрики и журналы диагностики, см. в разделе [служб мультимедиа монитор метрики и журналы диагностики](media-services-metrics-diagnostic-logs.md).

Azure Monitor предоставляет несколько способов взаимодействия с метриками, включая создание диаграмм метрик на портале, доступ к метрикам через REST API или запрос метрик с помощью интерфейса командной строки. В этой статье показано, как выполнять мониторинг метрик с помощью диаграмм Azure портала и Azure CLI.

## <a name="prerequisites"></a>предварительные требования

- [Создание учетной записи Служб мультимедиа](create-account-cli-how-to.md)
- Просмотрите [служб мультимедиа монитор метрик и журналов диагностики](media-services-metrics-diagnostic-logs.md)

## <a name="view-metrics-in-azure-portal"></a>Просмотр метрик на портале Azure

1. Войдите на портал Azure по адресу https://portal.azure.com.
1. Перейдите к учетной записи служб мультимедиа Azure и выберите **метрики**.
1. Нажмите кнопку **РЕСУРСОВ** поле и выберите ресурс, для которого вы хотите отслеживать метрики. 

    **Выберите ресурс** появится окно справа со списком доступных ресурсов, чтобы вы. В этом случае вы видите 

    * &lt;Имя учетной записи служб мультимедиа&gt;
    * &lt;Имя учетной записи служб мультимедиа&gt;/&lt;потоковой передачи имя конечной точки&gt;
    * &lt;Имя учетной записи хранения&gt;

    Выберите ресурс и нажмите клавишу **применить**. Дополнительные сведения о поддерживаемых ресурсов и метрики, см. в разделе [метрики служб мультимедиа монитор](media-services-metrics-diagnostic-logs.md).
 
    ![metrics](media/media-services-metrics/metrics02.png)
    
    > [!NOTE]
    > Переключение между ресурсами, для которых вы хотите отслеживать метрики, щелкните **РЕСУРСОВ** поле еще раз и повторите этот шаг.
1. (Необязательно) имя диаграммы (Изменение имени, нажав клавишу значком карандаша в верхней).
1. Добавьте метрики, которые вы хотите просмотреть.

    ![metrics](media/media-services-metrics/metrics03.png)
1. Вы можете закрепить диаграмму на панели мониторинга.

## <a name="view-metrics-with-azure-cli"></a>Просмотр метрик с помощью Azure CLI

Чтобы получить метрики «Исходящий» с помощью интерфейса командной строки, необходимо выполнить следующие `az monitor metrics` команду интерфейса командной строки:

```cli
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

Чтобы получить другие метрики, замените «Исходящие» по имени метрики, которые вас интересуют.

## <a name="see-also"></a>См. также

* [Метрики Azure Monitor](../../azure-monitor/platform/data-platform.md)
* [Создание, просмотр и управление оповещения метрик с помощью Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

## <a name="next-steps"></a>Следующие шаги

[Журналы диагностики](media-services-diagnostic-logs-howto.md)
