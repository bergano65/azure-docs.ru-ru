---
title: Знакомство с Azure Stream Analytics с помощью Visual Studio Code (Предварительная версия)
description: В этой статье показано, как экспортировать задание Azure Stream Analytics в локальном проекте, перечисление заданий и просмотреть задания сущности и настройка конвейера CI/CD для задания Stream Analytics.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/06/2019
ms.topic: conceptual
ms.openlocfilehash: 640a81fcb94194e2e907e9339f016bd35e279fdd
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65079215"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>Знакомство с Azure Stream Analytics с помощью Visual Studio Code (Предварительная версия)

Azure Stream Analytics для расширения Visual Studio Code дает разработчикам облегченным для управления их заданий Stream Analytics. С помощью расширения Azure Stream Analytics вы можете:

- [Создание](quick-create-vs-code.md), запуск и остановка заданий
- Экспортировать существующие задания для локального проекта
- Выполнение запросов локально
- Настройка конвейера CI/CD

## <a name="export-a-job-to-a-local-project"></a>Экспортировать задание для локального проекта

Чтобы экспортировать задание локального проекта, найдите задание, необходимо экспортировать в **обозревателе Stream Analytics** в Visual Studio code. Выберите папку для проекта. Проект будет экспортирован в выбранной папки, и вы можете управлять заданием из Visual Studio Code. Дополнительные сведения об использовании Visual Studio Code для управления заданиями Stream Analytics, см. в разделе Visual Studio Code [быстрого запуска](quick-create-vs-code.md).

![Экспорт задания ASA в Visual Studio Code](./media/vs-code-how-to/export-job.png)

## <a name="run-queries-locally"></a>Выполнение запросов локально

Расширение Azure Stream Analytics для Visual Studio Code можно использовать для тестирования заданий Stream Analytics локально с образцами данных.

1. Когда вы создали свое задание Stream Analytics, используйте **Ctrl + Shift + P** чтобы открыть палитру команд. Затем введите и выберите **ASA: Добавление входных данных**.

    ![Добавить входные данные ASA в Visual Studio code](./media/vs-code-how-to/add-input.png)

2. Выберите **локальные входные данные**.

    ![Добавить локальные входные данные ASA в Visual Studio code](./media/vs-code-how-to/add-local-input.png)

3. Выберите **+ новое входное значение локального**.

    ![Добавить новый ASA локальных входных данных в Visual Studio code](./media/vs-code-how-to/add-new-local-input.png)

4. Введите тот же входной псевдоним, используемый в запросе.

    ![Добавьте новый локальный входной псевдоним ASA](./media/vs-code-how-to/new-local-input-alias.png)

5. В **LocalInput_DefaultLocalStream.json** файл, введите путь к файлу, где находится ваш локальный файл данных.

    ![Введите локальный путь к файлу в Visual Studio](./media/vs-code-how-to/local-file-path.png)

6. Вернитесь к редактору запросов и выберите **локального запуска**.

    ![Выберите запуска локально в редакторе запросов](./media/vs-code-how-to/run-locally.png)

## <a name="set-up-a-cicd-pipeline"></a>Настройка конвейера CI/CD

Вы можете включить непрерывную интеграцию и развертывание для заданий Azure Stream Analytics, с помощью **asa cicd средства** пакета NPM. Пакет NPM предоставляет средства для автоматического развертывания проектов Stream Analytics Visual Studio Code. Его можно использовать в Windows, macOS и Linux без установки Visual Studio Code.

При наличии [пакет](https://usqldownload.blob.core.windows.net/ext/asa/asa-cicd-0.0.1-preview-beta.tar), используйте следующую команду для вывода шаблонов Azure Resource Manager. Если **outputPath** не указан, шаблоны будут помещены в **развернуть** папку к проекту **bin** папки.

```powershell
asa-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```

## <a name="next-steps"></a>Дальнейшие действия

* [Создать задание Azure Stream Analytics cloud в Visual Studio Code (Предварительная версия)](quick-create-vs-code.md)