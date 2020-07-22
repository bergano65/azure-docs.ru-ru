---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/24/2020
ms.author: alkohli
ms.openlocfilehash: 5c5732a825116d4762c3a27bcbf9eac195327afb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85378396"
---
Ниже приведены ограничения для размера данных, копируемых в учетную запись хранения. Убедитесь, что отправляемые данные соответствуют этим ограничениям. Самые последние сведения об этих ограничениях см. в разделе [целевые показатели масштабируемости и производительности для хранилища BLOB-объектов](../articles/storage/blobs/scalability-targets.md) и [целевые показатели масштабируемости и производительности файлов Azure](../articles/storage/files/storage-files-scale-targets.md).

| Размер данных, копируемых в учетную запись хранения Azure                      | Ограничение по умолчанию          |
|---------------------------------------------------------------------|------------------------|
| Блочный BLOB-объект и страничный BLOB-объект                                            | Максимальное ограничение совпадает с [ограничением хранилища, определенным для подписки Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#storage-limits) , и включает данные из всех источников, включая Data Box.|
| Файлы Azure                                                          | Максимальный размер стандартных файловых ресурсов составляет 5 ТБ <br> Максимальный размер файловых ресурсов уровня "Премиум" — 100TiB на общую папку.<br> Все папки в *StorageAccount_AzureFiles* должны соответствовать этому ограничению.       |
