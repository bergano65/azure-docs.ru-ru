---
title: Использование портал Azure для мониторинга управляемого приложения
description: Показано, как использовать портал Azure для мониторинга доступности и оповещений управляемого приложения.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: afe78dd00ecebdc54b6d73c4c8324729e117d95b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "75651752"
---
# <a name="monitor-a-deployed-instance-of-a-managed-application"></a>Мониторинг развернутого экземпляра управляемого приложения

После развертывания управляемого приложения в вашей подписке Azure, можно проверить состояние приложения. В этой статье показаны параметры проверки состояния на портале Azure. Можно отслеживать доступность ресурсов в управляемом приложении. Можно также настроить и просмотреть оповещения.

## <a name="view-resource-health"></a>Просмотр состояния работоспособности ресурса.

1. Выберите экземпляр управляемого приложения.

   ![Выбор управляемого приложения](./media/monitor-managed-application-portal/select-managed-application.png)

1. Выберите элемент **Работоспособность ресурсов**.

   ![Выбор работоспособности ресурсов](./media/monitor-managed-application-portal/select-resource-health.png)

1. В управляемом приложении можно отслеживать доступность ресурсов.

   ![Просмотр состояния работоспособности ресурса.](./media/monitor-managed-application-portal/view-health.png)

## <a name="view-alerts"></a>Просмотр оповещений

1. Выберите **Оповещения**.

   ![Выбор оповещений](./media/monitor-managed-application-portal/select-alerts.png)

1. Если правила генерации оповещений настроены, появится информация о созданных оповещениях.

   ![Просмотр оповещений](./media/monitor-managed-application-portal/view-alerts.png)

1. Чтобы добавить правила генерации оповещений, выберите **+Новое правило генерации оповещений**.

   ![Создание оповещения](./media/monitor-managed-application-portal/create-new-alert.png)

В управляемом приложении, для его экземпляра или ресурсов можно создать оповещения. Сведения о создании оповещений см. в статье [Обзор оповещений в Microsoft Azure](../../azure-monitor/platform/alerts-overview.md).

## <a name="next-steps"></a>Дальнейшие действия

* См. дополнительные сведения о [примерах проектов для управляемых приложений Azure](sample-projects.md).
* Сведения о том, как развернуть экземпляр управляемого приложения, см. в статье [Развертывание приложения из каталога служб с помощью портала Azure](deploy-service-catalog-quickstart.md).