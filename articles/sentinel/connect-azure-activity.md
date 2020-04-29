---
title: Подключение данных действий Azure к Azure Sentinel | Документация Майкрософт
description: Узнайте, как подключить данные действий Azure к Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 8c25baa8-b93b-41da-9e6c-15bb7b5c5511
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2020
ms.author: yelevin
ms.openlocfilehash: 82dfcaf3394703aae531c828a1b96ad290bab798
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80124983"
---
# <a name="connect-data-from-azure-activity-log"></a>Подключение данных из журнала действий Azure

Вы можете выполнить потоковую передачу журналов из [журнала действий Azure](../azure-monitor/platform/platform-logs-overview.md) в метку Azure одним щелчком мыши. Журнал действий — это журнал подписки, который записывает и отображает события уровня подписки в Azure, от Azure Resource Manager операционных данных до обновлений событий работоспособности службы. С помощью журнала действий можно определить "что, кто и когда" для любой операции записи (помещение, публикация, удаление), выполняемой для ресурсов в вашей подписке. Вы также можете узнать состояние операции и другие соответствующие свойства. Журнал действий не включает операции чтения (GET) или операции для ресурсов, использующих классическую модель/"RDFE". 

## <a name="prerequisites"></a>Предварительные условия

- Пользователь должен иметь разрешения участника для рабочей области Log Analytics.
- Пользователь должен иметь разрешения читателя для всех подписок, журналы которых вы хотите передавать в Azure Sentinel.

## <a name="set-up-the-azure-activity-connector"></a>Настройка соединителя действий Azure

1. В меню навигации меток Azure выберите **соединители данных**. В списке соединителей щелкните **действие Azure**, а затем на кнопке **Открыть соединительную страницу** в правом нижнем углу.

2. На вкладке **инструкции** щелкните ссылку **Настройка журналов действий Azure >** .

3. В области **Журнал действий Azure** выберите подписки, журналы которых вы хотите передать в Azure Sentinel. 

4. В области подписка, которая открывается справа, нажмите кнопку **подключить**.

5. Чтобы использовать соответствующую схему в Log Analytics оповещений об активности Azure, введите `AzureActivity` в окне запроса.

## <a name="next-steps"></a>Дальнейшие шаги
В этом документе вы узнали, как подключить журнал действий Azure к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Приступите к обнаружению угроз с помощью Azure Sentinel, используя [встроенные](tutorial-detect-threats-built-in.md) или [Настраиваемые](tutorial-detect-threats-custom.md) правила.
