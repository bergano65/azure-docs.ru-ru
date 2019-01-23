---
title: Проверка текста в Azure Content Moderator — Content Moderator
description: Узнайте, как проверить текст в Content Moderator, чтобы просмотреть его оценку и обнаруженные теги. С помощью этих сведений можно определить уместность содержимого.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 005e6ed853cbc6013d74e7ff479097dcbb0a1043
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54259569"
---
# <a name="let-human-reviewers-review-text"></a>Проверка текста человеком

Azure Content Moderator можно использовать для проверки текста с помощью оценок и обнаруженных тегов. С помощью этих сведений можно определить уместность содержимого. 

## <a name="select-or-enter-the-text-to-review"></a>Выбор или ввод текста для проверки

В Content Moderator выберите вкладку **Try** (Пробная версия). Выберите **Text** (Текст), чтобы перейти на начальный экран модерации текста. Введите любой текст или отправьте пример текста по умолчанию для автоматической модерации. Его длина не должна превышать 1024 знака.

## <a name="get-ready-to-review-results"></a>Подготовка к проверке результатов

Сначала инструмент проверки API модерации текста. Затем он создает результаты проверки текста с помощью обнаруженных тегов. Инструмент проверки сопоставляет результаты оценки для ознакомления вашей командой.

## <a name="review-text-results"></a>Проверка результатов текста

Подробные результаты отображаются в окнах. Результаты включают в себя обнаруженные теги и термины, которые были возвращены API модерации текста. Чтобы переключить состояние выбора тега, выберите этот тег. Можно также работать с любыми настраиваемыми тегами, созданными вами.

![Снимок экрана с отображением в средстве проверки отмеченного текста в окне браузера Chrome](../images/reviewresults_text.png)
