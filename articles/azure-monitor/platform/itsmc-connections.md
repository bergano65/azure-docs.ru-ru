---
title: Соединитель управления ИТ-услугами в Azure Monitor
description: В этой статье описывается, как подключить продукты и службы ITSM с помощью соединителя управления ИТ-службами (ITSM) в Azure Monitor, чтобы централизованно отслеживать рабочие элементы ITSM и управлять ими.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/12/2020
ms.openlocfilehash: a372cdcd05267f3bdb093f676948a79c473ad955
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/29/2020
ms.locfileid: "97804026"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>Подключение продуктов и служб ITSM с помощью соединителя управления ИТ-службами
В этой статье описывается, как настроить в Log Analytics связь между продуктами или службами ITSM и соединителем управления ИТ-службами (ITSM), чтобы централизованно управлять рабочими элементами ITSM. Дополнительные сведения об ITSMC см. в [этом обзоре](./itsmc-overview.md).

Поддерживаются следующие службы и продукты ITSM. Выберите продукт, чтобы просмотреть подробные сведения о том, как его подключить к ITSMC.

- [ServiceNow](./itsmc-connections-servicenow.md)
- [System Center Service Manager](./itsmc-connections-scsm.md);
- [Cherwell](./itsmc-connections-cherwell.md)
- [Provance](./itsmc-connections-provance.md);

> [!NOTE]
> Мы предлагаем нашим клиентам Cherwell и Provance использовать [действие веб-перехватчика](./action-groups.md#webhook) для Cherwell и Provance конечной точки в качестве другого решения интеграции.

## <a name="next-steps"></a>Дальнейшие действия

* [Устранение неполадок с соединителем ITSM](./itsmc-resync-servicenow.md)