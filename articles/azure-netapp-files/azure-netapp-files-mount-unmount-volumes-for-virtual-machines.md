---
title: Подключение Azure NetApp Files томов для виртуальных машин
description: Узнайте, как подключить или отключить том для виртуальных машин Windows или виртуальных машин Linux в Azure.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 07/06/2020
ms.openlocfilehash: 4bfd90be2a469c5ab94172769729095069f53cd7
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045660"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>Подключение или отключение тома для виртуальных машин Windows или Linux 

При необходимости вы можете подключить или отключить том для виртуальных машин Windows или Linux.  Инструкции по подключению для виртуальных машин Linux доступны в службе Azure NetApp Files.  

> [!IMPORTANT] 
> Чтобы получить доступ к тому NFS, необходимо иметь хотя бы одну политику экспорта.

1. Щелкните колонку **тома** , а затем выберите том, для которого необходимо установить подключение. 
2. Нажмите кнопку **подключить инструкции** из выбранного тома, а затем следуйте инструкциям по подключению тома. 

    ![Инструкции по подключению NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![Инструкции по подключению SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)  
    * При подключении тома NFS убедитесь, что используется `vers` параметр в `mount` команде, чтобы указать версию протокола NFS, соответствующую тому, который необходимо подключить. 
    * Если вы используете Нфсв 4.1, для подключения файловой системы используйте следующую команду:`sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  

3. Если вы хотите, чтобы том NFS был автоматически подключен при запуске или перезагрузке виртуальной машины Azure, добавьте запись в `/etc/fstab` файл на узле. 

    Например:  `$ANFIP:/$FILEPATH        /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

    * `$ANFIP`— Это IP-адрес тома Azure NetApp Files, найденного в колонке свойств тома.
    * `$FILEPATH`путь экспорта Azure NetApp Filesого тома.
    * `$MOUNTPOINT`— это каталог, созданный на узле Linux, который используется для подключения экспорта NFS.

4. Если вы хотите подключить том к Windows с помощью NFS:

    а. Сначала подключите том на виртуальной машине UNIX или Linux.  
    b. Выполните `chmod 777` команду или `chmod 775` для тома.  
    c. Подключите том через клиент NFS в Windows.

## <a name="next-steps"></a>Дальнейшие шаги

* [Настройка домена по умолчанию NFSv4.1 для Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md)
* [Вопросы и ответы по NFS](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#nfs-faqs)
* [Обзор сетевой файловой системы](https://docs.microsoft.com/windows-server/storage/nfs/nfs-overview)
