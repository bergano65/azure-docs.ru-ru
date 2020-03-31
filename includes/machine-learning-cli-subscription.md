---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: df4735699f71256598bfd418a505ae8d3e5b816c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80296885"
---
После входа в систему появится список подписок, связанных с учетной записью Azure. Информация о `isDefault: true` подписке — это активированная в настоящее время подписка для команд Azure CLI. Эта подписка должна быть той же самой, которая содержит рабочее пространство Azure Machine Learning. Идентификатор подписки можно найти на [портале Azure,](https://portal.azure.com) посетив страницу обзора рабочего пространства. Вы также можете использовать SDK для получения идентификатора подписки от объекта рабочего пространства. Например, `Workspace.from_config().subscription_id`.
    
Чтобы выбрать другую подписку, укажите идентификатор подписки с помощью команды [az account set](https://docs.microsoft.com/cli/azure/account#az-account-set). Для получения дополнительной информации о выборе [подписки см.](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)