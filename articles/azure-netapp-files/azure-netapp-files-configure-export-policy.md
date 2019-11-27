---
title: Настройка политики экспорта для тома NFS с помощью Azure NetApp Files | Документация Майкрософт
description: Описание настройки политики экспорта для управления доступом к тому NFS с помощью Azure NetApp Files
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
ms.date: 10/18/2019
ms.author: b-juche
ms.openlocfilehash: d323bd0b9684cfe4930d8c779a6728fcfd3836fb
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2019
ms.locfileid: "72674929"
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

    * **Access**  
        Выберите одно из следующих типов доступа.  
        * Нет доступа 
        * Чтение и запись
        * Только чтение

    ![Экспортировать политику](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 


## <a name="next-steps"></a>Дополнительная информация 
* [Управление томами](azure-netapp-files-manage-volumes.md)
* [Подключение и отключение тома для виртуальных машин](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Управление моментальными снимками](azure-netapp-files-manage-snapshots.md)
