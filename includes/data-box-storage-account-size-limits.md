---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: 0975df8ee9da4a239dbd72e468b967c88ab3feed
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67277430"
---
Ниже приведены ограничения для размера данных, копируемых в учетную запись хранения. Убедитесь, что отправляемые данные соответствуют этим ограничениям. Наиболее актуальные сведения об этих ограничениях см. в статьях [Целевые показатели масштабируемости хранилища BLOB-объектов Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets) и [Целевые показатели масштабируемости службы файлов Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-files-scale-targets).

| Размер данных, копируемых в учетную запись хранения Azure                      | Ограничение по умолчанию          |
|---------------------------------------------------------------------|------------------------|
| Блочный BLOB-объект и страничный BLOB-объект                                            | 2 PB для жителей США и Европе.<br>500 ТБ для всех других регионов, включая Великобритании.  <br> Сюда входят данные из всех источников, включая Data Box.|
| Файлы Azure                                                          | 5 ТБ на акцию, т.е.<br> Все папки в *StorageAccount_AzureFiles* должны соответствовать этому ограничению.       |
