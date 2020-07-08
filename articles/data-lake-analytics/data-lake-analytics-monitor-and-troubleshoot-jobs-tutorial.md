---
title: Мониторинг Azure Data Lake Analytics-портал Azure
description: В этой статье описано, как с помощью портала Azure устранить неполадки с заданиями Azure Data Lake Analytics.
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: b7066d81-3142-474f-8a34-32b0b39656dc
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: cab647f6102187b094806db0b17c6f781ea27484
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85564745"
---
# <a name="monitor-jobs-in-azure-data-lake-analytics-using-the-azure-portal"></a>Мониторинг заданий в Azure Data Lake Analytics с помощью портала Azure

## <a name="to-see-all-the-jobs"></a>Просмотр всех заданий

1. На портале Azure щелкните **Microsoft Azure** в левом верхнем углу.

2. Щелкните элемент с именем вашей учетной записи аналитики озера данных.  Сводные данные задания отображаются на элементе **Управление заданиями** .

   ![Аналитика озера данных Azure: управление заданиями](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)

    Элемент управления заданиями позволяет взглянуть на состояние заданий. Обратите внимание, что здесь имеется задание, завершившееся сбоем.
3. Щелкните элемент **Управление заданиями** для просмотра заданий. Задания делятся на три категории: **Выполняется**, **В очереди** и **Завершено**. Задание, завершившееся сбоем, будет отображено в разделе **Завершено** . Оно должно быть первым в списке. При наличии большого числа заданий можно щелкнуть **Фильтр** , чтобы упростить их поиск.

   ![Аналитика озера данных Azure: фильтрация заданий](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)

4. Щелкните невыполненное задание из списка, чтобы открыть сведения о задании:

   ![Аналитика озера данных Azure: невыполненное задание](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)

    Обратите внимание на кнопку **Отправить повторно** . После устранения проблемы можно отправить задание повторно.

5. Щелкните выделенную часть на предыдущем снимке экрана, чтобы открыть сведения об ошибке.  Отобразится примерно следующий текст:

   ![Аналитика озера данных Azure: сведения о невыполненном задании](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

   В нем сообщается, что исходная папка не найдена.

6. Щелкните **Дублировать скрипт**.

7. Измените путь **с** на:`/Samples/Data/SearchLog.tsv`

8. Щелкните **Отправить задание**.

## <a name="next-steps"></a>Дальнейшие шаги

* [Обзор аналитики озера данных Azure](data-lake-analytics-overview.md)
* [Начало работы с аналитикой озера данных Azure с помощью Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Управление Azure Data Lake Analytics с помощью портал Azure](data-lake-analytics-manage-use-portal.md)
