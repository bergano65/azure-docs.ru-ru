---
title: Подключение или отключение тома службы Azure NetApp Files для виртуальных машин Windows или Linux | Документация Майкрософт
description: В этой статье описывается подключение и отключение тома для виртуальных машин Windows или Linux.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: b-juche
ms.openlocfilehash: 4cd3bc7e4f95869d3efd2d92a7cdf1addc7ce5b2
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953094"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>Подключение или отключение тома для виртуальных машин Windows или Linux 

При необходимости вы можете подключить или отключить том для виртуальных машин Windows или Linux.  Инструкции по подключению для виртуальных машин Linux доступны в службе Azure NetApp Files.  

1. Щелкните колонку **тома** , а затем выберите том, для которого необходимо установить подключение. 
2. Нажмите кнопку **подключить инструкции** из выбранного тома, а затем следуйте инструкциям по подключению тома. 

    ![Инструкции по подключению NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![Инструкции по подключению SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)
    
Если вы используете Нфсв 4.1, для подключения файловой системы используйте следующую команду:  

`sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  

## <a name="next-steps"></a>Дополнительная информация

* [Настройка домена Нфсв 4.1 по умолчанию для Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md)
* См. [часто задаваемые вопросы](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#nfs-faqs) о том, как автоматически ПОДКЛЮЧИТЬ том NFS при запуске или перезагрузке виртуальной машины Azure.
