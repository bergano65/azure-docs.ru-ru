---
title: Что произошло с моим проектом облачных служб? | Документы Майкрософт
description: Сведения о том, что происходит в проекте облачных служб после подключения к учетной записи хранения Azure с помощью подключенных служб Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: ca0ea68d-f417-4ce8-9413-40d76f69cdea
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: d96e523b6e4e060eaef33de0f961e7020e5dfb57
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69510642"
---
# <a name="what-happened-to-my-cloud-services-project-visual-studio-azure-storage-connected-service"></a>Что произошло с моим проектом облачных служб (подключенными к службе хранилища Azure службами Visual Studio)?
## <a name="references-added"></a>Добавлены ссылки
Пакет NuGet хранилища Azure был добавлен в проект Visual Studio.  
Этот пакет добавляет следующие ссылки .NET:

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.Configuration**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>Добавлена строка подключения к хранилищу Azure
Созданы элементы с ключом и строкой подключения выбранной учетной записи хранилища. Внесены изменения в следующие файлы:

* **ServiceDefinition.csdef**
* **ServiceConfiguration.Cloud.cscfg**
* **ServiceConfiguration.Local.cscfg**

