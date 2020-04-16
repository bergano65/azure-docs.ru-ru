---
title: Создайте управляемую частную конечную точку для подключения к результатам исхода данных.
description: Эта статья научит вас, как создать управляемую частную конечную точку для ваших источников данных из рабочего пространства Azure Synapse.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 363dc4d469d912c14f5f89ef6ff433a2243587e8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428905"
---
# <a name="create-a-managed-private-endpoint-to-your-data-source-preview"></a>Создание управляемой частной конечной точки для исходного кода данных (предварительный просмотр)

Эта статья научит вас, как создать управляемую частную конечную точку для вашего источника данных в Azure. Смотрите [Управляемые частные конечные точки,](./synapse-workspace-managed-private-endpoints.md) чтобы узнать больше.

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>Шаг 1: Откройте рабочее пространство Azure Synapse на портале Azure

Можно создать управляемую частную конечную точку для исходного кода данных из студии Azure Synapse. Выберите вкладку **«Обзор»** на портале Azure и выберите **студию запуска Synapse.**
![Запуск студии Azure Synapse](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-1.png)

## <a name="step-2-navigate-to-the-managed-virtual-networks-tab-in-synapse-studio"></a>Шаг 2: Перейдите к управляемой вкладке виртуальных сетей в студии Synapse

В студии Azure Synapse выберите вкладку **«Управление»** из левой навигации. Выберите **Управляемые виртуальные сети,** а затем выберите **новые**.
![Создание новой управляемой частной конечной точки](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-2.png)

## <a name="step-3-select-the-data-source-type"></a>Шаг 3: Выберите тип источника данных

Выберите тип источника данных. В этом случае источником целевых данных является учетная запись ADLS gen2. Выберите **Continue** (Продолжить).
![Выберите тип источника исхода целевых данных](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-3.png)

## <a name="step-4-enter-information-about-the-data-source"></a>Шаг 4: Введите информацию об источнике данных

В следующем окне введите информацию об источнике данных. В этом примере мы создаем управляемую частную конечную точку для учетной записи ADLS gen2. Введите **имя** для управляемой частной конечной точки. Предоставьте **подписку Azure** и **имя учетной записи хранилища.** Нажмите кнопку **создания**.
![Введите данные источника целевых данных](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-4.png)

## <a name="step-5-verify-that-your-managed-private-endpoint-was-successfully-created"></a>Шаг 5: Убедитесь, что ваша управляемая частная конечная точка была успешно создана

После отправки запроса вы увидите его статус. Чтобы проверить успешное создание управляемой частной конечной точки была создана, проверьте ее *состояние обеспечения.* Возможно, вам придется подождать 1 минуту и выбрать **обновление** состояния подготовки. Вы можете видеть, что управляемая частная конечная точка для учетной записи ADLS gen2 была успешно создана.

Вы также можете видеть, что *состояние утверждения* находится *в ожидании*. Владелец целевого ресурса может одобрить или отклонить частный запрос на подключение конечной точки. Если владелец одобряет частный запрос на подключение конечных точек, то устанавливается частная ссылка. Если отказано, то частная связь не установлена.
![Управляемый статус запроса создания частной конечных точек](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-5.png)

## <a name="next-steps"></a>Дальнейшие действия

Подробнее о [управляемых частных конечных точках](./synapse-workspace-managed-private-endpoints.md)