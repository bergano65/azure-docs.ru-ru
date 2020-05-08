---
title: Краткое руководство. Использование пользовательских команд с настраиваемым голосом (Предварительная версия) — служба речи
titleSuffix: Azure Cognitive Services
description: В этой статье вы укажете выходной поток для приложения с пользовательскими командами.
services: cognitive-services
author: anhoang
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: anhoang
ms.openlocfilehash: c2b9b4d51e89975d988ed94bf85695bd8a1cc770
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872467"
---
# <a name="quickstart-use-custom-commands-with-custom-voice-preview"></a>Краткое руководство. Использование пользовательских команд с настраиваемым голосовым стандартом (Предварительная версия)

В [предыдущей статье](./quickstart-custom-speech-commands-create-parameters.md)мы создали новый проект пользовательских команд для реагирования на команды с параметрами.

В этой статье мы будем выбирать пользовательский выходной поток для созданного приложения.

## <a name="select-a-custom-voice"></a>Выбор пользовательского голоса

1. Откройте [созданный ранее](./quickstart-custom-speech-commands-create-parameters.md) проект
1. На панели слева выберите **Параметры** .
1. Выбор **пользовательского голоса** из средней области
1. Выбор требуемого пользовательского или общедоступного голоса из таблицы
1. Нажмите кнопку **Сохранить**.

> [!div class="mx-imgBorder"]
> ![Примеры предложений с параметрами](media/custom-speech-commands/select-custom-voice.png)

> [!NOTE]
> - Для **общих голосов** **типы нейронов** доступны только для конкретных регионов. Сведения о проверке доступности см. в статье [Стандартные и нейронные голоса по регионам и конечным точкам](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices).
> - Для **пользовательских голосов**их можно создать на странице настраиваемого голосового проекта. См. статью Начало [работы с пользовательским голосовым стандартом](./how-to-custom-voice.md).

Теперь приложение будет отвечать на выбранный голоса вместо голоса по умолчанию.

## <a name="next-steps"></a>Дальнейшие действия
> [!div class="nextstepaction"]
> [Краткое руководство. подключение к пользовательскому командному приложению с помощью речевого пакета SDK (Предварительная версия)](./quickstart-custom-speech-commands-speech-sdk.md)

