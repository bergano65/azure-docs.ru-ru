---
title: Создание тома NFS для Azure NetApp Files | Документация Майкрософт
description: Описание процесса создания тома NFS для Azure NetApp Files.
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
ms.date: 7/9/2019
ms.author: b-juche
ms.openlocfilehash: 45164acd89fc9634d6929bafb35e64a5dc9f2b86
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2019
ms.locfileid: "71178224"
---
# <a name="create-an-nfs-volume-for-azure-netapp-files"></a>Создание тома NFS для Azure NetApp Files

Azure NetApp Files поддерживает тома NFS и SMBv3. Потребление емкости тома зависит от подготовленной емкости пула. В этой статье показано, как создать том NFS. Если вы хотите создать том SMB, см. раздел [Создание тома SMB для Azure NetApp Files](azure-netapp-files-create-volumes-smb.md). 

## <a name="before-you-begin"></a>Перед началом работы 
Перед началом необходимо настроить пул емкости.   
[Настройка пула емкости](azure-netapp-files-set-up-capacity-pool.md)   
Подсеть должна быть делегирована службе Azure NetApp Files.  
[Делегирование подсети службе Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="create-an-nfs-volume"></a>Создание тома NFS

1.  Щелкните колонку **тома** в колонке пулы ресурсов. 

    ![Переход к томам](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2.  Чтобы создать том щелкните **+ Добавить том**.  
    Откроется окно Создание тома.

3.  В окне Создание тома щелкните **создать** и укажите сведения для следующих полей:   
    * **Имя тома**      
        Укажите имя создаваемого тома.   

        Имя тома должно быть уникальным в пределах каждого пула емкости. Длина имени должна быть не менее трех знаков. Можно использовать любой алфавитно-цифровой символ.   

        Нельзя использовать `default` в качестве имени тома.

    * **Пул ресурсов**  
        Укажите пул ресурсов, в котором нужно создать том.

    * **Квота**  
        Укажите объем логического хранилища, выделенный для тома.  

        В поле **Доступная квота** отображается объем неиспользуемого пространства выбранного пула емкости, которое можно использовать для создания нового тома. Размер нового тома не должен превышать доступную квоту.  

    * **Виртуальная сеть**  
        Укажите Виртуальную сеть Azure, из которой будет осуществляться доступ к тому.  

        В указанной виртуальной сети должна быть подсеть, делегированная службе Azure NetApp Files. Доступ к службе Azure NetApp Files можно получить только из той же виртуальной сети либо из виртуальной сети, которая находится в том же расположении, что и том, через пиринговую связь. Вы также можете получить доступ к тому из локальной сети через Express Route.   

    * **Подсеть**  
        Укажите подсеть, которую нужно использовать для тома.  
        Указанная подсеть должна быть делегирована службе Azure NetApp Files. 
        
        Если вы не делегировали подсеть, вы можете щелкнуть **Создать** на странице "Создать том". Затем на странице "Создание подсети" укажите сведения о подсети и выберите **Microsoft.NetApp/volumes**, чтобы делегировать подсеть службе Azure NetApp Files. В каждой виртуальной сети можно делегировать только одну подсеть в Azure NetApp Files.   
 
        ![Создание тома](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Создание подсети](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Нажмите кнопку **Протокол**, а затем для типа протокола тома выберите **NFS**.   
    * Укажите **путь к файлу** , который будет использоваться для создания пути экспорта для нового тома. Путь экспорта используется для подключения и получения доступа к тому.

        Имя пути к файлу может содержать только буквы, цифры и дефисы. Длина имени находится в диапазоне от 16 до 40 знаков. 

        Путь к файлу должен быть уникальным в пределах каждой подписки и каждого региона. 

    * При необходимости [Настройте политику экспорта для тома NFS](azure-netapp-files-configure-export-policy.md) .

    ![Указание протокола NFS](../media/azure-netapp-files/azure-netapp-files-protocol-nfs.png)

5. Щелкните **проверить и создать** , чтобы проверить сведения о томе.  Затем нажмите кнопку **создать** , чтобы создать том NFS.

    Созданный том появится на странице тома. 
 
    От пула емкости том наследует атрибуты подписки, группы ресурсов и расположения. Состояние развертывания можно отслеживать на вкладке уведомлений.


## <a name="next-steps"></a>Следующие шаги  

* [Подключение или отключение тома для виртуальных машин Windows или Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Configure export policy for an NFS volume](azure-netapp-files-configure-export-policy.md) (Настройка политики экспорта для тома NFS)
* [Ограничения ресурсов для службы Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Узнайте об интеграции виртуальной сети для служб Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
