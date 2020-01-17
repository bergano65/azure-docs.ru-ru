---
title: Подключение данных действий Azure к Azure Sentinel | Документация Майкрософт
description: Узнайте, как подключить данные действий Azure к Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 8c25baa8-b93b-41da-9e6c-15bb7b5c5511
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 107bbed8aea692a76f6d5bf6bbf1da26277b2c85
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156579"
---
# <a name="connect-data-from-azure-activity-log"></a>Подключение данных из журнала действий Azure



Вы можете выполнить потоковую передачу журналов из [журнала действий Azure](../azure-monitor/platform/platform-logs-overview.md) в метку Azure одним щелчком мыши. Журнал действий — это журнал подписки с подробными сведениями о событиях уровня подписки, произошедших в Azure. Сюда входят различные данные — от операционных данных Azure Resource Manager до обновлений в событиях работоспособности службы. С помощью журнала действий можно определить "что, кто и когда" для любой операции записи (размещения, публикации, удаления), полученной для ресурсов в вашей подписке. Вы также можете отслеживать состояние операции и другие ее свойства. Журнал действий не включает операции чтения (GET) или операции для ресурсов, использующих классическую модель/"RDFE". 


## <a name="prerequisites"></a>Технические условия

- Пользователь с разрешениями участника для Log Analytics рабочей области 


## <a name="connect-to-azure-activity-log"></a>Подключение к журналу действий Azure

1. В поле Sentinel Azure выберите **соединители данных** , а затем щелкните плитку **Журнал действий Azure** .

2. В области журнал действий Azure выберите подписки, которые требуется передать в Azure Sentinel. 

3. Нажмите кнопку **Соединить**.

4. Чтобы использовать соответствующую схему в Log Analytics для оповещений об активности Azure, выполните поиск по запросу **AzureActivity**.


 

## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как подключить журнал действий Azure к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).
