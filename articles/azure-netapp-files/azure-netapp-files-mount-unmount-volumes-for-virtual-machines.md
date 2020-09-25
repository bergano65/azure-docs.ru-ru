---
title: Подключение Azure NetApp Files томов для виртуальных машин
description: Узнайте, как подключить или отключить том для виртуальных машин Windows или виртуальных машин Linux в Azure.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: d5db91a8864d6090466b40197187c9386e053d12
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91325544"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>Подключение или отключение тома для виртуальных машин Windows или Linux 

При необходимости вы можете подключить или отключить том для виртуальных машин Windows или Linux.  Инструкции по подключению для виртуальных машин Linux доступны в службе Azure NetApp Files.  

## <a name="requirements"></a>Требования 

* Чтобы получить доступ к тому NFS, необходимо иметь хотя бы одну политику экспорта.
* Чтобы успешно подключить том NFS, убедитесь, что между клиентом и томами NFS открыты следующие порты NFS:
    * 111 = `RPCBIND/Portmapper`
    * 635 = `mountd`
    * 2049 = `nfs`
    * 4045 = `nlockmgr` (только NFSv3)
    * 4046 = `status` (только NFSv3)

## <a name="steps"></a>Шаги

1. Щелкните колонку **тома** , а затем выберите том, для которого необходимо установить подключение. 
2. Нажмите кнопку **подключить инструкции** из выбранного тома, а затем следуйте инструкциям по подключению тома. 

    ![Инструкции по подключению NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![Инструкции по подключению SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)  
    * При подключении тома NFS убедитесь, что используется `vers` параметр в `mount` команде, чтобы указать версию протокола NFS, соответствующую тому, который необходимо подключить. 
    * Если вы используете Нфсв 4.1, для подключения файловой системы используйте следующую команду:  `sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  
        > [!NOTE]
        > При использовании Нфсв 4.1 Убедитесь, что на всех виртуальных машинах для подключения экспорта используются уникальные имена узлов.

3. Если вы хотите, чтобы том NFS был автоматически подключен при запуске или перезагрузке виртуальной машины Azure, добавьте запись в `/etc/fstab` файл на узле. 

    Например: `$ANFIP:/$FILEPATH        /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

    * `$ANFIP` — Это IP-адрес тома Azure NetApp Files, найденного в колонке свойств тома.
    * `$FILEPATH` путь экспорта Azure NetApp Filesого тома.
    * `$MOUNTPOINT` — это каталог, созданный на узле Linux, который используется для подключения экспорта NFS.

4. Если вы хотите подключить том к Windows с помощью NFS:

    а. Сначала подключите том на виртуальной машине UNIX или Linux.  
    b. Выполните `chmod 777` команду или `chmod 775` для тома.  
    c. Подключите том через клиент NFS в Windows.
    
5. Если вы хотите подключить том Kerberos для NFS, см. Дополнительные сведения в разделе [Настройка шифрования Kerberos для нфсв 4.1](configure-kerberos-encryption.md) . 

## <a name="next-steps"></a>Дальнейшие действия

* [Настройка домена по умолчанию NFSv4.1 для Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md)
* [Вопросы и ответы по NFS](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#nfs-faqs)
* [Обзор сетевой файловой системы](https://docs.microsoft.com/windows-server/storage/nfs/nfs-overview)
* [Подключение тома Kerberos NFS](configure-kerberos-encryption.md#kerberos_mount)
