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
ms.date: 02/15/2019
ms.author: b-juche
ms.openlocfilehash: af3738849382317eeddf8bce3d2f87e38e0fb583
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56427802"
---
# <a name="set-up-a-capacity-pool"></a>Настройка пула емкости

Настройка пула емкости позволяет создавать в нем тома.  

## <a name="before-you-begin"></a>Перед началом работы 

Должна быть создана учетная запись NetApp.   

[Создание учетной записи NetApp](azure-netapp-files-create-netapp-account.md)

## <a name="steps"></a>Действия 

1. Перейдите в колонку управления учетной записью NetApp, а затем в навигационной панели выберите **Пулы емкости**.  
    
    ![Переход к пулам емкости](../media/azure-netapp-files/azure-netapp-files-navigate-to-capacity-pool.png)

2. Нажмите **+ Добавить пулы** для создания нового пула емкости.   
    Появится окно New Capacity Pool (Новый пул емкости).

3. Укажите следующую информацию для нового пула емкости.  
  * **Имя**  
    Придумайте название для пула емкости.  
    Название пула емкости должно быть уникальным для каждой учетной записи NetApp.

  * **Уровень службы**   
    В этом поле отображается целевая производительность пула емкости.  
    Выберите уровень обслуживания для пула емкости: [**"Премиум"**](azure-netapp-files-service-levels.md#Premium) или [**"Стандартный"**](azure-netapp-files-service-levels.md#Standard).

  * **Размер**      
    Укажите приобретаемый размер пула емкости.        
    Минимальный размер пула емкости равен 4 ТиБ. Можно создавать пул с размером, кратным 4 ТиБ.   
      
    ![Новый пул емкости](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. Последовательно выберите **ОК**.

## <a name="next-steps"></a>Дополнительная информация 

- [Service levels for Azure NetApp Files](azure-netapp-files-service-levels.md) (Уровни обслуживания для для службы Azure NetApp Files)
- Сведения о ценах на разные уровни обслуживания см. на странице [Цены на службу Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/)
- [Делегирование подсети службе Azure NetApp Files](azure-netapp-files-delegate-subnet.md)
