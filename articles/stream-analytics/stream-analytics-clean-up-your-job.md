---
title: Очистка задания Azure Stream Analytics
description: В этой статье показаны различные методы удаления заданий Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: d99920417f20034da1001a821c02376ac19274d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426489"
---
# <a name="stop-or-delete-your-azure-stream-analytics-job"></a>Остановить или удалить задание Azure Stream Analytics

Задания Azure Stream Analytics можно легко остановить или удалить через портал Azure, Azure PowerShell, Azure SDK для .Net или REST API. Задание Stream Analytics не может быть восстановлено после его удаления.

>[!NOTE] 
>При остановке задания Stream Analytics данные сохраняются в хранилище входных и выходных данных, например Центрах событий или Базе данных SQL Azure. Если необходимо удалить данные из Azure, обязательно следуйте процедуре удаления входящих и исходящих ресурсов задания Stream Analytics.

## <a name="stop-a-job-in-azure-portal"></a>Остановка задания на портале Azure

Когда вы останавливаете работу, ресурсы дерезервируются и он останавливает обработку событий. Сборы, связанные с этой работой, также прекращаются. Однако вся конфигурация сохранена, и вы можете перезапустить работу позже 

1. Войдите на [портал Azure](https://portal.azure.com). 

2. Найдите выполняющееся задание Stream Analytics и выберите его.

3. На странице задания Stream Analytics щелкните **Остановить**, чтобы остановить задание. 

   ![Остановка задания Azure Stream Analytics](./media/stream-analytics-clean-up-your-job/stop-stream-analytics-job.png)


## <a name="delete-a-job-in-azure-portal"></a>Удаление задания на портале Azure

>[!WARNING] 
>Задание Stream Analytics не может быть восстановлено после его удаления.

1. Войдите на портал Azure. 

2. Найдите имеющееся задание Stream Analytics и выберите его.

3. На странице задания Stream Analytics щелкните **Удалить**, чтобы удалить задание. 

   ![Удаление задания Azure Stream Analytics](./media/stream-analytics-clean-up-your-job/delete-stream-analytics-job.png)


## <a name="stop-or-delete-a-job-using-powershell"></a>Остановка или удаление задания с помощью PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Чтобы остановить работу с помощью PowerShell, используйте смдлет [Stop-AzStreamAnalyticsJob.](https://docs.microsoft.com/powershell/module/az.streamanalytics/stop-azstreamanalyticsjob) Чтобы удалить задание с помощью PowerShell, используйте cmdlet [Remove-AzStreamAnalyticsJob.](https://docs.microsoft.com/powershell/module/az.streamanalytics/Remove-azStreamAnalyticsJob)

## <a name="stop-or-delete-a-job-using-azure-sdk-for-net"></a>Остановка или удаление задания с помощью пакета Azure SDK для .NET

Чтобы остановить задание с помощью пакета Azure SDK для .NET, используйте метод [StreamingJobsOperationsExtensions.BeginStop](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop?view=azure-dotnet). Чтобы удалить задание с помощью пакета Azure SDK для .NET, используйте метод [StreamingJobsOperationsExtensions.BeginDelete](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete?view=azure-dotnet).

## <a name="stop-or-delete-a-job-using-rest-api"></a>Остановка или удаление задания с помощью REST API

Чтобы остановить задание с помощью REST API, используйте метод [Stop](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#stop). Чтобы удалить задание с помощью REST API, используйте метод [Delete](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#delete).
