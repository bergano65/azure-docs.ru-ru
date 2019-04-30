---
title: Устранение неполадок дисков, подключенных к виртуальным машинам Azure | Документация Майкрософт
description: Ссылки на ресурсы по устранению неполадок с виртуальными жесткими дисками виртуальной машины Azure.
services: storage
author: WenJason
ms.service: storage
ms.topic: article
origin.date: 10/31/2018
ms.date: 12/10/2018
ms.author: v-jay
ms.openlocfilehash: bf1aa75399f28b8dd1732bc9ec09c15e46132939
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098105"
---
# <a name="troubleshoot-disks-attached-to-azure-vms"></a>Устранение неполадок дисков, подключенных к виртуальным машинам Azure 

В виртуальных машинах Azure для диска операционной системы и всех подключенных дисков данных используются виртуальные жесткие диски (VHD). VHD хранятся в одной или нескольких учетных записях хранения Azure как страничные BLOB-объекты. В этой статье приведены указания по устранению распространенных неполадок с VHD. 

## <a name="troubleshoot-storage-deletion-errors-for-a-vm"></a>Устранение неполадок с удалением ресурсов хранилища виртуальной машины

В некоторых случаях может возникнуть ошибка при удалении ресурса хранилища, если виртуальная машина в развертывании Resource Manager содержит подключенные VHD. Помощь в устранении этой проблемы можно получить, ознакомившись с одной из следующих статей: 

  * На виртуальных машинах Linux: [ошибки удаления хранилища в развертывании Resource Manager](../../virtual-machines/linux/storage-resource-deletion-errors.md)  
  * На виртуальных машинах Windows: [ошибки удаления хранилища в развертывании Resource Manager](../../virtual-machines/windows/storage-resource-deletion-errors.md)  

## <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Устранение непредвиденных перезагрузок виртуальных машин с подключенными VHD

Если происходят непредвиденные перезагрузки виртуальной машины с большим числом подключенных VHD, ознакомьтесь со следующими статьями:

  * На виртуальных машинах Linux: [непредвиденные перезагрузки виртуальных машин с подключенными VHD](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md);
  * На виртуальных машинах Windows: [непредвиденные перезагрузки виртуальных машин с подключенными VHD](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md);
