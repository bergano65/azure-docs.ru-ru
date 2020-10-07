---
title: включить файл
description: включить файл
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 08/25/2020
ms.openlocfilehash: 4eacc1c4e863ad1a278a4974bb0f6c101aafe7e0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2020
ms.locfileid: "89055408"
---
### <a name="change-the-model-update-frequency"></a>Изменение частоты обновления модели

На портале Azure в ресурсе "Персонализатор" на странице **Настройка** установите значение для параметра **Частота обновления модели** на 10 секунд. С такой высокой частотой служба будет обучаться быстро, и вы увидите, как главное действие меняется с каждой итерацией.

![Изменение частоты обновления модели](../media/settings/configure-model-update-frequency-settings.png)

После создания цикла Персонализатора модель не используется, поскольку отсутствуют вызовы API вознаграждения для обучения. Вызовы ранжирования будут возвращать равновероятностный результат для каждого элемента. Приложение должно по-прежнему ранжировать содержимое на основе выходных данных RewardActionId.