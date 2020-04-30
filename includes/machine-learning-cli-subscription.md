---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: 428a3ad17c81b465635207de622398e814289d87
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81616823"
---
> [!TIP]
> После входа в систему появится список подписок, связанных с учетной записью Azure. Сведения о подписке `isDefault: true` с активированной в настоящее время подпиской для Azure CLI команд. Эта подписка должна быть той же, которая содержит рабочую область Машинное обучение Azure. Идентификатор подписки можно найти в [портал Azure](https://portal.azure.com) , перейдя на страницу обзора для своей рабочей области. Вы также можете использовать пакет SDK для получения идентификатора подписки из объекта рабочей области. Например, `Workspace.from_config().subscription_id`.
> 
> Чтобы выбрать другую подписку, используйте `az account set -s <subscription name or ID>` команду и укажите имя или идентификатор подписки, к которой нужно перейти. Дополнительные сведения о выборе подписки см. в статье [Использование нескольких подписок Azure](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest).