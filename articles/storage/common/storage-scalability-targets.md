---
title: Целевые показатели масштабируемости и производительности службы хранилища Azure
description: Узнайте о целевых показателях масштабируемости и производительности, включая показатели емкости, частоты запросов, входящей и исходящей пропускной способности для учетных записей хранения Azure ценовой категории "Стандартный".
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 11/08/2018
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: 2f00b01bb07aafca847897f0c31d24d4add7cdbf
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2019
ms.locfileid: "56328883"
---
# <a name="azure-storage-scalability-and-performance-targets-for-standard-storage-accounts"></a>Целевые показатели масштабируемости и производительности службы хранилища Azure для учетных записей хранения ценовой категории "Стандартный"

В статье подробно рассматриваются целевые показатели масштабируемости и производительности для учетных записей хранения Azure. Приведенные целевые показатели производительности и масштабируемости предельно высоки, но достижимы. В любом случае частота запросов, с которой успешно справляется учетная запись хранения, и ее пропускная способность зависят от размера хранимых объектов, используемых схем доступа и типа рабочей нагрузки приложения. 

Обязательно протестируйте службу для определения соответствия ее производительности необходимым требованиям. По возможности избегайте внезапных пиковых нагрузок по трафику. Убедитесь в том, что трафик соответствующим образом распределяется по разделам.

Когда при работе приложения достигается предельная рабочая нагрузка на раздел, служба хранилища Azure начинает выдавать код ошибки 503 (сервер занят) или 500 (время ожидания операции истекло). При возникновении ошибок 503 попробуйте изменить приложение, чтобы при повторных попытках оно использовало политику экспоненциальной задержки. Экспоненциальное откладывание позволяет уменьшить нагрузку на раздел и облегчить обработку им пикового трафика. 

## <a name="standard-storage-account-scale-limits"></a>Ограничения масштабируемости учетной записи хранения ценовой категории "Стандартный".
[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

## <a name="premium-storage-account-scale-limits"></a>Ограничения масштабируемости учетной записи хранения ценовой категории "Премиум"
[!INCLUDE [azure-premium-limits](../../../includes/azure-storage-limits-premium.md)]

## <a name="storage-resource-provider-scale-limits"></a>Ограничения масштабируемости поставщика ресурсов хранилища

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Целевые показатели масштабируемости хранилища BLOB-объектов Azure
[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Целевые показатели масштабируемости службы файлов Azure
Дополнительные сведения о целевых показателях масштабируемости и производительности для службы файлов Azure и службы синхронизации файлов Azure см. в [этой статье](../files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="azure-file-sync-scale-targets"></a>Целевые показатели масштабируемости службы "Синхронизация файлов Azure"
Служба синхронизации файлов Azure разрабатывалась для использования без ограничений, но такое использование не всегда возможно. В таблице указаны пределы тестирования, проведенного Майкрософт, и жесткие ограничения для некоторых целевых показателей.

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="azure-queue-storage-scale-targets"></a>Целевые показатели масштабируемости хранилища очередей Azure
[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

## <a name="azure-table-storage-scale-targets"></a>Целевые показатели масштабируемости табличного хранилища Azure
[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="see-also"></a>См. также
* [Сведения о ценах на хранилище](https://azure.microsoft.com/pricing/details/storage/)
* [Лимиты, квоты и ограничения подписки и обслуживания Azure](../../azure-subscription-service-limits.md)
* [Репликация хранилища Azure](../storage-redundancy.md)
* [Производительность хранилища Microsoft Azure и контрольный список масштабируемости](../storage-performance-checklist.md)

