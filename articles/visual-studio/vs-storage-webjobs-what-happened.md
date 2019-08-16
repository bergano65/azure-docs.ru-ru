---
title: Что произошло с моим проектом веб-заданий (подключенными к службе хранилища Azure службами Visual Studio)? | Документы Майкрософт
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
ms.openlocfilehash: c54214e0b919ddaa60403a1c986a144100143577
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69510479"
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

