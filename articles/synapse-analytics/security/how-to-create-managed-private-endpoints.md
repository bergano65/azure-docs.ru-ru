---
title: Создайте управляемую закрытую конечную точку для подключения к результатам источника данных.
description: Из этой статьи вы узнаете, как создать управляемую закрытую конечную точку для источников данных из рабочей области Azure синапсе.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: c6f0054364e5bbfb80fc4c1b9db6a4c15f365a81
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85193895"
---
# <a name="create-a-managed-private-endpoint-to-your-data-source-preview"></a>Создание управляемой частной конечной точки для источника данных (Предварительная версия)

Из этой статьи вы узнаете, как создать управляемую закрытую конечную точку для источника данных в Azure. Дополнительные сведения см. в разделе [управляемые частные конечные точки](./synapse-workspace-managed-private-endpoints.md) .

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>Шаг 1. Откройте рабочую область Azure синапсе в портал Azure

Вы можете создать управляемую закрытую конечную точку для источника данных из Azure синапсе Studio. Перейдите на вкладку **Обзор** в портал Azure и выберите **запустить синапсе Studio**.
![Запуск Azure синапсе Studio](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-1.png)

## <a name="step-2-navigate-to-the-managed-virtual-networks-tab-in-synapse-studio"></a>Шаг 2. Перейдите на вкладку "управляемые виртуальные сети" в синапсе Studio

В Azure синапсе Studio в области навигации слева выберите вкладку **Управление** . Выберите **управляемые виртуальные сети** и щелкните **+ создать**.
![Создание новой управляемой частной конечной точки](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-2.png)

## <a name="step-3-select-the-data-source-type"></a>Шаг 3. Выбор типа источника данных

Выберите тип источника данных. В этом случае целевой источник данных является учетной записью ADLS 2-го поколения. Выберите **Continue** (Продолжить).
![Выберите целевой тип источника данных](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-3.png)

## <a name="step-4-enter-information-about-the-data-source"></a>Шаг 4. Ввод сведений об источнике данных

В следующем окне введите сведения об источнике данных. В этом примере мы создаем управляемую закрытую конечную точку для учетной записи ADLS 2-го поколения. Введите **имя** управляемой частной конечной точки. Укажите **подписку Azure** и **имя учетной записи хранения**. Выберите **Создать**.
![Введите сведения о целевом источнике данных](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-4.png)

## <a name="step-5-verify-that-your-managed-private-endpoint-was-successfully-created"></a>Шаг 5. Проверка успешного создания управляемой частной конечной точки

После отправки запроса вы увидите его состояние. Чтобы проверить успешность создания управляемой частной конечной точки, проверьте ее *состояние подготовки*. Возможно, потребуется подождать 1 минуту и выбрать **Обновить** , чтобы обновить состояние подготовки. Вы видите, что управляемая частная конечная точка была успешно создана для учетной записи ADLS 2-го поколения.

Вы также можете увидеть, что *состояние утверждения* — *Ожидание*. Владелец целевого ресурса может утвердить или отклонить запрос на подключение частной конечной точки. Если владелец утверждает запрос на подключение к закрытой конечной точке, устанавливается частная ссылка. Если отказано, то частная ссылка не устанавливается.
![Состояние запроса на создание управляемой частной конечной точки](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-5.png)

## <a name="next-steps"></a>Дальнейшие шаги

См. дополнительные сведения об [управляемых частных конечных точках](./synapse-workspace-managed-private-endpoints.md) и статью