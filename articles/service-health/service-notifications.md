---
title: Просмотр уведомлений о работоспособности служб на портале Azure
description: Уведомления о работоспособности служб позволяют просматривать соответствующие сообщения, публикуемые Microsoft Azure.
author: stephbaron
ms.author: stbaron
services: monitoring
ms.service: service-health
ms.topic: conceptual
ms.date: 6/27/2019
ms.subservice: ''
ms.openlocfilehash: d2e18ae28e79590cb04ee0045341ea817be4a3bc
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538358"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Просмотр уведомлений о работоспособности служб на портале Azure

Уведомления о работоспособности служб публикуются в инфраструктуре Azure в [журнала действий Azure](../azure-monitor/platform/activity-logs-overview.md).  Уведомления содержат сведения о ресурсах в вашей подписке. Учитывая возможно большой объем сведений, хранящихся в журнале действий, имеется отдельный интерфейс, чтобы было проще просматривать и настраивать оповещения для уведомления о работоспособности службы. 

В зависимости от класса, уведомления о работоспособности служб могут носить информационный характер или предполагать какое-либо действие.

Дополнительные сведения о различных классов уведомлений о работоспособности службы, см. в разделе [свойств уведомлений о работоспособности службы](service-health-notifications-properties.md).

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Просмотр уведомлений о работоспособности службы на портале Azure

1. На [портале Azure](https://portal.azure.com) выберите **Монитор**.

    ![Снимок экрана с меню портала Azure и выбранной службой Monitor](./media/service-notifications/home-monitor.png)

    В Azure Monitor отображено единое представление всех параметров мониторинга и данных. Сначала открывается раздел **Журнал действий** .

1. Выберите **Оповещения**.

    ![Снимок экрана журнала мониторинга активности с выбранными оповещениями](./media/service-notifications/service-health-summary.png)

1. Выберите **+Добавить оповещение журнала действий** и настройте оповещение, чтобы в дальнейшем получать оповещения об уведомлениях службы. Дополнительные сведения см. в статье [Создание оповещений журнала действий для уведомлений службы](../azure-monitor/platform/alerts-activity-log-service-notifications.md).

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте больше об [оповещениях журнала действий](../azure-monitor/platform/activity-log-alerts.md).
