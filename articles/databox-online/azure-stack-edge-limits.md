---
title: Пределы Azure Stack границы Pro | Документация Майкрософт
description: Сведения об ограничениях и рекомендуемых размерах при развертывании и эксплуатации Azure Stack пограничных Pro, включая ограничения службы, ограничения устройств и ограничения хранилища.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 5f3c39ce7334145d3ffc1d54badb1f7b766da70f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90904454"
---
# <a name="azure-stack-edge-pro-limits"></a>Ограничения Azure Stack Edge Pro

Эти ограничения следует учитывать при развертывании и эксплуатации решения Microsoft Azure Stack пограничной Pro. 

## <a name="azure-stack-edge-service-limits"></a>Ограничения службы Azure Stack ребра

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-device-limits"></a>Ограничения пограничных устройств Azure Stack

В следующей таблице описаны ограничения для устройства Pro Azure Stack. 

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

## <a name="next-steps"></a>Дальнейшие действия

- [Подготовка к развертыванию Azure Stack пограничных Pro](azure-stack-edge-deploy-prep.md)
