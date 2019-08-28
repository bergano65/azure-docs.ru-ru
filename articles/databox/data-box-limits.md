---
title: Ограничения Azure Data Box | Документация Майкрософт
description: В этой статье описаны ограничения системы и рекомендуемые размеры для компонентов и подключений Microsoft Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 08/27/2019
ms.author: alkohli
ms.openlocfilehash: 13d5bce46883ea4ddca91d7222f68a19ba65c21c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098817"
---
# <a name="azure-data-box-limits"></a>Ограничения Azure Data Box

Учитывайте эти ограничения, когда развертываете и используете Microsoft Azure Data Box. Ограничения для Data Box описаны в следующей таблице.

## <a name="data-box-service-limits"></a>Ограничения службы Data Box

[!INCLUDE [data-box-service-limits](../../includes/data-box-service-limits.md)]

## <a name="data-box-limits"></a>Ограничения Data Box

- В устройстве Data Box может храниться до 500 миллионов файлов.
- Data Box поддерживает не более 512 контейнеров или общих папок в облаке. Каталоги верхнего уровня в общей папке пользователя преобразуются в контейнеры или общие файловые ресурсы Azure в облаке. 

## <a name="azure-storage-limits"></a>Ограничения службы хранилища Azure

[!INCLUDE [data-box-storage-limits](../../includes/data-box-storage-limits.md)]

## <a name="data-upload-caveats"></a>Предупреждения, связанные с передачей данных

[!INCLUDE [data-box-data-upload-caveats](../../includes/data-box-data-upload-caveats.md)]

## <a name="azure-storage-account-size-limits"></a>Ограничения размера для учетной записи хранения Azure

[!INCLUDE [data-box-storage-account-size-limits](../../includes/data-box-storage-account-size-limits.md)]

## <a name="azure-object-size-limits"></a>Ограничения размера для объектов Azure

[!INCLUDE [data-box-object-size-limits](../../includes/data-box-object-size-limits.md)]

## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Соглашения об именовании для файлов, блочных и страничных BLOB-объектов Azure

[!INCLUDE [data-box-naming-conventions](../../includes/data-box-naming-conventions.md)]
