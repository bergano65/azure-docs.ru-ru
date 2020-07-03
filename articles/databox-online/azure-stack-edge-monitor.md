---
title: Мониторинг Azure Stack пограничных устройств | Документация Майкрософт
description: Описывает, как использовать портал Azure и локальный веб-интерфейс для мониторинга Azure Stackного периметра.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 2fc7435988a07968e65aaf265c33878c6e72e85b
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82569437"
---
# <a name="monitor-your-azure-stack-edge"></a>Мониторинг Azure Stack пограничных устройств

В этой статье описывается, как отслеживать пограничные Azure Stack. Для мониторинга устройства можно использовать портал Azure или локальный пользовательский веб-интерфейс. Используйте портал Azure для просмотра событий устройства и метрик, а также настройки метрик и управления ими. Используйте локальный пользовательский веб-интерфейс на физическом устройстве для просмотра состояния оборудования различных компонентов устройства.

Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Просмотр событий устройства и соответствующих оповещений.
> * Просмотр состояния оборудования компонентов устройства.
> * Просмотр метрик емкости и транзакций устройства.
> * Настройка оповещений и управление ими.

## <a name="view-device-events"></a>Просмотр событий устройства.

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-device-events.md)]

## <a name="view-hardware-status"></a>Просмотр состояния оборудования

Выполните следующие действия в локальном пользовательском веб-интерфейсе, чтобы просмотреть состояние оборудования компонентов устройства.

1. Подключитесь к локальному пользовательскому веб-интерфейсу устройства.
2. Выберите **Maintenance > Hardware status** (Обслуживание > Состояние оборудования). Можно просмотреть состояние работоспособности различных компонентов устройства. 

    ![Просмотр состояния оборудования](media/azure-stack-edge-monitor/view-hardware-status.png)

## <a name="view-metrics"></a>Просмотр метрик

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-metrics.md)]

## <a name="manage-alerts"></a>Управление оповещениями

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-manage-alerts.md)]

## <a name="next-steps"></a>Дальнейшие действия 

Узнайте об [управлении пропускной способностью](azure-stack-edge-manage-bandwidth-schedules.md).