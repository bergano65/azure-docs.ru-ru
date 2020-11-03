---
title: Руководство по интеграции с конвейерами
description: Из этого руководства вы узнаете, как интегрировать конвейеры и действия с помощью Synapse Studio.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: tutorial
ms.date: 10/27/2020
ms.openlocfilehash: af01d5b5e424dd5ea229115f7aa3570d0b7cd511
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92744934"
---
# <a name="integrate-with-pipelines"></a>Интеграция с конвейерами

Из этого руководства вы узнаете, как интегрировать конвейеры и действия с помощью Synapse Studio. 

## <a name="overview"></a>Обзор

В Azure Synapse можно интегрировать множество задач.

1. В Synapse Studio перейдите в центр **Интеграция**.
1. Выберите **+**  > **Конвейер** , чтобы создать новый конвейер.
1. Перейдите в центр **Разработка** и выберите одну из созданных ранее записных книжек.
1. Перетащите эту записную книжку в конвейер. ( **Примечание:** добавьте в записную книжку шаг импорта модулей, как указано в этом [документе](https://docs.microsoft.com/azure/synapse-analytics/spark/synapse-spark-sql-pool-import-export#transfer-data-to-or-from-a-sql-pool-attached-with-the-workspace); это необходимо при запуске из конвейера.)
1. В конвейере выберите **Добавить триггер** > **Создать или изменить**.
1. В разделе **Выберите триггер** щелкните **Создать** и задайте для параметра **Повторения** значение "Каждый час".
1. Щелкните **ОК**. 
1. Выберите **Опубликовать все**.
1. Чтобы запустить конвейер немедленно, не дожидаясь следующего часа, выберите **Добавить триггер** > **Trigger now** (Активировать сейчас).



## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Визуализация данных с помощью Power BI](get-started-visualize-power-bi.md)
                                 
