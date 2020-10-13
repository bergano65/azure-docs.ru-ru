---
title: Просмотр Azure Stream Analytics заданий в Visual Studio Code
description: В этой статье показано, как экспортировать задание Azure Stream Analytics в локальный проект, вывести список заданий и просмотреть объекты заданий.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/15/2019
ms.topic: how-to
ms.openlocfilehash: 4b4ddd7db4891c642ec2002bbe992314fe67a20f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90891977"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>Изучение Azure Stream Analytics с Visual Studio Code (Предварительная версия)

Azure Stream Analytics для расширения Visual Studio Code предоставляет разработчикам упрощенный интерфейс для управления заданиями Stream Analytics. Его можно использовать в Windows, Mac и Linux. С помощью расширения Azure Stream Analytics можно:

- [Создание](quick-create-visual-studio-code.md), запуск и завершение заданий
- Экспорт существующих заданий в локальный проект
- Вывод списка заданий и Просмотр сущностей заданий

## <a name="export-a-job-to-a-local-project"></a>Экспорт задания в локальный проект

Чтобы экспортировать задание в локальный проект, в Visual Studio Code в **обозревателе Stream Analytics** выберите задание, которое необходимо экспортировать. Затем выберите папку для проекта. Проект экспортируется в выбранную папку, и вы можете продолжить Управление заданием из Visual Studio Code. Дополнительные сведения об использовании Visual Studio Code для управления заданиями Stream Analytics см. в [руководстве](quick-create-visual-studio-code.md)по Visual Studio Code.

![Экспорт задания ASA в Visual Studio Code](./media/vscode-explore-jobs/export-job.png)

## <a name="list-job-and-view-job-entities"></a>Составление списка и Просмотр сущностей заданий

С помощью представления задания можно взаимодействовать с заданиями Azure Stream Analytics из Visual Studio.


1. Щелкните значок **Azure** на панели действий Visual Studio Code а затем разверните **узел Stream Analytics**. Задания должны отображаться в подписках.

   ![Открыть обозреватель Stream Analytics](./media/vscode-explore-jobs/open-explorer.png)

2. Разверните узел задания, можно открыть и просмотреть запрос задания, конфигурацию, входные данные, выходы и функции. 

3. Щелкните правой кнопкой мыши узел задания и выберите в узле **портала пункт Открыть представление задания** , чтобы открыть представление задания в портал Azure.

   ![Открытие представления заданий на портале](./media/vscode-explore-jobs/open-job-view.png)

## <a name="next-steps"></a>Дальнейшие шаги

* [Создание облачного задания Azure Stream Analytics в Visual Studio Code (предварительная версия)](quick-create-visual-studio-code.md)
