---
title: Масштабируйте DB Azure Cosmos по расписанию, используя таймер функций Azure
description: Узнайте, как масштабировать изменения пропускной способностью в Azure Cosmos DB с помощью функций PowerShell и Azure.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: mjbrown
ms.openlocfilehash: 68ba40ea212c061fa5c8bbddc47ea0dfc6d8caa4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75935172"
---
# <a name="scale-azure-cosmos-db-throughput-by-using-azure-functions-timer-trigger"></a>Масштабируйте пропускную связь Azure Cosmos DB с помощью триггера Azure Functions Timer

Производительность учетной записи Azure Cosmos основана на объеме прокладки, выраженной в единицах запроса в секунду (RU/s). Подготовка находится на второй детализации и выставляется на основе самых высоких RU / s в час. Данная модель подготовленной емкости позволяет службе обеспечивать предсказуемую и согласованную пропускную способность, гарантированную низкую задержку и высокий уровень доступности. Большинство производственных нагрузок этих функций. Однако в средах разработки и тестирования, где Azure Cosmos DB используется только в рабочее время, можно увеличить пропускную связь утром и сократить масштаб ы вечером после рабочего дня.

Вы можете настроить пропускную способность с помощью [шаблонов менеджера ресурсов Azure,](resource-manager-samples.md) [Azure CLI](cli-samples.md)и [PowerShell](powershell-samples-sql.md)для учетных записей API Core (S'L) или с помощью специальных SDK Azure Cosmos DD. Преимущество использования шаблонов диспетчера ресурсов, Azure CLI или PowerShell заключается в том, что они поддерживают все AB-модели Azure Cosmos DB.

## <a name="throughput-scheduler-sample-project"></a>Проект выборочного образца планировщика пропускных данных

Чтобы упростить процесс масштабирования Azure Cosmos DB по графику, мы создали пример проекта под названием [Планировщик пропускной работы Azure Cosmos.](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler) Этот проект представляет собой приложение Azure Functions с двумя триггерами таймер - "ScaleUpTrigger" и "ScaleDownTrigger". Триггеры запускают скрипт PowerShell, который устанавливает пропускную `resources.json` способность на каждом ресурсе, как это определено в файле в каждом триггере. ScaleUpTrigger настроен для запуска в 8 утра UTC и ScaleDownTrigger настроен для запуска в 6 вечера UTC и эти времена могут быть легко обновлены в `function.json` файл для каждого триггера.

Можно клонировать этот проект локально, изменять его, чтобы указать ресурсы Azure Cosmos DB для масштабирования вверх и вниз и расписания для запуска. Позже вы можете развернуть его в подписке Azure и обезопасить его, используя управляемый идентификатор службы с разрешениями [На основе role Access Control](role-based-access-control.md) (RBAC) с ролью "Оператор Azure Cosmos DB" для настройки пропускной записи на учетных записях Azure Cosmos.

## <a name="next-steps"></a>Next Steps

- Узнайте больше и загрузите образец из [планировщика пропускной выхаживания Пропускной список Azure Cosmos DB.](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler)
