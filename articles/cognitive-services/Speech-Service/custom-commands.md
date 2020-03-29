---
title: Пользовательские команды (Предварительный просмотр) - Служба речи
titleSuffix: Azure Cognitive Services
description: Обзор функций, возможностей и ограничений для пользовательских команд (Preview), решения для создания голосовых приложений.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: 2e1b6ee0bd6c392804915fac6ff23278a00b6d33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367845"
---
# <a name="what-are-custom-commands-preview"></a>Что такое пользовательские команды (Предварительный просмотр)?

Голосовые приложения, такие как [голосовые помощники,](voice-assistants.md) слушают пользователей и принимают меры в ответ, часто выступая назад. Они используют [речевой текст](speech-to-text.md) для расшифровки речи пользователя, а затем принимают меры по пониманию текста на естественном языке. Это действие часто включает в себя устный вывод от помощника, генерируемого [текстом к речи.](text-to-speech.md) Устройства подключаются к помощникам с `DialogServiceConnector` объектом Speech SDK.

**Пользовательские команды (Preview)** — это упрощенное решение для создания голосовых приложений. Он обеспечивает единый авторский опыт, модель автоматического хостинга, и относительно более низкую сложность по сравнению с другими вариантами, такими как [Direct Line Speech.](direct-line-speech.md) Однако это упрощение сопровождается снижением гибкости. Таким образом, пользовательские команды (Preview) лучше всего подходят для выполнения задач или сценариев командования и управления. Это особенно хорошо сочетается с Интернетвещей (IoT) и безголовых устройств.

Для сложного разговорного взаимодействия и интеграции с другими решениями, такими как [решение Virtual Assistant и шаблон Enterprise,](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) рекомендуется использовать Direct Line Speech.

Хорошие кандидаты для пользовательских команд (Preview) имеют фиксированный словарь с четко определенными наборами переменных. Например, задачи домашней автоматизации, такие как управление термостатом, идеальны.

   ![Примеры сценариев завершения задач](media/voice-assistants/task-completion-examples.png "примеры завершения задач")

## <a name="getting-started-with-custom-commands-preview"></a>Начало работы с пользовательскими командами (Предварительный просмотр)

Первый шаг для использования пользовательских команд (Preview), чтобы сделать голосовое приложение, чтобы [получить ключ подписки речи](get-started.md) и доступ к пользовательским командам (Предварительный) Builder на [речевой студии.](https://speech.microsoft.com) Оттуда вы можете автор нового приложения Custom Commands (Preview) и опубликовать его, после чего приложение на устройстве может общаться с ним с помощью Speech SDK.

   ![Авторинговые потоки для пользовательских команд (Предварительный просмотр)](media/voice-assistants/custom-commands-flow.png "Пользовательские команды (Предварительный) авторский поток")

Мы предлагаем quickstarts разработан, чтобы вы работаете код менее чем за 10 минут.

* [Создание приложения пользовательских команд (Preview)](quickstart-custom-speech-commands-create-new.md)
* [Создание приложения пользовательских команд (Preview) с параметрами](quickstart-custom-speech-commands-create-parameters.md)
* [Подключение к приложению Пользовательские команды (Preview) с помощью речевого SDK, C #](quickstart-custom-speech-commands-speech-sdk.md)

Как только вы закончите с quickstarts, изучить наши как-tos.

- [Добавление валидаций к параметрам custom Command](./how-to-custom-speech-commands-validations.md)
- [Выполняй команды на клиента с помощью речи SDK](./how-to-custom-speech-commands-fulfill-sdk.md)
- [Добавление подтверждения в настраиваемую команду](./how-to-custom-speech-commands-confirmations.md)
- [Добавление одноэтапного исправления в настраиваемую команду](./how-to-custom-speech-commands-one-step-correction.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Try the Speech service for free](get-started.md) (Бесплатное использование службы "Речь")
* [Перейти к речи студии, чтобы опробовать пользовательские команды](https://speech.microsoft.com)
* [Получение пакета SDK для службы "Речь"](speech-sdk.md)
