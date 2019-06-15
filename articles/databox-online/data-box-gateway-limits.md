---
title: Ограничения для шлюза Azure Data Box | Документация Майкрософт
description: Описание ограничений системы и рекомендуемых размеров для шлюза Microsoft Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/20/2019
ms.author: alkohli
ms.openlocfilehash: e80b03f696a78887676e9f16750055a4dcfac230
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60755228"
---
# <a name="azure-data-box-gateway-limits"></a>Ограничения Azure шлюз поле данных

Учитывайте эти ограничения, когда развертываете и используете решение шлюза Microsoft Azure Data Box. 


## <a name="data-box-gateway-service-limits"></a>Ограничения службы шлюза Data Box

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="data-box-gateway-device-limits"></a>Ограничение устройства шлюза Data Box

В следующей таблице описаны ограничения для устройства шлюза Data Box.

| Описание | Значение |
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

## <a name="next-steps"></a>Дальнейшие действия

- [Подготовка к развертыванию шлюза Azure Data Box](data-box-gateway-deploy-prep.md)
