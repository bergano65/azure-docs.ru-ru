---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: 428a3ad17c81b465635207de622398e814289d87
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81616823"
---
> [!TIP]
> После входа в систему появится список подписок, связанных с учетной записью Azure. Информация о `isDefault: true` подписке — это активированная в настоящее время подписка для команд Azure CLI. Эта подписка должна быть той же самой, которая содержит рабочее пространство Azure Machine Learning. Идентификатор подписки можно найти на [портале Azure,](https://portal.azure.com) посетив страницу обзора рабочего пространства. Вы также можете использовать SDK для получения идентификатора подписки от объекта рабочего пространства. Например, `Workspace.from_config().subscription_id`.
> 
> Чтобы выбрать другую `az account set -s <subscription name or ID>` подписку, используйте команду и укажите имя подписки или идентификатор для переключения. Для получения дополнительной информации о выборе [подписки см.](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)