---
title: Объемы файлов NetApp Mount Azure для виртуальных машин
description: Узнайте, как смонтировать или отойти на режут громкость для виртуальных компьютеров Windows или виртуальных linux в Azure.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: c439ff8df95d759e96d2fc82356bda8551507e8d
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084946"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>Подключение или отключение тома для виртуальных машин Windows или Linux 

При необходимости вы можете подключить или отключить том для виртуальных машин Windows или Linux.  Инструкции по подключению для виртуальных машин Linux доступны в службе Azure NetApp Files.  

1. Нажмите на лезвие **томов,** а затем выберите громкость, для которой вы хотите смонтировать. 
2. Нажмите **На кнопку инструкции Mount** из выбранного тома, а затем следуйте инструкциям, чтобы смонтировать громкость. 

    ![Инструкции по горе NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![Инструкции по горе SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)
    
    Если вы используете NFSv4.1, используйте следующую команду для установки файловой системы:`sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  

3. Если вы хотите автоматически установить громкость NFS при начале или перезагрузке Azure `/etc/fstab` VM, добавьте запись в файл узла. 

    Например:  `$ANFIP:/$FILEPATH        /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

    * `$ANFIP`— это IP-адрес объема файлов Azure NetApp, найденный в лезвии свойств громкости.
    * `$FILEPATH`— это траектория экспорта объема файлов NetApp Azure.
    * `$MOUNTPOINT`— это каталог, созданный на хосте Linux, используемый для установки экспорта NFS.

4. Если вы хотите смонтировать громкость в Windows с помощью NFS:

    а. Сначала установите громкость на Unix или Linux VM.  
    b. Выполнить `chmod 777` `chmod 775` или командовать против объема.  
    c. Установите громкость через клиент NFS на Windows.

## <a name="next-steps"></a>Дальнейшие действия

* [Настройка домена по умолчанию NFSv4.1 для Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md)
* [Часто задаваемые вопросы NFS](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#nfs-faqs)
* [Обзор сетевой файловой системы](https://docs.microsoft.com/windows-server/storage/nfs/nfs-overview)
