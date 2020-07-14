---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: f26f0ab6da398dcdee307f89b27cca780d08af85
ms.sourcegitcommit: 374d1533ea2f2d9d3f8b6e6a8e65c6a5cd4aea47
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/01/2020
ms.locfileid: "85838965"
---
## <a name="prerequisites"></a>Предварительные требования

Необходимые условия:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Создайте ресурс службы "Речь" в Azure <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
> * [Настройте среду разработки и создайте пустой проект](../../../../quickstarts/setup-platform.md?pivots=programming-language-python).
> * Убедитесь, что у вас есть доступ к микрофону для аудиозахвата.

## <a name="source-code"></a>Исходный код

Создайте файл с именем *quickstart.py* и вставьте в него приведенный код Python.

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/from-microphone/quickstart.py#code)]

[!INCLUDE [replace key and region](../replace-key-and-region.md)]

## <a name="code-explanation"></a>Пояснение к коду

[!INCLUDE [code explanation](../code-explanation.md)]

## <a name="build-and-run-app"></a>Компиляция и запуск приложения

Теперь можно приступать к проверке распознавания речи с помощью службы "Речь". 

Если вы выполняете ее в macOS и это первое созданное вами приложение Python, использующее микрофон, возможно, вам потребуется предоставить терминалу доступ к микрофону. Откройте **параметры системы** и выберите **Безопасность и конфиденциальность**. Затем выберите **Конфиденциальность** и найдите **Микрофон** в списке. Выберите **Терминал** и сохраните параметры. 

1. **Запустите приложение**. В командной строке введите следующее:
    ```bash
    python quickstart.py
    ```
2. **Начните распознавание**. Вам будет предложено произнести фразу на английском языке. Речь, записанная в виде текста, отправляется в службу "Речь" и выводится в консоли.

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]
