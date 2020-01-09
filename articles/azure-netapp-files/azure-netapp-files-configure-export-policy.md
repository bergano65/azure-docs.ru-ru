---
title: Настройка политики экспорта для тома NFS — Azure NetApp Files
description: Описание настройки политики экспорта для управления доступом к тому NFS с помощью Azure NetApp Files
services: azure-netapp-files
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: b96fca3a5627a1c6c96c8db5c1c209a51c5e102a
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/31/2019
ms.locfileid: "75551564"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>Настройка политики экспорта для тома NFS

При необходимости можно настроить политику экспорта для управления доступом к тому Azure NetApp Files. Azure NetApp Files политика экспорта поддерживает только тома NFS.  Поддерживаются NFSv3 и NFSv4. 

## <a name="steps"></a>Действия 

1.  Нажмите кнопку **Экспорт политики** в области навигации Azure NetApp Files. 

2.  Чтобы создать правило политики экспорта, заполните следующие поля.   
    *  **Индекс**   
        Укажите номер индекса для правила.  
        Политика экспорта может содержать до пяти правил. Правила вычисляются в соответствии с их номером индекса в списке. Правила с более низкими номерами индекса вычисляются первыми. Например, сначала вычисляется правило с номером индекса 1, а затем правило с номером индекса 2. 

    * **Разрешенные клиенты**   
        Укажите значение в одном из следующих форматов.  
        * IPv4-адрес, например `10.1.12.24` 
        * IPv4-адрес и маска подсети, выраженная количеством бит, например `10.1.12.10/4`

    * **Доступ**  
        Выберите одно из следующих типов доступа.  
        * Нет доступа 
        * Чтение и запись
        * Только для чтения

    ![Экспортировать политику](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 


## <a name="next-steps"></a>Дальнейшие действия 
* [Управление томами](azure-netapp-files-manage-volumes.md)
* [Подключение и отключение тома для виртуальных машин](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Управление моментальными снимками](azure-netapp-files-manage-snapshots.md)
