---
title: Шаблоны Azure Resource Manager
description: Azure Resource Manager шаблоны для использования Azure Site Recoveryных функций.
services: site-recovery
author: rishjai
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 02/04/2021
ms.author: rishjai
ms.openlocfilehash: 0477045ac48ee2746e3d6a1dd95051673412ffee
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99551455"
---
# <a name="azure-resource-manager-templates-for-azure-site-recovery"></a>Шаблоны Azure Resource Manager для Azure Site Recovery

В следующей таблице приведены ссылки на шаблоны Azure Resource Manager для использования Azure Site Recoveryных функций.

| Шаблон | Описание |
|---|---|
|**Из Azure в Azure** | |
| [создание хранилища служб восстановления](https://docs.microsoft.com/azure/site-recovery/quickstart-create-vault-template);| Создайте хранилище служб восстановления, Хранилище можно использовать для Azure Backup и Azure Site Recovery. |
| [Включение репликации для виртуальных машин Azure](https://aka.ms/asr-arm-enable-replication) | Включите репликацию для виртуальных машин Azure, используя существующее хранилище и настраиваемые целевые параметры.|
| [Активация отработки отказа и повторного включения защиты](https://aka.ms/asr-arm-failover-reprotect) | Активируйте операцию отработки отказа и повторного включения защиты для набора виртуальных машин Azure. |
| [Выполнение сквозного процесса аварийного восстановления для виртуальных машин Azure](https://aka.ms/asr-arm-e2e-flow) | Запустите комплексный поток аварийного восстановления (включите репликацию + отработку отказа и повторно включите защиту + восстановление размещения и повторное включение защиты) для виртуальных машин Azure, которые также называются потоком 540 °.|
|   |   |