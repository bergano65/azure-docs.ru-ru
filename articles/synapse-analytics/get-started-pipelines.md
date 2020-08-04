---
title: Руководство по оркестрации с использованием конвейеров
description: В этом учебнике вы узнаете, как управлять конвейерами и действиями с помощью Synapse Studio.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 5e32a6a9817f2a3176e96e39c5e261875e8f4ed1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87093795"
---
# <a name="orchestrate-with-pipelines"></a>Оркестрация с использованием конвейеров

В этом учебнике вы узнаете, как управлять конвейерами и действиями с помощью Synapse Studio. 

## <a name="overview"></a>Обзор

В Azure Synapse можно управлять множеством задач.

1. В Synapse Studio перейдите в центр **Оркестрация**.
1. Выберите **+**  > **Конвейер**, чтобы создать новый конвейер.
1. Перейдите в центр **Разработка** и найдите созданную ранее записную книжку.
1. Перетащите эту записную книжку в конвейер.
1. В конвейере выберите **Добавить триггер** > **Создать или изменить**.
1. В окне **Выбор триггера** выберите **Создать**, а затем для **повторения** задайте ежечасный запуск триггера.
1. Щелкните **ОК**.
1. Выберите **Опубликовать все**. Конвейер запускается каждый час.
1. Чтобы конвейер выполнился сейчас, не дожидаясь следующего часа, выберите **Добавить триггер** > **Создать или изменить**.



## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Визуализация данных с помощью Power BI](get-started-visualize-power-bi.md)
                                 
