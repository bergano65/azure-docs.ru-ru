---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: 428a3ad17c81b465635207de622398e814289d87
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "81616823"
---
> [!TIP]
> После входа в систему появится список подписок, связанных с учетной записью Azure. В сведениях о подписке с `isDefault: true` указана текущая активная подписка для команд Azure CLI. Эта подписка должна быть той же, которая содержит рабочую область Машинного обучения Azure. Идентификатор подписки можно найти на [портале Azure](https://portal.azure.com), перейдя на страницу обзора рабочей области. Вы также можете использовать пакет SDK для получения идентификатора подписки из объекта рабочей области. Например, `Workspace.from_config().subscription_id`.
> 
> Чтобы выбрать другую подписку, укажите имя или идентификатор этой подписки с помощью команды `az account set -s <subscription name or ID>`. См. дополнительные сведения о [выборе нужной подписки при использовании нескольких подписок Azure](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest).