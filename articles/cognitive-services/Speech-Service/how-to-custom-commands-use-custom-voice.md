---
title: Как использовать пользовательские команды с пользовательской службой голосовой речи
titleSuffix: Azure Cognitive Services
description: В этой статье вы укажете выходной поток для приложения с пользовательскими командами.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/16/2020
ms.author: sausin
ms.openlocfilehash: b1e488653f210089ee5ed6757fb2f3d1e4fb0fe4
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294548"
---
# <a name="use-custom-commands-with-custom-voice"></a>Использование пользовательских команд с Пользовательским голосом

В этой статье вы узнаете, как выбрать пользовательский выходной поток для приложения с пользовательскими командами.

## <a name="select-a-custom-voice"></a>Выбор пользовательского голоса

1. В приложении "пользовательские команды" выберите **Параметры** в левой области.
1. Выберите **Пользовательский голосовое** значение в средней области.
1. Выберите нужный пользовательский или общедоступный голосовое значение из таблицы.
1. Щелкните **Сохранить**.

> [!div class="mx-imgBorder"]
> ![Примеры предложений с параметрами](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> - Для **общих голосов** **типы нейронов** доступны только для конкретных регионов. Сведения о проверке доступности см. в статье [Стандартные и нейронные голоса по регионам и конечным точкам](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices).
> - Для **пользовательских голосов**их можно создать на странице настраиваемого голосового проекта. См. статью Начало [работы с пользовательским голосовым стандартом](./how-to-custom-voice.md).

Теперь приложение будет отвечать на выбранный голоса вместо голоса по умолчанию.
