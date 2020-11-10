---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: v-demjoh
ms.openlocfilehash: a5796dfb066ea8ac7c59c02b29ef67b184c67edb
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94425247"
---
Одной из основных функций речевой службы является возможность распознавания речи и перевода их на другие языки. В этом кратком руководстве вы узнаете, как использовать речевой пакет SDK в приложениях и продуктах для выполнения высококачественного перевода речи. Это краткое руководство преобразует речь из микрофона в текст на другом языке.

## <a name="prerequisites"></a>Предварительные требования

В этой статье предполагается, что у вас есть учетная запись Azure и подписка на службу "Речь". Если у вас нет учетной записи и подписки, [попробуйте службу "Речь" бесплатно](../../../overview.md#try-the-speech-service-for-free).

[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="set-source-and-target-language"></a>Задание исходного и целевого языка

Эта команда вызывает интерфейс командной строки для перевода речи с микрофона с итальянского на французский.

```shell
 spx translate --microphone --source it-IT --target fr
```