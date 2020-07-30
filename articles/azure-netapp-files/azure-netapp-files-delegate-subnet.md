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
ms.topic: how-to
ms.date: 07/28/2020
ms.author: b-juche
ms.openlocfilehash: 14c97fdea57fa50faf8b73275ec406ea36fbf552
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87420402"
---
# <a name="delegate-a-subnet-to-azure-netapp-files"></a>Делегирование подсети в Azure NetApp Files 

Вам следует делегировать подсеть в Azure NetApp Files.   При создании тома необходимо указать делегированную подсеть.

## <a name="considerations"></a>Рекомендации
* Мастер создания новой подсети по умолчанию настраивает маску сети /24, обеспечивающую 251 доступный IP-адрес. Для службы достаточно использовать маску сети/28, которая предоставляет 11 пригодных для использования IP-адресов.
* В каждой виртуальной сети Azure можно делегировать только одну подсеть для Azure NetApp Files.   
   Azure позволяет создавать несколько делегированных подсетей в виртуальной сети.  Однако при использовании нескольких делегированных подсетей попытки создания нового тома завершатся ошибкой.  
   В виртуальной сети может быть только одна Делегированная подсеть. Учетная запись NetApp может развертывать тома в нескольких виртуальных сетей, каждый из которых имеет собственную делегированную подсеть.  
* В делегированной подсети невозможно назначить группу безопасности сети или конечную точку службы. Попытка к сбою делегирования подсети.
* Доступ к тому из глобальной одноранговой виртуальной сети в настоящее время не поддерживается.
* Создание [определяемых пользователем пользовательских маршрутов](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes) в ПОДСЕТЯХ виртуальных машин с префиксом адреса (назначением) к подсети, делегированной в Azure NetApp Files, не поддерживается. Это повлияет на подключение к виртуальной машине.

## <a name="steps"></a>Шаги 
1.  Перейдите к колонке **Виртуальные сети** на портале Azure и выберите виртуальную сеть, которую нужно использовать для Azure NetApp Files.    

1. Выберите **Подсети** из колонки виртуальной сети и нажмите кнопку **+Подсеть**. 

1. Создайте новую подсеть для использования в Azure NetApp Files, заполнив следующие обязательные поля на странице добавления подсети:
    * **Имя**: укажите имя подсети.
    * **Диапазон адресов**: укажите диапазон IP-адресов.
    * **Делегирование подсети**. Выберите **Microsoft. NetApp/Volumes**. 

      ![Делегирование подсети](../media/azure-netapp-files/azure-netapp-files-subnet-delegation.png)
    
Можно также создать и делегировать подсеть при [создании тома для Azure NetApp Files](azure-netapp-files-create-volumes.md). 

## <a name="next-steps"></a>Дальнейшие действия  
* [Создание тома для Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Узнайте об интеграции виртуальной сети для служб Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)


