---
title: Тестирование запросов Azure Stream Analytics локально с помощью Visual Studio Code (Предварительная версия)
description: В этой статье описывается, как выполнить проверку запроса локально при инструменты Azure Stream Analytics для Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: f477a0f99c3eaa82568d8188bfaae03818fb72dc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65827955"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio-code"></a>Тестирование запросов Stream Analytics локально с помощью Visual Studio Code

Инструменты Azure Stream Analytics для Visual Studio Code можно использовать для тестирования заданий Stream Analytics локально с образцами данных.

Используйте этот [быстрого запуска](quick-create-vs-code.md) чтобы узнать, как создать задание Stream Analytics с помощью Visual Studio Code.

## <a name="run-queries-locally"></a>Выполнение запросов локально

Расширение Azure Stream Analytics для Visual Studio Code можно использовать для тестирования заданий Stream Analytics локально с образцами данных.

1. Когда вы создали свое задание Stream Analytics, используйте **Ctrl + Shift + P** чтобы открыть палитру команд. Затем введите и выберите **ASA: добавить входные данные**.

    ![Добавить входные данные ASA в Visual Studio code](./media/vscode-local-run/add-input.png)

2. Выберите **локальные входные данные**.

    ![Добавить локальные входные данные ASA в Visual Studio code](./media/vscode-local-run/add-local-input.png)

3. Выберите **+ новое входное значение локального**.

    ![Добавить новый ASA локальных входных данных в Visual Studio code](./media/vscode-local-run/add-new-local-input.png)

4. Введите тот же входной псевдоним, используемый в запросе.

    ![Добавьте новый локальный входной псевдоним ASA](./media/vscode-local-run/new-local-input-alias.png)

5. В **LocalInput_DefaultLocalStream.json** файл, введите путь к файлу, где находится ваш локальный файл данных.

    ![Введите локальный путь к файлу в Visual Studio](./media/vscode-local-run/local-file-path.png)

6. Вернитесь к редактору запросов и выберите **локального запуска**.

    ![Выберите запуска локально в редакторе запросов](./media/vscode-local-run/run-locally.png)

## <a name="next-steps"></a>Дальнейшие действия

* [Создать задание Azure Stream Analytics cloud в Visual Studio Code (Предварительная версия)](quick-create-vs-code.md)

* [Просмотр заданий Azure Stream Analytics с помощью Visual Studio Code (Предварительная версия)](vscode-explore-jobs.md)
