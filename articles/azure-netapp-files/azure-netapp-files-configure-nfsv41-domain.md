---
title: Налаживать домен NFSv4.1 по умолчанию для файлов NetApp Azure (ru) Документы Майкрософт
description: Описывает, как настроить клиент NFS для использования NFSv4.1 с azure NetApp Files.
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
ms.openlocfilehash: 77178a23206eadae941794c92b8dd99fe2ca1e05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73906290"
---
# <a name="configure-nfsv41-default-domain-for-azure-netapp-files"></a>Настройка домена по умолчанию NFSv4.1 для Azure NetApp Files

NFSv4 вводит понятие домена аутентификации. В настоящее время Azure NetApp Files поддерживает отображение пользователей только для корней от службы до клиента NFS. Чтобы использовать функциональность NFSv4.1 с помощью файлов Azure NetApp, необходимо обновить клиент NFS.

## <a name="default-behavior-of-usergroup-mapping"></a>Поведение пользователя/группы по умолчанию

Корневая картографирование по умолчанию `nobody` для пользователя, `localdomain`потому что домен NFSv4 установлен на . При установке объема Azure NetApp Files NFSv4.1 в качестве корня вы увидите разрешения файлов следующим образом:  

![Поведение пользователя/группы по умолчанию для NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-default-behavior-user-group-mapping.png)

Как показано выше приведенное `file1` в `root`приведенном примере, пользователь должен быть, но он карты по `nobody` умолчанию.  В этой статье показано, `file1` как `root`настроить пользователя на .  

## <a name="steps"></a>Шаги 

1. Отображай `/etc/idmapd.conf` файл на клиенте NFS.   
    Не комментируйте `#Domain` строку (то `#` есть удалите из `localdomain` `defaultv4iddomain.com`строки) и измените значение на . 

    Первоначальная конфигурация: 
    
    ![Первоначальная конфигурация для NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-initial-config.png)

    Обновленная конфигурация:
    
    ![Обновленная конфигурация для NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-updated-config.png)

2. Открепите все в настоящее время установленные объемы NFS.
3. Обновите файл `/etc/idmapd.conf`.
4. Перезапустите `rpcbind` службу на`service rpcbind restart`хосте (), или просто перезагрузите усте.
5. Смонтировать объемы NFS по мере необходимости.   

    Смотрите [маунт или открепить громкость для виртуальных машин Windows или Linux.](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) 

Следующий пример показывает полученное изменение пользователя/группы: 

![Резкая конфигурация для NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-resulting-config.png)

Как показано на примере, пользователь/группа `nobody` `root`теперь изменилась с .

## <a name="behavior-of-other-non-root-users-and-groups"></a>Поведение других (некорневых) пользователей и групп

Azure NetApp Files поддерживает местных пользователей (пользователей, созданных локально на хосте), у которых есть разрешения, связанные с файлами или папками в томах NFSv4.1. Однако в настоящее время служба не поддерживает отображение пользователей/групп в нескольких узлах. Таким образом, пользователи, созданные на одном узеле, не отображают по умолчанию карту для пользователей, созданных на другом узеле. 

В следующем примере `Host1` имеется три`testuser01`существующих `testuser02` `testuser03`учетных записи тестовых пользователей (, , 

![Резкая конфигурация для NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-host1-users.png)

На `Host2`, обратите внимание, что учетные записи тестпользователя не были созданы, но тот же объем устанавливается на обоих узлах:

![Резкая конфигурация для NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-host2-users.png)

## <a name="next-step"></a>Следующий шаг 

[Подключение или отключение тома для виртуальных машин Windows или Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)

