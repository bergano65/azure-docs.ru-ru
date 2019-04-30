---
title: Настройка политики экспорта для тома NFS, использующих службу файлов Azure NetApp | Документация Майкрософт
description: Описывается, как настроить политику экспорта для управления доступом на том NFS, использующих службу файлов Azure NetApp
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
ms.date: 03/20/2019
ms.author: b-juche
ms.openlocfilehash: 8cda5921a1aec86d28beabbd9cea5b07a203a0e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61086171"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>Настройка политики экспорта для тома NFS

При необходимости можно настроить политику экспорта для управления доступом к тому Azure NetApp Files. Экспорт политики поддерживается только для тома NFS. 

## <a name="steps"></a>Действия 

1.  Нажмите колонку **Создать политику экспорта** в колонке "Управление томом". 

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

    * **Протоколы**   
        Укажите протокол, используемый для экспорта политики.   
        В настоящее время политики экспорта NetApp службы файлов Azure поддерживает только NFSv3.

    ![Экспортировать политику](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 


## <a name="next-steps"></a>Дальнейшие действия 
* [Управление томами](azure-netapp-files-manage-volumes.md)
* [Подключение и отключение тома для виртуальных машин](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Управление моментальными снимками](azure-netapp-files-manage-snapshots.md)
