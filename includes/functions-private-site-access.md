---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 2d66e7f497f85141de172c59b67676e1bb93955e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021008"
---
[Частная конечная точка Azure](../articles/private-link/private-endpoint-overview.md) — это сетевой интерфейс, который обеспечивает безопасное подключение к службе с помощью Приватного канала Azure.  Частная конечная точка использует частный IP-адрес из виртуальной сети, эффективно предоставляя доступ к службе из виртуальной сети.

Вы можете использовать закрытую конечную точку для функций, размещенных в планах [службы приложений](../articles/azure-functions/functions-scale.md#app-service-plan) уровня " [премиум](../articles/azure-functions/functions-premium-plan.md) " и "Услуга".

При создании входящего подключения к частной конечной точке для функций потребуется также запись DNS для разрешения частного адреса.  По умолчанию частная запись DNS будет создана при создании частной конечной точки с помощью портал Azure.

Дополнительные сведения см. в статье [использование частных конечных точек для веб-приложений](../articles/app-service/networking/private-endpoint.md).