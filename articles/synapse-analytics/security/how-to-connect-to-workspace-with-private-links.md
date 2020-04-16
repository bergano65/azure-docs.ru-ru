---
title: Подключение к рабочему пространству Azure Synapse с помощью частных ссылок
description: Эта статья научит вас, как подключиться к вашему рабочему пространству Azure Synapse с помощью частных ссылок
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 5a00fc44021278a8b910cf454b43b0bae2c3a1f9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81432194"
---
# <a name="connect-to-your-azure-synapse-workspace-using-private-links-preview"></a>Подключение к рабочему пространству Azure Synapse с помощью частных ссылок (предварительный просмотр)

Эта статья научит вас, как создать частную конечную точку для рабочего пространства Azure Synapse. Смотрите [частные ссылки и частные конечные точки,](https://docs.microsoft.com/azure/private-link/) чтобы узнать больше.

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>Шаг 1: Откройте рабочее пространство Azure Synapse на портале Azure

Выберите **приватное соединение конечных точек** **в соответствии** с безопасностью, а затем выберите **частную конечную точку.**
![Откройте рабочее пространство Azure Synapse на портале Azure](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-1.png)

## <a name="step-2-select-your-subscription-and-region-details"></a>Шаг 2: Выберите информацию о подписке и регионе

Под вкладкой **«Основы»** в окне **«Создайте частное окно конечных точек»** выберите **группу подписки** и **ресурсов.** Назовите **частную** конечную точку, которую вы хотите создать. Выберите **Регион,** где требуется создать частную конечную точку.

Частные конечные точки создаются в подсети. Подписка, группа ресурсов и выбранные регионы фильтруют частные конечные сети. Выберите **далее: >ресурсов,** когда это сделано.
![Выберите информацию о подписке и регионе](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-2.png)

## <a name="step-3-select-your-azure-synapse-workspace-details"></a>Шаг 3: Выберите детали рабочего пространства Azure Synapse

Выберите **«Подключение к ресурсу Azure» в моем каталоге** в вкладке **Ресурс.** Выберите **подписку,** содержащую рабочее пространство Azure Synapse. **Тип ресурса** для создания частных конечных точек в рабочее пространство Azure Synapse — *это Microsoft.Synapse/workspaces.*

Выберите рабочее пространство Azure Synapse в качестве **ресурса.** Каждое рабочее пространство Azure Synapse имеет три **целевых субресурса,** которые можно создать в частной точке конца: Sql, SqlOnDemand и Dev.

Выберите **далее: Конфигурация>,** чтобы перейти к следующей части установки.
![Выберите информацию о подписке и регионе](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-3.png)

Во вкладке **Configuration** выберите **Виртуальную сеть** и **подсеть,** в которой должна быть создана частная конечная точка. Также необходимо создать запись DNS, которая отображает сядок в частную конечную точку.

Выберите **«Да»** для **интеграции с частной зоной DNS** для интеграции вашей частной конечной точки с частной зоной DNS. Если у вас нет частной зоны DNS, связанной с Вашим VNet, создается новая частная зона DNS. Выберите **Обзор и создайте,** когда сделано.

![Выберите информацию о подписке и регионе](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-4.png)

После завершения развертывания откройте рабочее пространство Azure Synapse на портале Azure и выберите **приватные соединения конечных точек.** Отображаются новые частные конечные точки и имя соединения частной, связанные с частной конечной точкой.

![Выберите информацию о подписке и регионе](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-5.png)

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше об [управляемом рабочем пространстве VNet](./synapse-workspace-managed-vnet.md)

Подробнее о [управляемых частных конечных точках](./synapse-workspace-managed-private-endpoints.md)

[Создание управляемых частных конечных точек для источников данных](./how-to-create-managed-private-endpoints.md)
