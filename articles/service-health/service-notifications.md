---
title: Просмотр уведомлений о работоспособности служб на портале Azure
description: Уведомления о работоспособности служб позволяют просматривать соответствующие сообщения, публикуемые Microsoft Azure.
ms.topic: conceptual
ms.date: 6/27/2019
ms.openlocfilehash: 21416edc95d345eb183030a9b0cccb7529305d6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75748650"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Просмотр уведомлений о работоспособности служб на портале Azure

Уведомления о работоспособности службы публикуются инфраструктурой Azure в [журнале активности Azure.](../azure-monitor/platform/platform-logs-overview.md)  Уведомления содержат информацию о ресурсах по подписке. Учитывая, что, возможно, большой объем информации, хранящийся в журнале действий, существует отдельный пользовательский интерфейс, который упрощает просмотр и настройку оповещений в уведомлениях о работоспособности службы. 

В зависимости от класса, уведомления о работоспособности служб могут носить информационный характер или предполагать какое-либо действие.

Для получения дополнительной информации о различных [Service health notifications properties](service-health-notifications-properties.md)классах уведомлений о работоспособности службы см.

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Просмотр уведомлений о работоспособности службы на портале Azure

1. На [портале Azure](https://portal.azure.com) выберите **Монитор**.

    ![Снимок экрана с меню портала Azure и выбранной службой Monitor](./media/service-notifications/home-monitor.png)

    В Azure Monitor отображено единое представление всех параметров мониторинга и данных. Сначала открывается раздел **Журнал действий** .

1. Выберите **оповещения**.

    ![Снимок экрана журнала мониторинга активности с выбранными оповещениями](./media/service-notifications/service-health-summary.png)

1. Выберите **+Добавить оповещение журнала действий** и настройте оповещение, чтобы в дальнейшем получать оповещения об уведомлениях службы. Дополнительные сведения см. в статье [Создание оповещений журнала действий для уведомлений службы](../azure-monitor/platform/alerts-activity-log-service-notifications.md).

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте больше об [оповещениях журнала действий](../azure-monitor/platform/activity-log-alerts.md).
