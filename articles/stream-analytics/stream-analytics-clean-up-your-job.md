---
title: Очистка задания Azure Stream Analytics
description: В этой статье показаны различные методы удаления заданий Azure Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: e43e1034abe4bbe3d31a46ab3b98b0efe612b852
ms.sourcegitcommit: 89b5e63945d0c325c1bf9e70ba3d9be6888da681
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/08/2019
ms.locfileid: "57588726"
---
# <a name="clean-up-your-azure-stream-analytics-job"></a>Очистка задания Azure Stream Analytics

Задания Azure Stream Analytics можно легко удалять с помощью портала Azure, Azure PowerShell, пакета Azure SDK для .NET или REST API. Задание Stream Analytics нельзя восстановить после его удаления.

>[!NOTE] 
>При остановке задания Stream Analytics данные сохраняются в хранилище входных и выходных данных, например Центрах событий или базе данных SQL Azure. Если необходимо удалить данные из Azure, обязательно следуйте процедуре удаления входящих и исходящих ресурсов задания Stream Analytics.

## <a name="stop-a-job-in-azure-portal"></a>Остановка задания на портале Azure

1. Войдите на [портале Azure](https://portal.azure.com). 

2. Найдите выполняющееся задание Stream Analytics и выберите его.

3. На странице задания Stream Analytics щелкните **Остановить**, чтобы остановить задание. 

   ![Остановка задания Azure Stream Analytics](./media/stream-analytics-clean-up-your-job/stop-stream-analytics-job.png)


## <a name="delete-a-job-in-azure-portal"></a>Удаление задания на портале Azure

1. Войдите на портал Azure. 

2. Найдите имеющееся задание Stream Analytics и выберите его.

3. На странице задания Stream Analytics щелкните **Удалить**, чтобы удалить задание. 

   ![Удаление задания Azure Stream Analytics](./media/stream-analytics-clean-up-your-job/delete-stream-analytics-job.png)


## <a name="stop-or-delete-a-job-using-powershell"></a>Остановка или удаление задания с помощью PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Чтобы остановить задание, с помощью PowerShell, используйте [Stop AzStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/az.streamanalytics/stop-azstreamanalyticsjob) командлета. Чтобы удалить задание с помощью PowerShell, используйте [Remove-AzStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/az.streamanalytics/Remove-azStreamAnalyticsJob) командлета.

## <a name="stop-or-delete-a-job-using-azure-sdk-for-net"></a>Остановка или удаление задания с помощью пакета Azure SDK для .NET

Чтобы остановить задание с помощью пакета Azure SDK для .NET, используйте метод [StreamingJobsOperationsExtensions.BeginStop](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop?view=azure-dotnet). Чтобы удалить задание с помощью пакета Azure SDK для .NET, используйте метод [StreamingJobsOperationsExtensions.BeginDelete](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete?view=azure-dotnet).

## <a name="stop-or-delete-a-job-using-rest-api"></a>Остановка или удаление задания с помощью REST API

Чтобы остановить задание с помощью REST API, используйте метод [Stop](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#stop). Чтобы удалить задание с помощью REST API, используйте метод [Delete](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#delete).
