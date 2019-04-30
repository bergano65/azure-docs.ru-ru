---
title: Мониторинг устройства Azure Data Box Edge | Документация Майкрософт
description: Описывается, как использовать портал Azure и локальный пользовательский веб-интерфейс для наблюдения за Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 188f5c6cfbb4650ad1ff767955d064f8e0c3cb70
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60756754"
---
# <a name="monitor-your-azure-data-box-edge"></a>Мониторинг Azure Data Box Edge

В этой статье описывается, как выполнять мониторинг Azure Data Box Edge. Для мониторинга устройства можно использовать портал Azure или локальный пользовательский веб-интерфейс. Используйте портал Azure для просмотра событий устройства и метрик, а также настройки метрик и управления ими. Используйте локальный пользовательский веб-интерфейс на физическом устройстве для просмотра состояния оборудования различных компонентов устройства.

В этой статье раскрываются следующие темы:

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

    ![Просмотр состояния оборудования](media/data-box-edge-monitor/view-hardware-status.png)

## <a name="view-metrics"></a>Показать метрики

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-metrics.md)]

## <a name="manage-alerts"></a>Управление оповещениями

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-manage-alerts.md)]

## <a name="next-steps"></a>Дальнейшие действия 

Узнайте об [управлении пропускной способностью](data-box-edge-manage-bandwidth-schedules.md).