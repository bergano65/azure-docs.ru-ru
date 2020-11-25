---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: 6147dff71ff8c93c8c46b5921b37db459ea73e4a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96025628"
---
> [!TIP]
> После входа в систему появится список подписок, связанных с учетной записью Azure. В сведениях о подписке с `isDefault: true` указана текущая активная подписка для команд Azure CLI. Эта подписка должна быть той же, которая содержит рабочую область Машинного обучения Azure. Идентификатор подписки можно найти на [портале Azure](https://portal.azure.com), перейдя на страницу обзора рабочей области. Вы также можете использовать пакет SDK для получения идентификатора подписки из объекта рабочей области. Например, `Workspace.from_config().subscription_id`.
> 
> Чтобы выбрать другую подписку, укажите имя или идентификатор этой подписки с помощью команды `az account set -s <subscription name or ID>`. См. дополнительные сведения о [выборе нужной подписки при использовании нескольких подписок Azure](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest).