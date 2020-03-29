---
title: Подключение данных о деятельности Azure к Azure Sentinel Документы Майкрософт
description: Узнайте, как подключить данные деятельности Azure к Azure Sentinel.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124983"
---
# <a name="connect-data-from-azure-activity-log"></a>Подключение данных из журнала деятельности Azure

Вы можете одним щелчком мыши передавать журналы из входа в [Azure Activity](../azure-monitor/platform/platform-logs-overview.md) в Azure Sentinel. Журнал активности — это журнал подписки, который записывает и отображает события уровня подписки в Azure, начиная с оперативных данных менеджера ресурсов Azure и обновляя события службы. Используя журнал Activity, вы можете определить, что, кто и когда " для любой операции записи (PUT, POST, DELETE) выполняется на ресурсах в подписке. Вы также можете узнать состояние операции и другие соответствующие свойства. В журнале activity не содержится считывания (GET) операций или операций для ресурсов, которые используют модель Classic/"RDFE". 

## <a name="prerequisites"></a>Предварительные требования

- Пользователь должен иметь разрешения на работу в рабочей области Log Analytics.
- Пользователь должен иметь разрешения Reader на любую подписку, журналы которой вы хотите транслировать в Azure Sentinel.

## <a name="set-up-the-azure-activity-connector"></a>Настройка разъема активности Azure

1. Из меню навигации Azure Sentinel выберите **разъемы данных.** Из списка разъемов нажмите на **Azure Activity,** а затем на кнопку **«Открытая разъемная страница»** в правом нижнем правом.

2. Под вкладкой **«Инструкции»** **щелкните журналы активности Azure >** ссылку.

3. В панели **журнала Azure Activity** выберите подписки, журналы которых вы хотите передать в Azure Sentinel. 

4. В панели подписки, которая открывается вправо, нажмите **Connect**.

5. Чтобы использовать соответствующую схему в журнале Analytics для `AzureActivity` предупреждений о деятельности Azure, введите окно запроса.

## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как подключить журнал деятельности Azure к журналу Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Начать обнаруживать угрозы с помощью Azure Sentinel, используя [встроенные](tutorial-detect-threats-built-in.md) или [пользовательские](tutorial-detect-threats-custom.md) правила.
