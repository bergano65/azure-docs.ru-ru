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
ms.openlocfilehash: 84496fbc8a415171172d0a138f647ecb0310b6c7
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173586"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>Подключение или отключение тома для виртуальных машин Windows или Linux 

При необходимости вы можете подключить или отключить том для виртуальных машин Windows или Linux.  Инструкции по подключению для виртуальных машин Linux доступны в службе Azure NetApp Files.  

1. Щелкните колонку **тома** , а затем выберите том, для которого необходимо установить подключение. 
2. Нажмите кнопку **подключить инструкции** из выбранного тома, а затем следуйте инструкциям по подключению тома. 

    ![Инструкции по подключению NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![Инструкции по подключению SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)
    
    Если вы используете Нфсв 4.1, для подключения файловой системы используйте следующую команду: `sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  

3. Если вы хотите, чтобы том NFS был автоматически подключен при запуске или перезагрузке виртуальной машины Azure, добавьте запись в файл `/etc/fstab` на узле. 

    Например: `$ANFIP:/$FILEPATH        /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

    * `$ANFIP` — IP-адрес тома Azure NetApp Files, который находится в колонке свойства тома.
    * `$FILEPATH` — путь экспорта тома Azure NetApp Files.
    * `$MOUNTPOINT` — это каталог, созданный на узле Linux, который используется для подключения экспорта NFS.

## <a name="next-steps"></a>Дополнительная информация

* [Настройка домена Нфсв 4.1 по умолчанию для Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md)
* [Вопросы и ответы по NFS](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#nfs-faqs)
