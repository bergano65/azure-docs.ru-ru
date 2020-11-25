---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/24/2020
ms.author: alkohli
ms.openlocfilehash: 2868e5a53686cfa94dc206c1aab65fe866d19b6d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027972"
---
Ниже приведены ограничения для размера данных, копируемых в учетную запись хранения. Убедитесь, что отправляемые данные соответствуют этим ограничениям. Самые последние сведения об этих ограничениях см. в разделе [целевые показатели масштабируемости и производительности для хранилища BLOB-объектов](../articles/storage/blobs/scalability-targets.md) и [целевые показатели масштабируемости и производительности файлов Azure](../articles/storage/files/storage-files-scale-targets.md).

| Размер данных, копируемых в учетную запись хранения Azure                      | Ограничение по умолчанию          |
|---------------------------------------------------------------------|------------------------|
| Блочный BLOB-объект и страничный BLOB-объект                                            | Максимальное ограничение совпадает с [ограничением хранилища, определенным для подписки Azure](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits) , и включает данные из всех источников, включая Data Box. |
| Файлы Azure                                                          | Data Box поддерживает большие файловые ресурсы (100TiB), если они включены перед созданием порядка Data Box. <br> Если не включено до создания заказа, максимальный поддерживаемый размер файлового ресурса составляет 5 тиб. <br> Файловые ресурсы уровня "Премиум" пока не поддерживаются.<br> Все папки в *StorageAccount_AzureFiles* должны соответствовать этому ограничению. <br> Дополнительные сведения см. в разделе [Включение и создание больших файловых ресурсов](../articles/storage/files/storage-files-how-to-create-large-file-share.md) .      |