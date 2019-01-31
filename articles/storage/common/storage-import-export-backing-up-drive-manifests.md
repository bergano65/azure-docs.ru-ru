---
title: Архивация манифестов дисков для импорта и экспорта Azure | Документация Майкрософт
description: Узнайте, как настроить автоматическую архивацию манифестов диска для службы импорта и экспорта Microsoft Azure.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 61881508e18a2c7dbe1bc3be72d34423f862437a
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55473397"
---
# <a name="backing-up-drive-manifests-for-azure-importexport-jobs"></a>Архивация манифестов дисков для заданий импорта и экспорта Azure

Манифесты диска можно архивировать автоматически в большие двоичные объекты, задав значение `true` для свойства `BackupDriveManifest` при операции REST API [Put Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) или [Update Job Properties](/rest/api/storageimportexport/jobs#Jobs_Update). По умолчанию манифесты диска не архивируются. Резервные копии манифестов диска хранятся в виде блочных BLOB-объектов в контейнере, расположенном в учетной записи хранения, которая связана с заданием. По умолчанию контейнер называется `waimportexport`, но при вызове операции `Put Job` или `Update Job Properties` в свойстве `DiagnosticsPath` можно указать другое имя. Имя большого двоичного объекта с резервными копиями манифестов имеет следующий формат: `waies/jobname_driveid_timestamp_manifest.xml`.

 Универсальный код ресурса (URI) резервных копий манифестов диска для задания можно получить, вызвав операцию [Get Job](/rest/api/storageimportexport/jobs#Jobs_Get). Универсальный код ресурса (URI) большого двоичного объекта возвращается в свойство `ManifestUri` для каждого диска.

## <a name="next-steps"></a>Дополнительная информация

* [Использование REST API службы импорта и экспорта Azure](storage-import-export-using-the-rest-api.md)
