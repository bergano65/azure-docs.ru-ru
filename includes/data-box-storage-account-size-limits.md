---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: 6a9c31eb46a457433834d5940b3f7e190ebe1476
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75469875"
---
Ниже приведены ограничения для размера данных, копируемых в учетную запись хранения. Убедитесь, что отправляемые данные соответствуют этим ограничениям. Для получения самой актуальной информации об этих ограничениях см. [цели масштабируемости и производительности для целей масштабирования и](../articles/storage/blobs/scalability-targets.md) [производительности файлов Azure Files.](../articles/storage/files/storage-files-scale-targets.md)

| Размер данных, копируемых в учетную запись хранения Azure                      | Ограничение по умолчанию          |
|---------------------------------------------------------------------|------------------------|
| Блочный BLOB-объект и страничный BLOB-объект                                            | 2 PB для США и Европы.<br>500 ТБ для всех других регионов, включая Великобританию.  <br> Сюда входят данные из всех источников, включая Data Box.|
| Файлы Azure                                                          | 5 ТБ на акцию.<br> Все папки в *StorageAccount_AzureFiles* должны соответствовать этому ограничению.       |
