---
title: включить файл
description: включить файл
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 01/15/2020
ms.author: diberry
ms.openlocfilehash: 7ef219e6b5f7547029612ec3898efec51abd4712
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122747"
---
## <a name="change-the-model-update-frequency"></a>Изменение частоты обновления модели

На портале Azure в ресурсе "Персонализатор" на странице **Настройка** установите значение для параметра **Частота обновления модели** на 10 секунд. С такой высокой частотой служба будет обучаться быстро, и вы увидите, как главное действие меняется с каждой итерацией.

![Изменение частоты обновления модели](../media/settings/configure-model-update-frequency-settings.png)

После создания цикла Персонализатора модель не используется, поскольку отсутствуют вызовы API вознаграждения для обучения. Вызовы ранжирования будут возвращать равновероятностный результат для каждого элемента. Приложение должно по-прежнему ранжировать содержимое на основе выходных данных RewardActionId.