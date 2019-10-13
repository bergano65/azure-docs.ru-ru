---
title: Что случилось с моим проектом веб-задания (хранилище Azure в Visual Studio)?
description: Описывает, что произошло в проекте веб-задания Azure после подключения к учетной записи хранения с помощью подключенных служб Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: 36ae7ff7-c22c-47eb-b220-049d61618c74
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 17861b7c25dfaf9bc9399e5261cdf2a5b43caf21
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298729"
---
# <a name="what-happened-to-my-webjob-project-visual-studio-azure-storage-connected-service"></a>Что произошло с моим проектом веб-заданий (подключенными к службе хранилища Azure службами Visual Studio)?
## <a name="references-added"></a>Добавленные ссылки
Пакет NuGet хранилища Azure добавлен в проект Visual Studio или обновлен в этом проекте.  
Этот пакет добавляет следующие ссылки .NET:

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.ConfigurationManager**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>Добавлена строка подключения к хранилищу Azure
В файле App.config вашего проекта в записи **AzureWebJobsStorage** и **AzureWebJobsDashboard** добавлена строка подключения и ключ выбранной учетной записи хранения.

Дополнительные сведения см. в статье [Документация по веб-заданиям Azure](https://go.microsoft.com/fwlink/?linkid=390226).

