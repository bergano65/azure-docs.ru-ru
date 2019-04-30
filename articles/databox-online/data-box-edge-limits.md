---
title: Ограничения Azure Data Box Edge | Документация Майкрософт
description: В этой статье описано ограничения системы и рекомендуемые размеры для Microsoft Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/22/2019
ms.author: alkohli
ms.openlocfilehash: a687398032adffe5e787ef1469abe0b2627a38fe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60758030"
---
# <a name="azure-data-box-edge-limits"></a>Ограничения поля данных Microsoft Edge

Учитывайте эти ограничения, когда развертываете и используете решение Microsoft Azure Data Box Edge.

## <a name="data-box-edge-service-limits"></a>Ограничения службы Data Box Edge

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="data-box-edge-device-limits"></a>Ограничения устройства Data Box Edge

В следующей таблице описаны ограничения для устройства Data Box Edge.

| Описание | Value |
|---|---|
|№ файлов на каждом устройстве |100 млн |
|№ общих папок на каждом устройстве |24 |
|№ общих папок в каждом контейнере |1 |
|Максимальный размер файла, записанного в общую папку| 5 ТБ |

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
