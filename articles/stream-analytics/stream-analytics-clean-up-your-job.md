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
ms.openlocfilehash: 85db38fef5e69c4de855f8cb6d54151496faebbe
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53090250"
---
# <a name="clean-up-your-azure-stream-analytics-job"></a>Очистка задания Azure Stream Analytics

Задания Azure Stream Analytics можно легко удалять с помощью портала Azure, Azure PowerShell, пакета Azure SDK для .NET или REST API.

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

Чтобы остановить задание с помощью PowerShell, используйте командлет [Stop AzureRmStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/stop-azurermstreamanalyticsjob?view=azurermps-5.7.0). Чтобы удалить задание с помощью PowerShell, используйте командлет [Remove-AzureRmStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/Remove-AzureRmStreamAnalyticsJob?view=azurermps-5.7.0).

## <a name="stop-or-delete-a-job-using-azure-sdk-for-net"></a>Остановка или удаление задания с помощью пакета Azure SDK для .NET

Чтобы остановить задание с помощью пакета Azure SDK для .NET, используйте метод [StreamingJobsOperationsExtensions.BeginStop](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop?view=azure-dotnet). Чтобы удалить задание с помощью пакета Azure SDK для .NET, используйте метод [StreamingJobsOperationsExtensions.BeginDelete](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete?view=azure-dotnet).

## <a name="stop-or-delete-a-job-using-rest-api"></a>Остановка или удаление задания с помощью REST API

Чтобы остановить задание с помощью REST API, используйте метод [Stop](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#stop). Чтобы удалить задание с помощью REST API, используйте метод [Delete](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#delete).
