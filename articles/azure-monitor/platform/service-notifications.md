---
title: Просмотр уведомлений о работоспособности служб на портале Azure
description: Уведомления о работоспособности служб позволяют просматривать соответствующие сообщения, публикуемые Microsoft Azure.
author: dkamstra
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 4/12/2018
ms.author: dukek
ms.subservice: logs
ms.openlocfilehash: 8cf8c3eb86f55b5595ef9a1af83ea50580bf638b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67069355"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Просмотр уведомлений о работоспособности служб на портале Azure

Уведомления о работоспособности служб публикуются в инфраструктуре Azure. Они содержат сведения о ресурсах в вашей подписке. Эти уведомления являются подклассом действия события журнала и таким образом можно также найти в журнале действий. В зависимости от класса, уведомления о работоспособности служб могут носить информационный характер или предполагать какое-либо действие.

Дополнительные сведения о различных классов уведомлений о работоспособности службы, см. в разделе [свойств уведомлений о работоспособности службы](../../service-health/service-health-notifications-properties.md).

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Просмотр уведомлений о работоспособности службы на портале Azure

1. На [портале Azure](https://portal.azure.com) выберите **Монитор**.

    ![Снимок экрана с меню портала Azure и выбранной службой Monitor](./media/service-notifications/home-monitor.png)

    В Azure Monitor отображено единое представление всех параметров мониторинга и данных. Сначала открывается раздел **Журнал действий** .

1. Выберите **Оповещения**.

    ![Снимок экрана журнала мониторинга активности с выбранными оповещениями](./media/service-notifications/service-health-summary.png)

1. Выберите **+Добавить оповещение журнала действий** и настройте оповещение, чтобы в дальнейшем получать оповещения об уведомлениях службы. Дополнительные сведения см. в статье [Создание оповещений журнала действий для уведомлений службы](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

## <a name="next-steps"></a>Дальнейшие действия

* Получайте [оповещения о публикации уведомлений о работоспособности службы](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).  
* Узнайте больше об [оповещениях журнала действий](../../azure-monitor/platform/activity-log-alerts.md).
