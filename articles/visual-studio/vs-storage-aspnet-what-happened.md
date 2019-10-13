---
title: Что произошло с моим проектом ASP.NET?
description: Описывает, что происходит после добавления службы хранилища Azure в проект ASP.NET с помощью подключенных служб Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: e1fe1b6d-4e3d-476d-8b2f-f7ade050515e
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 4374955d6d51c1ae44c211aaa93d0b5e8930fe5b
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/13/2019
ms.locfileid: "72300038"
---
# <a name="what-happened-to-my-aspnet-project-visual-studio-azure-storage-connected-service"></a>Что произошло с моим проектом ASP.NET (подключенными к службе хранилища Azure службами Visual Studio)?
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
В файле web.config проекта был создан элемент с ключом и строкой подключения выбранной учетной записи хранилища.

Дополнительные сведения см. в разделе [ASP.NET](https://www.asp.net).

