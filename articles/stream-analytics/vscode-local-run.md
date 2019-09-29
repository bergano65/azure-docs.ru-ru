---
title: Тестирование Azure Stream Analytics запросов локально с Visual Studio Code (Предварительная версия)
description: В этой статье описывается, как проверить запросы локально с помощью средств Azure Stream Analytics для Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: 751cdf50fccc654dfab06b4d18428531312d08e6
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673026"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio-code"></a>Тестирование Stream Analytics запросов локально с Visual Studio Code

Вы можете использовать средства Azure Stream Analytics для Visual Studio Code для локального тестирования заданий Stream Analytics с помощью примеров данных.

Используйте это [Краткое руководство](quick-create-vs-code.md) , чтобы узнать, как создать задание Stream Analytics с помощью Visual Studio Code.

## <a name="prerequisites"></a>Предварительные требования
* Установите [пакет SDK для .NET Core](https://dotnet.microsoft.com/download).
* Перезапустите Visual Studio Code.
 
## <a name="run-queries-locally"></a>Локальное выполнение запросов

Вы можете использовать расширение Azure Stream Analytics для Visual Studio Code, чтобы локально проверить задания Stream Analytics с помощью демонстрационных данных.

1. После создания задания Stream Analytics используйте **сочетание клавиш CTRL + SHIFT + P** , чтобы открыть палитру команд. Затем введите и выберите **ASA: добавить входные данные**.

    ![Добавление входных данных ASA в Visual Studio Code](./media/vscode-local-run/add-input.png)

2. Выберите **локальный вход**.

    ![Добавление местных входных данных ASA в Visual Studio Code](./media/vscode-local-run/add-local-input.png)

3. Выберите **+ новый локальный вход**.

    ![Добавление нового локального входа ASA в Visual Studio Code](./media/vscode-local-run/add-new-local-input.png)

4. Введите тот же псевдоним ввода, который использовался в запросе.

    ![Добавление нового локального псевдонима ASA](./media/vscode-local-run/new-local-input-alias.png)

5. В файле **LocalInput_DefaultLocalStream. JSON** введите путь к файлу, в котором находится локальный файл данных.

    ![Введите путь к локальному файлу в Visual Studio](./media/vscode-local-run/local-file-path.png)

6. Вернитесь в редактор запросов и выберите **запустить локально**.

    ![Выберите выполнить локально в редакторе запросов.](./media/vscode-local-run/run-locally.png)

## <a name="next-steps"></a>Следующие шаги

* [Создание Azure Stream Analytics облачного задания в Visual Studio Code (Предварительная версия)](quick-create-vs-code.md)

* [Просмотр Azure Stream Analytics заданий с помощью Visual Studio Code (Предварительная версия)](vscode-explore-jobs.md)
