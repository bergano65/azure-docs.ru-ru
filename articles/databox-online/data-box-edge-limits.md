---
title: Ограничения Azure Data Box Edge | Документация Майкрософт
description: В этой статье описано ограничения системы и рекомендуемые размеры для Microsoft Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/05/2019
ms.author: alkohli
ms.openlocfilehash: 30e0c37d3d0c03e77b6dab9c06c0a50bff27e8bc
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55966943"
---
# <a name="azure-data-box-edge-limits-preview"></a>Ограничения Azure Data Box Edge (предварительная версия)

Учитывайте эти ограничения, когда развертываете и используете решение Microsoft Azure Data Box Edge.

> [!IMPORTANT]
> В данный момент решение Data Box Edge находится в состоянии ознакомительной версии. Изучите [условия использования для предварительной версии](https://azure.microsoft.com/support/legal/preview-supplemental-terms/), прежде чем развертывать это решение.


## <a name="data-box-edge-service-limits"></a>Ограничения службы Data Box Edge

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="data-box-edge-device-limits"></a>Ограничения устройства Data Box Edge

В следующей таблице описаны ограничения для устройства Data Box Edge.

| ОПИСАНИЕ | Значение |
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

## <a name="next-steps"></a>Дополнительная информация

- [Подготовка к развертыванию шлюза Azure Data Box](data-box-gateway-deploy-prep.md)
