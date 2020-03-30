---
title: Протестируйте работу Azure Stream Analytics локально с помощью выборочных данных с помощью Visual Studio Code
description: В этой статье описывается, как тестировать запросы локально с помощью выборочных данных с помощью инструментов аналитики Azure Stream для визуального кода Studio.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 11/10/2019
ms.topic: conceptual
ms.openlocfilehash: c29d0d9ecd856ee9611df21d23b1b2b763e24652
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75486473"
---
# <a name="test-stream-analytics-queries-locally-with-sample-data-using-visual-studio-code"></a>Тестовая stream Analytics запрашивает локально с выборочными данными с помощью Visual Studio Code

Для проверки заданий Stream Analytics на месте с помощью выборочных данных можно использовать инструменты Azure Stream Analytics для Visual Studio Code. Результаты запроса можно найти в файлах JSON в папке **LocalRunOutputs** вашего проекта.

## <a name="prerequisites"></a>Предварительные требования

* Установите [ядро SDK .NET](https://dotnet.microsoft.com/download) и перезапустите Visual Studio Code.

* Используйте этот [быстрый запуск,](quick-create-vs-code.md) чтобы узнать, как создать работу Stream Analytics с помощью Visual Studio Code.

## <a name="prepare-sample-data"></a>Подготовка примера данных

Сначала необходимо подготовить файлы входных данных образца. Если у вас уже есть некоторые примеры файлов данных на вашей машине, вы можете пропустить этот шаг и перейти к следующему.

1. Нажмите **Предварительные данные** в файле конфигурации ввода с верхней строки. Некоторые входные данные будут получены из концентратора IoT и отображаются в окне предварительного просмотра. Обратите внимание, что это может занять некоторое время.

2. После отображаемых данных нажмите **«Сохранить» для** сохранения данных в локальном файле.

 ![Просмотр динамических входных данных](./media/quick-create-vs-code/preview-live-input.png)

## <a name="define-a-local-input"></a>Определение локального ввода

1. Нажмите **на input.json** под папку Ввода в проекте Stream Analytics. Затем выберите **Добавить локальный вход** из верхней строки.

    ![Добавление локального ввода из проекта](./media/quick-create-vs-code/add-input-from-project.png)

    Вы также можете использовать **Ctrl-Shift-P,** чтобы открыть командную палитру и ввести **ASA: Добавить вход**.

   ![Добавление входных данных Stream Analytics в VS Code](./media/quick-create-vs-code/add-input.png)

2. Выберите **локальный вход**.

    ![Добавление локального ввода ASA в код Visual Studio](./media/vscode-local-run/add-local-input.png)

3. Выберите **новый локальный вход**.

    ![Добавление нового локального ввода ASA в код Visual Studio](./media/vscode-local-run/add-new-local-input.png)

4. Введите тот же псевдоним ввода, что и в запросе.

    ![Добавление нового локального псевдонима ASA](./media/vscode-local-run/new-local-input-alias.png)

5. В недавно созданном файле **LocalInput_Input.json** введите путь файла, где находится ваш локальный файл данных.

    ![Введите локальный путь файла в Visual Studio](./media/vscode-local-run/local-file-path.png)

6. Выберите **данные предварительного просмотра** для предварительного просмотра входных данных. Тип сериализации для ваших данных автоматически обнаруживается, если его JSON или CSV. Используйте селектор для просмотра данных в **формате Таблица** или **Сырье.** Следующая таблица является примером данных в **формате таблицы:**

     ![Предварительный просмотр локальных данных в формате таблицы](./media/vscode-local-run/local-file-preview-table.png)

    Следующая таблица является примером данных в **формате Raw:**

    ![Предварительный просмотр локальных данных в сыром формате](./media/vscode-local-run/local-file-preview-raw.png)

## <a name="run-queries-locally"></a>Локальное выполнение запросов.

Вернитесь к редактору запроса и выберите **Выполнить локально.** Затем выберите **Используйте локальные входные данные** из списка выпадающих данных.

![Выберите запуск локально в редакторе запроса](./media/vscode-local-run/run-locally.png)

![Использование локального ввода](./media/vscode-local-run/run-locally-use-local-input.png)

Результат отображается в нужном окне. Вы можете нажать **Run,** чтобы проверить еще раз. Вы также можете выбрать **Open в папке,** чтобы увидеть файлы результата в исследователи файлов и далее открыть их с помощью других инструментов. Обратите внимание, что файлы результатов доступны только в формате JSON.

![Просмотр результатов локального запуска](./media/vscode-local-run/run-locally-result.png)

## <a name="next-steps"></a>Дальнейшие действия

* [Тестирование Лазурный поток Analytics рабочих мест локально с живым вхотвочонным использованием Visual Studio code](visual-studio-code-local-run-live-input.md)

* [Исследуйте вакансии Azure Stream Analytics с помощью визуального кода студии (Предварительный просмотр)](visual-studio-code-explore-jobs.md)
