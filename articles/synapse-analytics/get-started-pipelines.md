---
title: Руководство по оркестрации с использованием конвейеров
description: В этом учебнике вы узнаете, как управлять конвейерами и действиями с помощью Synapse Studio.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: tutorial
ms.date: 10/16/2020
ms.openlocfilehash: 42d2ac6cf6592f8e22b0a66aee84c3436d466572
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2020
ms.locfileid: "92329889"
---
# <a name="orchestrate-with-pipelines"></a>Оркестрация с использованием конвейеров

В этом учебнике вы узнаете, как управлять конвейерами и действиями с помощью Synapse Studio. 

## <a name="overview"></a>Обзор

В Azure Synapse можно управлять множеством задач.

1. В Synapse Studio перейдите в центр **Интеграция** .
1. Выберите **+**  > **Конвейер** , чтобы создать новый конвейер.
1. Перейдите в центр **Разработка** и выберите одну из созданных ранее записных книжек.
1. Перетащите эту записную книжку в конвейер. ( **Примечание:** добавьте в записную книжку шаг импорта модулей, как указано в этом [документе](https://docs.microsoft.com/azure/synapse-analytics/spark/synapse-spark-sql-pool-import-export#transfer-data-to-or-from-a-sql-pool-attached-with-the-workspace); это необходимо при запуске из конвейера.)
1. В конвейере выберите **Добавить триггер** > **Создать или изменить** .
1. В разделе **Выберите триггер** щелкните **Создать** и задайте для параметра **Повторения** значение "Каждый час".
1. Щелкните **ОК** . 
1. Выберите **Опубликовать все** .
1. Чтобы запустить конвейер немедленно, не дожидаясь следующего часа, выберите **Добавить триггер** > **Trigger now** (Активировать сейчас).



## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Визуализация данных с помощью Power BI](get-started-visualize-power-bi.md)
                                 
