---
title: Копирование или резервное копирование заданий Azure Stream Analytics
description: В этой статье описывается, как скопировать или создать резервную копию задания Azure Stream Analytics.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 5c8f770855dd8d19a9d313f1b79f9bf8da4b2393
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75771501"
---
# <a name="copy-or-back-up-azure-stream-analytics-jobs"></a>Копирование или резервное копирование заданий Azure Stream Analytics

Вы можете скопировать или создать резервную копию развернутых заданий Azure Stream Analytics с помощью Visual Studio Code или Visual Studio. 

## <a name="before-you-begin"></a>Перед началом
* Если у вас нет подписки На Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/).

* Войдите на [портал Azure](https://portal.azure.com/).

* Установка [расширения Azure Stream Analytics для Visual Studio Code](quick-create-vs-code.md#install-the-azure-stream-analytics-tools-extension) или [инструментов Azure Stream Analytics для Visual Studio.](quick-create-vs-code.md#install-the-azure-stream-analytics-tools-extension)  

## <a name="visual-studio-code"></a>Visual Studio Code

1. Нажмите значок **Azure** в панели действий Visual Studio, а затем расширьте узел **Stream Analytics.** Ваши рабочие места должны отображаться под подпиской.

   ![Исследователь аналитики open Stream](./media/vscode-explore-jobs/open-explorer.png)

2. Чтобы экспортировать работу в местный проект, найдите задание, которую вы хотите экспортировать в **Stream Analytics Explorer** в Visual Studio Code. Затем выберите папку для проекта.

    ![Экспорт ASA работу в визуальный код студии](./media/vscode-explore-jobs/export-job.png)

    Проект экспортируется в выбранную папку и добавляется в текущее рабочее пространство.

    ![Экспорт ASA работу в визуальный код студии](./media/stream-analytics-manage-job/copy-backup-stream-analytics-jobs.png)

3. Чтобы опубликовать задание в другой регион или резервное копирование с помощью другого\*имени, **выберите из подписки, чтобы опубликовать** в редакторе запроса (.asaql) и следуйте инструкциям.

    ![Публикация в Azure в визуальном коде студии](./media/quick-create-vs-code/submit-job.png)

## <a name="visual-studio"></a>Visual Studio

1. Следите за [выполнением работ по развертыванию задания Azure Stream Analytics в инструкции по проекту.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-vs-tools#export-jobs-to-a-project)

2. Откройте \*файл .asaql в редакторе запроса, выберите «Отправить в **Azure»** в редакторе скрипта и следуйте инструкциям по публикации задания в другой регион или резервному копированию с помощью нового имени.

## <a name="next-steps"></a>Дальнейшие действия

* [Быстрый запуск: Создайте работу Stream Analytics с помощью visual Studio Code](quick-create-vs-code.md)
* [Быстрый запуск: Создайте работу Stream Analytics с помощью Visual Studio](stream-analytics-quick-create-vs.md)
* [Развертывание заданий Azure Stream Analytics с помощью CI/CD и Azure Pipelines](stream-analytics-tools-visual-studio-cicd-vsts.md)
