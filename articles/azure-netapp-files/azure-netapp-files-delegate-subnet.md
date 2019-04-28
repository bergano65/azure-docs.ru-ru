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
ms.date: 03/25/2019
ms.author: b-juche
ms.openlocfilehash: fd8e380ad68b86b9ffd0f1e40efde8bdadfb19c5
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763311"
---
# <a name="delegate-a-subnet-to-azure-netapp-files"></a>Делегирование подсети в Azure NetApp Files 

Вам следует делегировать подсеть в Azure NetApp Files.   При создании тома необходимо указать делегированную подсеть.

## <a name="considerations"></a>Рекомендации
* Мастер создания новой подсети по умолчанию настраивает маску сети /24, обеспечивающую 251 доступный IP-адрес. Использование этой маски подсети, которая предоставляет 16 пригодных IP-адресов, достаточно для службы.
* В каждой виртуальной сети Azure можно делегировать только одну подсеть для Azure NetApp Files.
* В делегированной подсети невозможно назначить группу безопасности сети или конечную точку службы. Попытка к сбою делегирования подсети.
* Доступ к тому из глобально пиринговой виртуальной сети в настоящее время не поддерживается.
* Создание [пользовательские настраиваемые маршруты](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes) в подсетях виртуальной Машины с адресом префикс (назначение) в подсети, делегировать NetApp службы файлов Azure не поддерживается. Это повлияет на подключение виртуальной Машины.

## <a name="steps"></a>Действия 
1.  Перейдите к колонке **Виртуальные сети** на портале Azure и выберите виртуальную сеть, которую нужно использовать для Azure NetApp Files.    

1. Выберите **Подсети** из колонки виртуальной сети и нажмите кнопку **+Подсеть**. 

1. Создайте новую подсеть для использования в Azure NetApp Files, заполнив следующие обязательные поля на странице добавления подсети:
    * **Имя**. Укажите имя подсети.
    * **Диапазон адресов**. Укажите диапазон IP-адресов.
    * **Делегирование подсети**. Выберите **Microsoft.NetApp/volumes**. 

      ![Делегирование подсети](../media/azure-netapp-files/azure-netapp-files-subnet-delegation.png)
    
Можно также создать и делегировать подсеть при [создании тома для Azure NetApp Files](azure-netapp-files-create-volumes.md). 

## <a name="next-steps"></a>Дальнейшие действия  
* [Создание тома для Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Узнайте об интеграции виртуальной сети для служб Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)


