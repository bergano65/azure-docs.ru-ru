---
title: Простой журнал в Azure Monitor (Предварительная версия) | Документация Майкрософт
description: Простой журнал позволяет создавать простые запросы в Azure Monitor без взаимодействия с ККл.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: bwren
ms.openlocfilehash: 323267dd47735ca54b84e47e6a55d1f2d14a0b06
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262174"
---
# <a name="simple-logs-experience-in-azure-monitor-preview"></a>Простой журнал в Azure Monitor (Предварительная версия)
Azure Monitor предоставляет [широкие возможности](get-started-portal.md) для создания [запросов журналов](log-query-overview.md) с помощью языка ККЛ. Вы можете не требовать полной мощности ККЛ хотя и предпочесть упрощенный интерфейс для основных требований к запросам. Простая процедура работы с журналами позволяет создавать простые запросы без непосредственного взаимодействия с ККл. Можно также использовать простые журналы в качестве средства обучения для ККЛ, так как требуется более сложные запросы.

> [!NOTE]
> В настоящее время простые журналы реализуются только для Cosmos DB и хранилищ ключей. Поделитесь своим опытом с корпорацией Майкрософт с помощью [голоса пользователя](https://feedback.azure.com/forums/913690-azure-monitor) , чтобы помочь нам определить, будет ли мы расширять и освобождать эту функцию.


## <a name="scope"></a>`Scope`
При работе с простыми журналами извлекаются данные из таблицы *AzureDiagnostics*, *азуреметрикс*и *AzureActivity* для выбранного ресурса. 

## <a name="using-simple-logs"></a>Использование простых журналов
Перейдите к любому Cosmos DB или Key Vault в подписке Azure с [параметрами диагностики, настроенными для получения журналов в рабочей области log Analytics](../platform/resource-logs-collect-storage.md). Щелкните **журналы** в меню **мониторинг** , чтобы открыть простой журнал.

![Меню](media/simple-logs/menu.png)

Выберите **поле** и **оператор** и укажите **значение** для сравнения. Щелкните **+** и укажите **и/или** , чтобы добавить дополнительные критерии.

![Критерии](media/simple-logs/criteria.png)

Нажмите кнопку **выполнить** , чтобы просмотреть результаты запроса.

## <a name="view-and-edit-kql"></a>Просмотр и изменение ККЛ
Выберите **Редактор запросов** , чтобы открыть ККЛ, созданный простым запросом журналов, в полной log Analytics. 

![Редактор запросов](media/simple-logs/query-editor.png)

Вы можете напрямую изменить ККЛ и использовать другие функции Log Analytics например фильтры, чтобы уточнить результаты.

![Изменить ККЛ](media/simple-logs/edit-kql.png)


## <a name="next-steps"></a>Следующие шаги

- Завершите [работу с руководством по использованию log Analytics в портал Azure](get-started-portal.md).
- Заполните учебник по [написанию запросов к журналам](get-started-portal.md).
