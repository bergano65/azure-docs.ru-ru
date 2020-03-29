---
title: ТестИруйте запросы Azure Stream Analytics локально против ввода потоковой передачи с помощью Visual Studio Code
description: В этой статье описывается, как тестировать запросы локально против ввода потоковой передачи с помощью инструментов аналитики Azure Stream для Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 11/14/2019
ms.topic: conceptual
ms.openlocfilehash: 34ce91a1385f951847abeedd3a6b526d3a07af35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75660857"
---
# <a name="test-stream-analytics-queries-locally-against-live-stream-input-by-using-visual-studio-code"></a>Тестовая аналитика потоков локально запрашивает против ввода потоковой передачи с помощью Visual Studio Code

Инструменты аналитики Azure Stream Analytics для visual Studio Code можно использовать для проверки заданий Stream Analytics локально на уровне ввода в прямом эфире. Вход может поступать из источника, например концентраторов событий Azure или Azure IoT Hub. Результаты вывода отправляются в виде файлов JSON в папку проекта **localRunOutputs.**

## <a name="prerequisites"></a>Предварительные требования

* Установите [.NET Core SDK](https://dotnet.microsoft.com/download) и перезапустите visual Studio Code.

* Используйте [этот быстрый запуск,](quick-create-vs-code.md) чтобы узнать, как создать работу Stream Analytics с помощью Visual Studio Code.

## <a name="define-a-live-stream-input"></a>Определение ввода потоковой передачи в реальном времени

1. Щелкните правой кнопкой мыши папку **Inputs** в проекте Stream Analytics. Затем выберите **ASA: Добавьте вход** ные данные из контекстного меню.

   ![Добавление входных данных из папки Inputs](./media/quick-create-vs-code/add-input-from-inputs-folder.png)

   Вы также можете выбрать **Ctrl-Shift-P,** чтобы открыть командную палитру и ввести **ASA: Добавить вход**.

   ![Добавление входа Stream Analytics в Visual Studio Code](./media/quick-create-vs-code/add-input.png)

2. Выберите тип исходного кода ввода из списка выпадающих.

   ![Выбор центра Интернета вещей в качестве источника входных данных](./media/quick-create-vs-code/iot-hub.png)

3. Если вы добавили источник входных данных с помощью палитры команд, выберите скрипт запроса Stream Analytics, который будет использовать этот источник. Здесь будет автоматически указан путь к файлу **myASAproj.asaql**.

   ![Выбор скрипта Stream Analytics в Visual Studio Code](./media/quick-create-vs-code/asa-script.png)

4. Щелкните **Выбрать подписку Azure** в раскрывающемся списке.

    ![Выбор подписки в списке](./media/quick-create-vs-code/add-input-select-subscription.png)

5. Настройка вновь созданного файла JSON. Функция CodeLens помогает вводить строки, выбирать значения в раскрывающихся списках и редактировать текст непосредственно в файле. На следующем снимке экрана показано, как **выбрать подписку в списке**.

   ![Настройка входного файла в Visual Studio Code](./media/quick-create-vs-code/configure-input.png)

## <a name="preview-input"></a>Предварительный просмотр входных данных

Чтобы убедиться, что данные ввода поступают, выберите **данные предварительного просмотра** в файле конфигурации ввода в реальном времени из верхней строки. Некоторые входные данные поступают из концентратора IoT и отображаться в окне предварительного просмотра. Появление предварительного просмотра может занять несколько секунд.

 ![Просмотр динамических входных данных](./media/quick-create-vs-code/preview-live-input.png)

## <a name="run-queries-locally"></a>Локальное выполнение запросов.

Вернитесь к редактору запроса и выберите **Выполнить локально.** Затем выберите **«Используйте live Input»** из списка выпадающих.

![Выберите "Запуск локально" в редакторе запроса](./media/vscode-local-run/run-locally.png)

![Выберите "Использование живого ввода"](./media/vscode-local-run-live-input/run-locally-use-live-input.png)

Результат отображается в нужном окне и обновляется каждые 3 секунды. Вы можете выбрать **Run** для повторного тестирования. Вы также можете выбрать **Open в папке,** чтобы увидеть файлы результатов в File Explorer и открыть их с помощью Visual Studio Code или такого инструмента, как Excel. Обратите внимание, что файлы результатов доступны только в формате JSON.

Время выполнения работы по умолчанию устанавливается **сейчас.** Вы можете настроить время, выбрав кнопку **времени начала вывода** в окне результата.

![Просмотр результатов локального запуска](./media/vscode-local-run-live-input/vscode-livetesting.gif)

## <a name="next-steps"></a>Дальнейшие действия

* [Исследуйте вакансии Azure Stream Analytics с помощью visual Studio Code (предварительный просмотр)](visual-studio-code-explore-jobs.md)

* [Настройка конвейеров CI/CD с использованием пакета npm](setup-cicd-vs-code.md)
