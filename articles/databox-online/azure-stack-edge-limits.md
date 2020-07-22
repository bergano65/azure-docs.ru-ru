---
title: Пределы Azure Stack границ | Документация Майкрософт
description: Описывает ограничения системы и рекомендованные размеры для Azure Stackной границы.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/22/2019
ms.author: alkohli
ms.openlocfilehash: 4f7800efb5d4382e8d73c819d950fdfafd10f296
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82569840"
---
# <a name="azure-stack-edge-limits"></a>Ограничения Azure Stack Edge

Эти ограничения следует учитывать при развертывании и эксплуатации решения Microsoft Azure Stack пограничной Организации. 

## <a name="azure-stack-edge-service-limits"></a>Ограничения службы Azure Stack ребра

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="azure-stack-device-limits"></a>Ограничения для устройств Azure Stack

В следующей таблице описаны ограничения для устройства Azure Stack пограничных устройств. 

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

- [Подготовка к развертыванию Azure Stack пограничных устройств](azure-stack-edge-deploy-prep.md)
