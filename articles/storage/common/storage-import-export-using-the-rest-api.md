---
title: Использование REST API службы импорта и экспорта Azure | Документация Майкрософт
description: Узнайте, где найти документацию по использованию REST API службы импорта и экспорта Azure, включая практические руководства и справочные материалы.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 833b8c79fba57b7129092e084381c0671c396496
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978872"
---
# <a name="using-the-azure-importexport-service-rest-api"></a>Использование REST API службы импорта и экспорта Azure

Служба импорта и экспорта Microsoft Azure предоставляет REST API для программного управления заданиями импорта и экспорта. REST API можно использовать для всех операций импорта и экспорта, которые можно выполнять с помощью [портала Azure](https://portal.azure.com/). Кроме того, с помощью REST API можно выполнять определенные детализированные операции (например, запросить процент завершения задания), которые в настоящее время недоступны на портале Azure.

Общие сведения о службе импорта и экспорта, а также руководство по использованию портала Azure создания заданий импорта и экспорта и управления ими см. в статье об [использовании службы импорта и экспорта Azure для передачи данных в хранилище BLOB-объектов](../storage-import-export-service.md).

## <a name="service-endpoints"></a>Конечные точки службы

Служба импорта и экспорта Azure представляет собой поставщик ресурсов для Azure Resource Manager. Она предоставляет набор интерфейсов REST API для управления заданиями импорта и экспорта в следующей конечной точке HTTPS:

```
https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ImportExport/jobs/<job-name>
```

## <a name="versioning"></a>Управление версиями

В запросах к службе импорта и экспорта необходимо указать параметр `api-version` со значением `2016-11-01`.

## <a name="importexport-service-operations"></a>Операции службы импорта и экспорта

[Создание задания импорта](../storage-import-export-creating-an-import-job.md)

[Создание задания экспорта](../storage-import-export-creating-an-export-job.md)

[Получение сведений о состоянии задания](storage-import-export-retrieving-state-info-for-a-job.md)

[Перечисление заданий](../storage-import-export-enumerating-jobs.md)

[Отмена и удаление заданий](storage-import-export-cancelling-and-deleting-jobs.md)

[Архивация манифестов дисков](../storage-import-export-backing-up-drive-manifests.md)

[Диагностика и восстановление после ошибок для заданий импорта и экспорта](../storage-import-export-diagnostics-and-error-recovery.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Storage Import/Export REST](/rest/api/storageimportexport) (Справочник по API REST служб хранилища импорта и экспорта)
