---
title: Делегирование подсети в Azure NetApp Files | Документация Майкрософт
description: Сведения о том, как делегировать подсеть в Azure NetApp Files.
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
ms.date: 03/19/2020
ms.author: b-juche
ms.openlocfilehash: b83f530549ffa43789963fd0c95b4982f5289356
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80054467"
---
# <a name="delegate-a-subnet-to-azure-netapp-files"></a>Делегирование подсети в Azure NetApp Files 

Вам следует делегировать подсеть в Azure NetApp Files.   При создании тома необходимо указать делегированную подсеть.

## <a name="considerations"></a>Рекомендации
* Мастер создания новой подсети по умолчанию настраивает маску сети /24, обеспечивающую 251 доступный IP-адрес. Использование этой маски подсети, которая предоставляет 16 пригодных IP-адресов, достаточно для службы.
* В каждой виртуальной сети Azure можно делегировать только одну подсеть для Azure NetApp Files.   
   Azure позволяет создавать несколько делегированных подсетей в VNet.  Однако любые попытки создать новый том не увенчались неудачей, если вы используете несколько делегированных подсетей.
* В делегированной подсети невозможно назначить группу безопасности сети или конечную точку службы. Попытка к сбою делегирования подсети.
* Доступ к объему из глобальной виртуальной сети в настоящее время не поддерживается.
* Создание [пользовательских маршрутов](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes) в подсетях VM с адресной префиксом (назначением) в подсеть, делегированную файлам NetApp Azure, не поддерживается. Это повлияет на подключение VM.

## <a name="steps"></a>Шаги 
1.  Перейдите к колонке **Виртуальные сети** на портале Azure и выберите виртуальную сеть, которую нужно использовать для Azure NetApp Files.    

1. Выберите **Подсети** из колонки виртуальной сети и нажмите кнопку **+Подсеть**. 

1. Создайте новую подсеть для использования в Azure NetApp Files, заполнив следующие обязательные поля на странице добавления подсети:
    * **Имя**: Укажите имя подсети.
    * **Диапазон адресов**: Укажите диапазон IP-адресов.
    * **Делегация subnet**: Выберите **Microsoft.NetApp/тома**. 

      ![Делегирование подсети](../media/azure-netapp-files/azure-netapp-files-subnet-delegation.png)
    
Можно также создать и делегировать подсеть при [создании тома для Azure NetApp Files](azure-netapp-files-create-volumes.md). 

## <a name="next-steps"></a>Дальнейшие действия  
* [Создание тома для Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Узнайте о виртуальной сетевой интеграции для служб Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)


