---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/24/2020
ms.author: alkohli
ms.openlocfilehash: acaebcea59e765f5544f1bfbd692c6508f66a84a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91025238"
---
Ниже приведены ограничения для размера данных, копируемых в учетную запись хранения. Убедитесь, что отправляемые данные соответствуют этим ограничениям. Самые последние сведения об этих ограничениях см. в разделе [целевые показатели масштабируемости и производительности для хранилища BLOB-объектов](../articles/storage/blobs/scalability-targets.md) и [целевые показатели масштабируемости и производительности файлов Azure](../articles/storage/files/storage-files-scale-targets.md).

| Размер данных, копируемых в учетную запись хранения Azure                      | Ограничение по умолчанию          |
|---------------------------------------------------------------------|------------------------|
| Блочный BLOB-объект и страничный BLOB-объект                                            | Максимальное ограничение совпадает с [ограничением хранилища, определенным для подписки Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#storage-limits) , и включает данные из всех источников, включая Data Box. |
| Файлы Azure                                                          | Data Box поддерживает большие файловые ресурсы (100TiB), если они включены перед созданием порядка Data Box. <br> Если не включено до создания заказа, максимальный поддерживаемый размер файлового ресурса составляет 5 тиб. <br> Файловые ресурсы уровня "Премиум" пока не поддерживаются.<br> Все папки в *StorageAccount_AzureFiles* должны соответствовать этому ограничению. <br> Дополнительные сведения см. в разделе [Включение и создание больших файловых ресурсов](../articles/storage/files/storage-files-how-to-create-large-file-share.md) .      |
