---
title: Устранение неполадок дисков, подключенных к виртуальным машинам Azure | Документация Майкрософт
description: Ссылки на ресурсы по устранению неполадок с виртуальными жесткими дисками виртуальной машины Azure.
services: storage
author: roygara
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: rogarana
ms.reviewer: wmgries
ms.openlocfilehash: b81c0947327dc8e84c5d3fbbecde3aa31313cc70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061179"
---
# <a name="troubleshoot-disks-attached-to-azure-vms"></a>Устранение неполадок дисков, подключенных к виртуальным машинам Azure 

В виртуальных машинах Azure для диска операционной системы и всех подключенных дисков данных используются виртуальные жесткие диски (VHD). VHD хранятся в одной или нескольких учетных записях хранения Azure как страничные BLOB-объекты. В этой статье приведены указания по устранению распространенных неполадок с VHD. 

## <a name="troubleshoot-storage-deletion-errors-for-a-vm"></a>Устранение неполадок с удалением ресурсов хранилища виртуальной машины

В некоторых случаях может возникнуть ошибка при удалении ресурса хранилища, если виртуальная машина в развертывании Resource Manager содержит подключенные VHD. Помощь в устранении этой проблемы можно получить, ознакомившись с одной из следующих статей: 

  * Для виртуальных машин Linux: [Ошибки удаления хранилища в развертывании Resource Manager](../../virtual-machines/linux/storage-resource-deletion-errors.md).  
  * Для виртуальных машин Windows: [Ошибки удаления хранилища в развертывании Resource Manager](../../virtual-machines/windows/storage-resource-deletion-errors.md).  

## <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Устранение непредвиденных перезагрузок виртуальных машин с подключенными VHD

Если происходят непредвиденные перезагрузки виртуальной машины с большим числом подключенных VHD, ознакомьтесь со следующими статьями:

  * Для виртуальных машин Linux: [Непредвиденные перезагрузки виртуальных машин с подключенными VHD](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md).
  * Для виртуальных машин Windows: [Непредвиденные перезагрузки виртуальных машин с подключенными VHD](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md).
