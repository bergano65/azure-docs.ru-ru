---
title: включение файла
description: включение файла
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/18/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 32b9b12c2adf03a4cb0616a5da48dd33fc81fb4f
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52973103"
---
Хранилище BLOB-объектов Azure — это решение корпорации Майкрософт для хранения объектов в облаке. Хранилище BLOB-объектов оптимизировано для хранения больших объемов неструктурированных данных. Неструктурированные данные — это данные, которые не соответствуют определенной модели данных или определению, например текстовых или двоичных данных. 

## <a name="about-blob-storage"></a>Сведения о хранилище BLOB-объектов

Хранилище BLOB-объектов предназначено для следующих задач:

* Обслуживание изображений или документов непосредственно в браузере.
* Хранение файлов для распределенного доступа.
* Потоковая передача видео и аудио.
* Запись в файлы журнала.
* Хранение резервных копий и восстановление данных, аварийное восстановление и архивация.
* Хранение данных для анализа локальной службой или службой, размещенной в Azure.

Пользователи или клиентские приложения могут получить доступ к объектам в хранилище BLOB-объектов через HTTP/HTTPS из любой точки мира. К объектам в хранилище BLOB-объектов можно обращаться через [REST API службы хранилища Azure](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), [Azure CLI](https://docs.microsoft.com/cli/azure/storage) или клиентскую библиотеку службы хранилища Azure. Клиентские библиотеки доступны для различных языков, в том числе [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/client), [Java](https://docs.microsoft.com/java/api/overview/azure/storage/client), [Node.js](http://azure.github.io/azure-storage-node), [Python](https://docs.microsoft.com/python/azure/), [Go](https://github.com/azure/azure-storage-blob-go/), [PHP](http://azure.github.io/azure-storage-php/)и [Ruby](http://azure.github.io/azure-storage-ruby).

## <a name="about-azure-data-lake-storage-gen2"></a>Сведения об Azure Data Lake Storage 2-го поколения 

Хранилище BLOB-объектов поддерживает Azure Data Lake Storage 2-го поколения, решение аналитики больших данных корпорации Майкрософт, предназначенное для облака. Azure Data Lake Storage 2-го поколения предлагает иерархическую файловую систему, а также преимущества хранилища BLOB-объектов, включая экономичное многоуровневое хранилище, высокую доступность, строгую согласованность и возможности аварийного восстановления. 

Общие сведения об Azure Data Lake Storage 2-го поколения (предварительная версия) см. в [этой статье](../articles/storage/data-lake-storage/introduction.md).