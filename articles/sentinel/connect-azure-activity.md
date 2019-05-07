---
title: Подключайте данные из действия Azure предварительную версию Sentinel Azure | Документация Майкрософт
description: Узнайте, как подключиться к Azure Sentinel данные о действиях Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 8c25baa8-b93b-41da-9e6c-15bb7b5c5511
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 67e4bb2d0f308a3f20f0a29ec8b1f2a0d46fc082
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65204361"
---
# <a name="connect-data-from-azure-activity-log"></a>Подключайте данные из журнала действий Azure

> [!IMPORTANT]
> Сейчас Azure Sentinel предоставляется в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Можно осуществлять потоковую передачу журналов из [журнал действий Azure](../azure-monitor/platform/activity-logs-overview.md) в Azure Sentinel одним щелчком. Журнал действий — это журнал подписки с подробными сведениями о событиях уровня подписки, которые произошли в Azure на. Сюда входят различные данные — от операционных данных Azure Resource Manager до обновлений в событиях работоспособности службы. С помощью журнала действий можно определить "что, кто и когда" о любой записи операции (PUT, POST, DELETE), выполненной с ресурсами в вашей подписке. Вы также можете отслеживать состояние операции и другие ее свойства. Журнал действий не содержит операции чтения (GET) или операции с ресурсами, которые используют классическую модель или модель «RDFE». 


## <a name="prerequisites"></a>Технические условия

- Пользователь с глобальным администратором или разрешения администратора безопасности


## <a name="connect-to-azure-activity-log"></a>Подключиться к журналу действий Azure

1. В Azure Sentinel, выберите **соединители данных** и нажмите кнопку **журнал действий Azure** плитку.

2. В области журнала действий Azure выберите подписки, которые вы хотите выполнять потоковую передачу в Azure Sentinel. 

3. Щелкните **Подключить**.

4. Чтобы использовать соответствующей схемы в Log Analytics для оповещений действий Azure, поиск **AzureActivity**.


 

## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как подключиться к Azure Sentinel журнал действий Azure. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [получить представление о данных и потенциальные угрозы](quickstart-get-visibility.md).
- Начало работы [обнаружение угроз с помощью Azure Sentinel](tutorial-detect-threats.md).
