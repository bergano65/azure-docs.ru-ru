---
title: Что случилось с моим проектом ASP.NET 5 (подключенные службы Visual Studio)
description: Сведения о том, что происходит после подключения к учетной записи хранения Azure в проекте Visual Studio ASP.NET 5 с помощью подключенных служб Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: e7caa9fa-c780-45eb-a546-299fc1c68455
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: be99465a48aaf680834f313e03384a9f0c211502
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/13/2019
ms.locfileid: "72300016"
---
# <a name="what-happened-to-my-aspnet-5-project-visual-studio-azure-storage-connected-services"></a>Что произошло с моим проектом ASP.NET 5 (подключенными к службе хранилища Azure службами Visual Studio)?
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

Кроме того, добавлен пакет NuGet **Microsoft.Framework.Configuration.Json** .

## <a name="connection-string-for-azure-storage-added"></a>Добавлена строка подключения к хранилищу Azure
В файле config.json проекта был создан элемент с ключом и строкой подключения выбранной учетной записи хранилища.

Дополнительные сведения см. на сайте [ASP.NET 5](https://www.asp.net/vnext).

