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
ms.openlocfilehash: 72eea7c46dd005cd16ae5b8f0022c1174dd28f27
ms.sourcegitcommit: 0194a29a960e3615f96a2d9d8a7e681cf3e8f9ab
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89667477"
---
# <a name="orchestrate-with-pipelines"></a>Оркестрация с использованием конвейеров

В этом учебнике вы узнаете, как управлять конвейерами и действиями с помощью Synapse Studio. 

## <a name="overview"></a>Обзор

В Azure Synapse можно управлять множеством задач.

1. В Synapse Studio перейдите в центр **Оркестрация**.
1. Выберите **+**  > **Конвейер**, чтобы создать новый конвейер.
1. Перейдите в центр **Разработка** и выберите одну из созданных ранее записных книжек.
1. Перетащите эту записную книжку в конвейер.
1. В конвейере выберите **Добавить триггер** > **Создать или изменить**.
1. В окне **Выбор триггера** выберите **Создать**, а затем для **повторения** задайте ежечасный запуск триггера.
1. Щелкните **ОК**.
1. Выберите **Опубликовать все**. Конвейер запускается каждый час.
1. Чтобы конвейер выполнился сейчас, не дожидаясь следующего часа, выберите **Добавить триггер** > **Создать или изменить**.



## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Визуализация данных с помощью Power BI](get-started-visualize-power-bi.md)
                                 
