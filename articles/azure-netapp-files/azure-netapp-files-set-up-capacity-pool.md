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
ms.topic: get-started-article
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: 55a1d16ce1617ecf7bc28c7c62de8557ceeea311
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53412917"
---
# <a name="set-up-a-capacity-pool"></a>Настройка пула емкости
Настройка пула емкости позволяет создавать в нем тома.  

## <a name="before-you-begin"></a>Перед началом работы 
Должна быть создана учетная запись NetApp.   

[Создание учетной записи NetApp](azure-netapp-files-create-netapp-account.md)

## <a name="steps"></a>Действия 

1. Перейдите в колонку управления учетной записью NetApp, а затем в навигационной панели выберите **Пулы емкости**.

2. Нажмите **+ Добавить пулы** для создания нового пула емкости.   
    Появится окно New Capacity Pool (Новый пул емкости).

3. Укажите следующую информацию для нового пула емкости.  
  * **Имя**  
    Придумайте название для пула емкости.  
    Название пула емкости должно быть уникальным для каждой учетной записи NetApp.

  * **Уровень службы**   
    В этом поле отображается целевая производительность пула емкости.  
    В настоящее время доступен только уровень службы "Премиум". 

  *  **Размер**      
      Укажите приобретаемый размер пула емкости.        
      Минимальный размер пула емкости равен 4 ТиБ. Можно создавать пул с размером, кратным 4 ТиБ.   
      
      ![Новый пул емкости](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. Последовательно выберите **ОК**.

## <a name="next-steps"></a>Дополнительная информация 

[Делегирование подсети в Azure NetApp Files](azure-netapp-files-delegate-subnet.md)


