---
title: Обновление команды из конечной точки веб-сайта
titleSuffix: Azure Cognitive Services
description: обновление команды из конечной точки веб-сайта
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: encorona
ms.openlocfilehash: 4432843ac93002bc92068db191706352234d76e6
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94571293"
---
# <a name="update-a-command-from-a-web-endpoint"></a>Обновление команды из конечной точки веб-сайта

Если клиентскому приложению требуется обновить состояние текущей команды без речевого ввода, можно использовать вызов конечной веб-точки для обновления команды.

В этой статье вы узнаете, как обновить текущую команду из веб-конечной точки.

## <a name="prerequisites"></a>Предварительные условия
> [!div class = "checklist"]
> * Предварительно [созданное приложение Пользовательских голосовых команд](quickstart-custom-commands-application.md).

## <a name="create-an-azure-function"></a>Создание функции Azure 

В этом примере вам потребуется HTTP-Triggered [функция Azure](https://docs.microsoft.com/azure/azure-functions/) , которая поддерживает следующие входные данные (или подмножество этих входных данных).

```JSON
{
  "conversationId": "SomeConversationId",
  "currentCommand": {
    "name": "SomeCommandName",
    "parameters": {
      "SomeParameterName": "SomeParameterValue",
      "SomeOtherParameterName": "SomeOtherParameterValue"
    }
  },
  "currentGlobalParameters": {
      "SomeGlobalParameterName": "SomeGlobalParameterValue",
      "SomeOtherGlobalParameterName": "SomeOtherGlobalParameterValue"
  }
}
```

Позволяет просматривать ключевые атрибуты этого входа.

| Атрибут | Пояснение |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **conversationId** | "Конверсатионид" — это уникальный идентификатор диалога, обратите внимание, что этот идентификатор может быть создан из клиентского приложения. |
| **курренткомманд** | "Курренткомманд" — это команда, которая активна в данный момент в диалоге. |
| **name** | "Name" — это имя команды, а "Parameters" — это таблица с текущими значениями параметров. |
| **куррентглобалпараметерс** | "Куррентглобалпараметерс" также является картой, например "Parameters", но используется для глобальных параметров. |

Выходные данные функции Azure должны поддерживать следующий формат.

```JSON
{
  "updatedCommand": {
    "name": "SomeCommandName",
    "updatedParameters": {
      "SomeParameterName": "SomeParameterValue"
    },
    "cancel": false
  },
  "updatedGlobalParameters": {
    "SomeGlobalParameterName": "SomeGlobalParameterValue"
  }
}
```

Вы можете распознать этот формат, так как он используется при [обновлении команды клиентом](./how-to-custom-commands-update-command-from-client.md). 

Теперь создайте функцию Azure на основе NodeJS и скопируйте и вставьте этот код

```nodejs
module.exports = async function (context, req) {
    context.log(req.body);
    context.res = {
        body: {
            updatedCommand: {
                name: "IncrementCounter",
                updatedParameters: {
                    Counter: req.body.currentCommand.parameters.Counter + 1
                }
            }
        }
    };
}
```

При вызове этой функции Azure из пользовательских команд мы отправим текущие значения диалога и возвращаем параметры, которые нужно обновить, или если нам нужно отменить текущую команду.

## <a name="update-the-existing-custom-commands-app"></a>Обновление существующего приложения пользовательских команд

Теперь давайте подключим функцию Azure к существующему приложению с пользовательскими командами.

1. Добавьте новую команду с именем Инкременткаунтер.
1. Добавьте только один пример предложения со значением "Increment".
1. Добавьте новый параметр с именем Counter (то же имя, как указано в функции Azure выше) типа Number со значением по умолчанию 0.
1. Добавьте новую веб-конечную точку с именем Инкрементендпоинт с URL-адресом функции Azure и с включенными удаленными обновлениями.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-web-endpoint-with-remote-updates.png" alt-text="Задать конечную точку веб-сайта с удаленными обновлениями":::
1. Создайте новое правило взаимодействия с именем "Инкрементруле" и добавьте действие вызов конечной веб-точки.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-rule-web-endpoint.png" alt-text="Правило приращения":::
1. В конфигурации действия выберите Инкрементендпоинт, настройте on Success (успешно), чтобы отправить речевой ответ со значением счетчика и при сбое с сообщением об ошибке.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-increment-counter-call-endpoint.png" alt-text="Задать конечную точку вызова счетчика приращений":::
1. Задание состояния после выполнения правила для ожидания ввода данных пользователем

## <a name="test-it"></a>Тестирование

1. Сохранение и обучение приложения
1. Щелкните тест
1. Отправить несколько раз "инкремент" (это пример предложения для команды Инкременткаунтер)
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-counter-example.png" alt-text="Пример счетчика приращений":::

Обратите внимание, что значение параметра Counter увеличивается при каждом включении функции Azure.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Включение процесса CI/CD для вашего приложения Пользовательских голосовых команд](./how-to-custom-commands-deploy-cicd.md)