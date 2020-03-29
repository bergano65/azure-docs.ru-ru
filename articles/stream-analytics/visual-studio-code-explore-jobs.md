---
title: Исследуйте вакансии Azure Stream Analytics в коде Visual Studio
description: В этой статье показано, как экспортировать задание Azure Stream Analytics в локальный проект, перечислять задания и просматривать объекты заданий.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: 1d3a02d3778f9b4113767c5f755d675aeadd901b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75479245"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>Исследуйте аналитику потоков Azure с помощью визуального кода студии (Предварительный просмотр)

Azure Stream Analytics для расширения Visual Studio Code предоставляет разработчикам легкий опыт управления своими рабочими местами в Stream Analytics. Он может быть использован на Windows, Mac и Linux. С расширением Azure Stream Analytics можно:

- [Создание,](quick-create-vs-code.md)запуск и остановка заданий
- Экспорт существующих рабочих мест в местный проект
- Перечислите задания и просмотр объектов заданий

## <a name="export-a-job-to-a-local-project"></a>Экспорт работы в местный проект

Чтобы экспортировать работу в местный проект, найдите задание, которую вы хотите экспортировать в **Stream Analytics Explorer** в Visual Studio Code. Затем выберите папку для проекта. Проект экспортируется в выбранную папку, и вы можете продолжать управлять заданием из Visual Studio Code. Для получения дополнительной информации об использовании Visual Studio Code [quickstart](quick-create-vs-code.md)для управления рабочими местами Stream Analytics см.

![Экспорт ASA работу в визуальный код студии](./media/vscode-explore-jobs/export-job.png)

## <a name="list-job-and-view-job-entities"></a>Перечислите должности и просмотреть объекты задания

С помощью представления задания можно взаимодействовать с заданиями Azure Stream Analytics из Visual Studio.


1. Нажмите значок **Azure** на панели действий Visual Studio Code, а затем расширьте **узел Stream Analytics.** Ваши рабочие места должны отображаться под подпиской.

   ![Исследователь аналитики open Stream](./media/vscode-explore-jobs/open-explorer.png)

2. Расширяйте свой рабочий узла, вы можете открывать и просматривать запрос задания, конфигурацию, входы, выходы и функции. 

3. Нажмите вправо на узел работы и выберите **открытый вид на работу в узеле Portal,** чтобы открыть представление о работе на портале Azure.

   ![Открытое представление о вакансиях на портале](./media/vscode-explore-jobs/open-job-view.png)

## <a name="next-steps"></a>Дальнейшие действия

* [Создание облачного задания Azure Stream Analytics в Visual Studio Code (предварительная версия)](quick-create-vs-code.md)
