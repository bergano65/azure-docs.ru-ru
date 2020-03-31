---
title: Настройка пула емкости для Azure NetApp Files | Документация Майкрософт
description: Описание настройки пула емкости, чтобы иметь возможность создавать в нем тома.
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
ms.date: 02/15/2019
ms.author: b-juche
ms.openlocfilehash: 8f50b2ad34c705c8d3831d8243f136c41d750dc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60691104"
---
# <a name="set-up-a-capacity-pool"></a>Настройка пула емкости

Настройка пула емкости позволяет создавать в нем тома.  

## <a name="before-you-begin"></a>Перед началом 

Должна быть создана учетная запись NetApp.   

[Создание учетной записи NetApp](azure-netapp-files-create-netapp-account.md)

## <a name="steps"></a>Шаги 

1. Перейдите в колонку управления учетной записью NetApp, а затем в навигационной панели выберите **Пулы емкости**.  
    
    ![Переход к пулам емкости](../media/azure-netapp-files/azure-netapp-files-navigate-to-capacity-pool.png)

2. Нажмите **+ Добавить пулы** для создания нового пула емкости.   
    Появится окно New Capacity Pool (Новый пул емкости).

3. Укажите следующую информацию для нового пула емкости.  
   * **Название**  
     Придумайте название для пула емкости.  
     Название пула емкости должно быть уникальным для каждой учетной записи NetApp.

   * **Уровень обслуживания**   
     В этом поле отображается целевая производительность пула емкости.  
     Укажите уровень обслуживания для пула емкости: [**Premium**](azure-netapp-files-service-levels.md#Premium) или [**Standard**](azure-netapp-files-service-levels.md#Standard).

   * **Размер**     
     Укажите приобретаемый размер пула емкости.        
     Минимальный размер пула емкости равен 4 ТиБ. Можно создавать пул с размером, кратным 4 ТиБ.   
      
     ![Новый пул емкости](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. Нажмите кнопку **ОК**.

## <a name="next-steps"></a>Дальнейшие действия 

- [Уровни обслуживания для Azure NetApp Files](azure-netapp-files-service-levels.md)
- Сведения о ценах на разные уровни обслуживания см. на странице [Цены на службу Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/)
- [Делегирование подсети службе Azure NetApp Files](azure-netapp-files-delegate-subnet.md)
