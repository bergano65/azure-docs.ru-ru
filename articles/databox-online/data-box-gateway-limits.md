---
title: Ограничения для шлюза Azure Data Box | Документация Майкрософт
description: Описание ограничений системы и рекомендуемых размеров для шлюза Microsoft Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 02/14/2019
ms.author: alkohli
ms.openlocfilehash: 34aac9a589516ace080906095ef3c14b34469bbd
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56311194"
---
# <a name="azure-data-box-gateway-limits-preview"></a>Ограничения для шлюза Azure Data Box (предварительная версия)


Учитывайте эти ограничения, когда развертываете и используете решение шлюза Microsoft Azure Data Box. 

> [!IMPORTANT] 
> Шлюз Data Box доступен в качестве предварительной версии. Изучите [условия использования для предварительной версии](https://azure.microsoft.com/support/legal/preview-supplemental-terms/), прежде чем развертывать это решение. 


## <a name="data-box-gateway-service-limits"></a>Ограничения службы шлюза Data Box

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="data-box-gateway-device-limits"></a>Ограничение устройства шлюза Data Box

В следующей таблице описаны ограничения для устройства шлюза Data Box.

| ОПИСАНИЕ | Значение |
|---|---|
|№ файлов на каждом устройстве |100 млн <br> Ограничение составляет ~25 миллионов файлов на каждые 2 ТБ места на диске с максимальным ограничением 100 миллионов |
|№ общих папок на каждом устройстве |24 |
|№ общих папок в каждом контейнере службы хранилища Azure |1 |
|Максимальный размер файла, записанного в общую папку|Для виртуального устройства объемом в 2 ТБ максимальный размер файла составляет 500 ГБ. <br> Максимальный размер файла пропорционально увеличивается в соответствии с размером диска данных, пока не достигнет 5 ТБ. |

## <a name="azure-storage-limits"></a>Ограничения службы хранилища Azure

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Предупреждения, связанные с передачей данных

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Ограничения размера для объектов и учетной записи хранения Azure

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]

## <a name="azure-object-size-limits"></a>Ограничения размера для объектов Azure

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Дополнительная информация

- [Подготовка к развертыванию шлюза Azure Data Box](data-box-gateway-deploy-prep.md)
