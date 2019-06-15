---
title: Просмотр заданий Azure Stream Analytics с помощью Visual Studio Code (Предварительная версия)
description: В этой статье показано, как экспортировать задание Azure Stream Analytics для локального проекта, список заданий и просмотреть задания сущности.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: 8674d478646c8f9be6b32521c6624752ac6df052
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65827805"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>Знакомство с Azure Stream Analytics с помощью Visual Studio Code (Предварительная версия)

Azure Stream Analytics для расширения Visual Studio Code дает разработчикам облегченным для управления их заданий Stream Analytics. Он может использоваться в Windows, Mac и Linux. С помощью расширения Azure Stream Analytics вы можете:

- [Создание](quick-create-vs-code.md), запуск и остановка заданий
- Экспортировать существующие задания для локального проекта
- Получение списка заданий и просмотр объектов задания

## <a name="export-a-job-to-a-local-project"></a>Экспортировать задание для локального проекта

Чтобы экспортировать задание локального проекта, найдите задание, необходимо экспортировать в **обозревателе Stream Analytics** в Visual Studio Code. Выберите папку для проекта. Проект будет экспортирован в выбранной папки, и вы можете управлять заданием из Visual Studio Code. Дополнительные сведения об использовании Visual Studio Code для управления заданиями Stream Analytics, см. в разделе Visual Studio Code [быстрого запуска](quick-create-vs-code.md).

![Экспорт задания ASA в Visual Studio Code](./media/vscode-explore-jobs/export-job.png)

## <a name="list-job-and-view-job-entities"></a>Список заданий и просмотр объектов задания

С помощью представления задания можно взаимодействовать с заданиями Azure Stream Analytics из Visual Studio.


1. Нажмите кнопку **Azure** значок на панели действия кода для Visual Studio и раскройте **узел Stream Analytics**. Заданий должна появиться в своих подписках.

   ![Explorer откройте Stream Analytics](./media/vscode-explore-jobs/open-explorer.png)

2. Разверните узел задания, можно открыть и просмотреть запрос задания, конфигурацию, входные данные, выходные данные и функции. 

3. Щелкните правой кнопкой мыши узел задания и выберите **открыть представление заданий на портале** узел, чтобы открыть представление задания на портале Azure.

   ![Открытие представления задания на портале](./media/vscode-explore-jobs/open-job-view.png)

## <a name="next-steps"></a>Дальнейшие действия

* [Создать задание Azure Stream Analytics cloud в Visual Studio Code (Предварительная версия)](quick-create-vs-code.md)
