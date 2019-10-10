---
title: Ограничения Azure Data Box Edge | Документация Майкрософт
description: Описание системных ограничений и рекомендуемых размеров Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/22/2019
ms.author: alkohli
ms.openlocfilehash: 9adfc1ef355c31f0bfeeb6c3b194d62426b7b9b3
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244318"
---
# <a name="azure-data-box-edge-limits"></a>Ограничения Azure Data Box Edge

Учитывайте эти ограничения, когда развертываете и используете решение Microsoft Azure Data Box Edge.

## <a name="data-box-edge-service-limits"></a>Ограничения службы Data Box Edge

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="data-box-edge-device-limits"></a>Ограничения устройства Data Box Edge

В следующей таблице описаны ограничения для устройства Data Box Edge.

| Описание | Значение |
|---|---|
|Нет. файлов на каждом устройстве |100 млн |
|Нет. общих папок на каждом устройстве |24 |
|Нет. общих папок в каждом контейнере |1 |
|Максимальный размер файла, записанного в общую папку| 5 ТБ |

## <a name="azure-storage-limits"></a>Ограничения службы хранилища Azure

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Предупреждения, связанные с передачей данных

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Ограничения размера для объектов и учетной записи хранения Azure

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Ограничения размера для объектов Azure

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Следующие шаги

- [Подготовка к развертыванию Azure Data Box Edge](data-box-edge-deploy-prep.md)
