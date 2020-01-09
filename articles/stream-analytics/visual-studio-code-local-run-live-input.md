---
title: Тестирование Azure Stream Analytics запросов локально для входа в динамический поток с помощью Visual Studio Code
description: В этой статье описывается, как выполнить локальное тестирование запросов для входных данных в динамическом потоке с помощью средств Azure Stream Analytics для Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 11/14/2019
ms.topic: conceptual
ms.openlocfilehash: 34ce91a1385f951847abeedd3a6b526d3a07af35
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660857"
---
# <a name="test-stream-analytics-queries-locally-against-live-stream-input-by-using-visual-studio-code"></a>Тестирование Stream Analytics запросов локально для входа в динамический поток с помощью Visual Studio Code

Azure Stream Analytics инструменты можно использовать для Visual Studio Code для локального тестирования заданий Stream Analytics в потоке ввода в режиме реального времени. Входные данные могут поступать из источника, такого как концентраторы событий Azure или центр Интернета вещей Azure. Выходные результаты отправляются в виде JSON-файлов в папку в проекте с именем **локалрунаутпутс**.

## <a name="prerequisites"></a>Технические условия

* Установите [пакет SDK для .NET Core](https://dotnet.microsoft.com/download) и перезапустите Visual Studio Code.

* Используйте [это краткое руководство](quick-create-vs-code.md) , чтобы узнать, как создать Stream Analytics задание с помощью Visual Studio Code.

## <a name="define-a-live-stream-input"></a>Определение входного потока в реальном времени

1. Щелкните правой кнопкой мыши папку **входные данные** в проекте Stream Analytics. Затем выберите **ASA: Добавить входные данные** из контекстного меню.

   ![Добавление входных данных из папки входных данных](./media/quick-create-vs-code/add-input-from-inputs-folder.png)

   Можно также нажать **клавиши CTRL + SHIFT + P** , чтобы открыть палитру команд, и ввести **ASA: Add input**.

   ![Добавление Stream Analytics входных данных в Visual Studio Code](./media/quick-create-vs-code/add-input.png)

2. Выберите тип источника входных данных из раскрывающегося списка.

   ![Выберите центр Интернета вещей в качестве параметра ввода](./media/quick-create-vs-code/iot-hub.png)

3. Если вы добавили входные данные из палитры команд, выберите Stream Analytics сценарий запроса, который будет использовать входные данные. Он должен автоматически заполняться путем к файлу **мясапрож. asaql**.

   ![Выберите Stream Analytics сценарий в Visual Studio Code](./media/quick-create-vs-code/asa-script.png)

4. В раскрывающемся меню выберите **выбрать из подписок Azure** .

    ![Выбрать из подписок](./media/quick-create-vs-code/add-input-select-subscription.png)

5. Настройте созданный JSON файл. Функцию CodeLens можно использовать для ввода строки, выбора из раскрывающегося списка или изменения текста непосредственно в файле. На следующем снимке экрана показан пример **выбора из подписок** в качестве примера.

   ![Настройка входного файла в Visual Studio Code](./media/quick-create-vs-code/configure-input.png)

## <a name="preview-input"></a>Предварительный просмотр входных данных

Чтобы убедиться, что входные данные получены, выберите **Предварительный просмотр данных** в динамическом входном файле конфигурации в верхней строке. Некоторые входные данные поступают из центра Интернета вещей и отображаются в окне предварительного просмотра. Просмотр может занять несколько секунд.

 ![Предварительный просмотр динамического ввода](./media/quick-create-vs-code/preview-live-input.png)

## <a name="run-queries-locally"></a>Локальное выполнение запросов

Вернитесь в редактор запросов и выберите **запустить локально**. Затем выберите **использовать динамический вход** из раскрывающегося списка.

![Выберите "выполнить локально" в редакторе запросов.](./media/vscode-local-run/run-locally.png)

![Выберите "использовать Live input"](./media/vscode-local-run-live-input/run-locally-use-live-input.png)

Результат отображается в правом окне и обновляется каждые 3 секунды. Можно выбрать **Запуск** для повторного тестирования. Можно также выбрать пункт **Открыть в папке** , чтобы просмотреть файлы результатов в проводнике и открыть их с помощью Visual Studio Code или такого средства, как Excel. Обратите внимание, что файлы результатов доступны только в формате JSON.

По умолчанию задано время начала создания выходных данных для задания, равное **Now**. Время можно настроить, нажав кнопку **время начала вывода** в окне результатов.

![Просмотреть результат локального запуска](./media/vscode-local-run-live-input/vscode-livetesting.gif)

## <a name="next-steps"></a>Дальнейшие действия

* [Просмотр Azure Stream Analytics заданий с помощью Visual Studio Code (Предварительная версия)](visual-studio-code-explore-jobs.md)

* [Настройка конвейеров CI/CD с помощью пакета NPM](setup-cicd-vs-code.md)
