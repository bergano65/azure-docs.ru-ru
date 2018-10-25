---
title: Использование портала Azure для мониторинга управляемого приложения | Документация Майкрософт
description: Показано, как использовать портал Azure для мониторинга доступности и оповещений управляемого приложения.
services: managed-applications
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: ea9f55d7c6002aaba2fd4fdc2a76b93f98e1d6b0
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48807804"
---
# <a name="monitor-a-deployed-instance-of-a-managed-application"></a>Мониторинг развернутого экземпляра управляемого приложения

После развертывания управляемого приложения в вашей подписке Azure, можно проверить состояние приложения. В этой статье показаны параметры проверки состояния на портале Azure. Можно отслеживать доступность ресурсов в управляемом приложении. Можно также настроить и просмотреть оповещения.

## <a name="view-resource-health"></a>Просмотр состояния работоспособности ресурса.

1. Выберите экземпляр управляемого приложения.

   ![Выбор управляемого приложения](./media/monitor-managed-application-portal/select-managed-application.png)

1. Выберите **Работоспособность ресурсов**.

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

В управляемом приложении, для его экземпляра или ресурсов можно создать оповещения. Сведения о создании оповещений см. в статье [Обзор оповещений в Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-alerts.md).

## <a name="next-steps"></a>Дополнительная информация

* См. дополнительные сведения о [примерах проектов для управляемых приложений Azure](sample-projects.md).
* Сведения о том, как развернуть экземпляр управляемого приложения, см. в статье [Развертывание приложения из каталога служб с помощью портала Azure](deploy-service-catalog-quickstart.md).