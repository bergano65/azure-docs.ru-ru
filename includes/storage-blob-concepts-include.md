---
title: включить файл
description: включить файл
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/05/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: e90e750bf248bdcc8e50c6ddc6e9fa0273660195
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79136024"
---
Хранилище BLOB-объектов Azure — это решение корпорации Майкрософт для хранения объектов в облаке. Хранилище BLOB-объектов оптимизировано для хранения больших объемов неструктурированных данных. Неструктурированные данные — это данные, которые не соответствуют определенной модели данных или определению, например текстовых или двоичных данных.

## <a name="about-blob-storage"></a>Сведения о хранилище BLOB-объектов

Хранилище BLOB-объектов предназначено для следующих задач:

* Обслуживание изображений или документов непосредственно в браузере.
* Хранение файлов для распределенного доступа.
* Потоковая передача видео и аудио.
* Запись в файлы журнала.
* Хранение резервных копий и восстановление данных, аварийное восстановление и архивация.
* Хранение данных для анализа локальной службой или службой, размещенной в Azure.

Пользователи или клиентские приложения могут получить доступ к объектам в хранилище BLOB-объектов через HTTP/HTTPS из любой точки мира. К объектам в хранилище BLOB-объектов можно обращаться через [REST API службы хранилища Azure](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), [Azure CLI](https://docs.microsoft.com/cli/azure/storage) или клиентскую библиотеку службы хранилища Azure. Клиентские библиотеки доступны для разных языков, включая:

* [.NET](/dotnet/api/overview/azure/storage?view=azure-dotnet)
* [Java](https://docs.microsoft.com/java/api/overview/azure/storage)
* [Node.js](https://azure.github.io/azure-storage-node) 
* [Python](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-python)
* [GO](https://github.com/azure/azure-storage-blob-go/)
* [PHP](https://azure.github.io/azure-storage-php/)
* [Ruby](https://azure.github.io/azure-storage-ruby)

## <a name="about-azure-data-lake-storage-gen2"></a>Сведения об Azure Data Lake Storage 2-го поколения

Хранилище BLOB-объектов поддерживает Azure Data Lake Storage 2-го поколения, решение аналитики больших данных корпорации Майкрософт, предназначенное для облака. Azure Data Lake Storage 2-го поколения предлагает иерархическую файловую систему, а также преимущества хранилища BLOB-объектов:

* экономичное многоуровневое хранилище;
* Высокий уровень доступности
* Строгая согласованность
* возможности аварийного восстановления.

Общие сведения об Azure Data Lake Storage 2-го поколения см. в [этой статье](../articles/storage/data-lake-storage/introduction.md).
