---
title: Соединитель управления ИТ-услугами в Azure Monitor
description: В этой статье описывается, как подключить продукты и службы ITSM с помощью соединителя управления ИТ-службами (ITSM) в Azure Monitor, чтобы централизованно отслеживать рабочие элементы ITSM и управлять ими.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/12/2020
ms.openlocfilehash: 7ac842df17e80b49990d89d1623330c4e31ab566
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100624015"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>Подключение продуктов и служб ITSM с помощью соединителя управления ИТ-службами
В этой статье описывается, как настроить в Log Analytics связь между продуктами или службами ITSM и соединителем управления ИТ-службами (ITSM), чтобы централизованно управлять рабочими элементами ITSM. Дополнительные сведения об ITSMC см. в [этом обзоре](../platform/itsmc-overview.md).

Поддерживаются следующие службы и продукты ITSM. Выберите продукт, чтобы просмотреть подробные сведения о том, как его подключить к ITSMC.

- [ServiceNow](../platform/itsmc-connections-servicenow.md)
- [System Center Service Manager](../platform/itsmc-connections-scsm.md);
- [Cherwell](../platform/itsmc-connections-cherwell.md)
- [Provance](../platform/itsmc-connections-provance.md);

> [!NOTE]
> Мы предлагаем нашим клиентам Cherwell и Provance использовать [действие веб-перехватчика](../platform/action-groups.md#webhook) для Cherwell и Provance конечной точки в качестве другого решения интеграции.

## <a name="next-steps"></a>Дальнейшие шаги

* [Устранение неполадок с соединителем ITSM](../platform/itsmc-resync-servicenow.md)