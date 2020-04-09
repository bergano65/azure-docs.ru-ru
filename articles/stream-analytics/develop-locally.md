---
title: Разработка и отладка заданий Azure Stream Analytics локально
description: Узнайте, как разрабатывать и тестировать задания Azure Stream Analytics на локальном компьютере перед запуском на портале Azure.
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 03/31/2020
ms.service: stream-analytics
ms.openlocfilehash: 736fce1d4b347e36ad5c10ca89ad0627104a0232
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879847"
---
# <a name="develop-and-debug-azure-stream-analytics-jobs-locally"></a>Разработка и отладка заданий Azure Stream Analytics локально

В то время как вы можете создавать и тестировать вакансии Azure Stream Analytics на портале Azure, многие разработчики предпочитают локальный опыт разработки. Stream Analytics упрощает использование любимого редактора кода и средств разработки для создания и тестирования заданий с живыми потоками событий из Azure Event Hub, IoT Hub и Blob Storage с помощью полностью функционирующего локального времени выполнения одного узла. Вы также можете отправлять задания в Azure непосредственно из локальной среды разработки.

## <a name="local-development-environments"></a>Локальные среды разработки

Способ разработки заданий Stream Analytics на локальном компьютере зависит от ваших предпочтений инструментария и доступности функций. Просмотрите [сравнение функций Azure Stream Analytics,](feature-comparison.md) чтобы узнать, какие функции поддерживаются для каждой среды разработки.

В нижеприведенной таблице указаны среды, которые поддерживают локальную разработку:

|Среда                              |Описание    |
|-----------------------------------------|------------|
|[Visual Studio Code](visual-studio-code-explore-jobs.md)| [Расширение Azure Stream Analytics Tools](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa) для Visual Studio Code позволяет авторизировать, управлять и тестировать работу аналитики Stream как локально, так и в облаке с богатым Интеллектом и управлением исходным кодом. Поддерживает разработку Linux, MacOS и Windows. Чтобы узнать больше, смотрите [создание работы по аналитике потоков Azure в Visual Studio Code.](quick-create-vs-code.md)|
|[Visual Studio 2019](stream-analytics-tools-for-visual-studio-install.md) |Stream Analytics Tools является частью разработки Azure и работы по хранению и обработке данных в Visual Studio. Вы можете использовать Visual Studio для написания пользовательских функций, определяемых пользователями, и десериализаторов. Чтобы узнать больше, [см.](stream-analytics-quick-create-vs.md)|
|[Командная строка или терминал](stream-analytics-tools-for-visual-studio-cicd.md)|Пакет Azure Stream Analytics CI/CD NuGet предоставляет инструменты для создания проекта Visual Studio, локального тестирования на произвольной машине. Пакет Azure Stream Analytics CI/CD npm предоставляет инструменты для создания проектов Visual Studio Code (который генерирует шаблон менеджера ресурсов Azure) на произвольной машине.|

## <a name="next-steps"></a>Дальнейшие действия

* [Тестовая stream Analytics запрашивает локально с выборочными данными с помощью Visual Studio Code](visual-studio-code-local-run.md)
* [Тестовая аналитика потоков локально запрашивает против ввода потоковой передачи с помощью Visual Studio Code](visual-studio-code-local-run-live-input.md)
* [Локальное тестирование запросов Stream Analytics с помощью Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Тестирование реальных данных в локальной среде с помощью инструментов Azure Stream Analytics для Visual Studio (предварительная версия)](stream-analytics-live-data-local-testing.md)
