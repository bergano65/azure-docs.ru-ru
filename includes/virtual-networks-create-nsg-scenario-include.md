---
title: включение файла
description: включение файла
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 588aa260f2ece543445bfd4da7ef4682dab8334c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67185208"
---
## <a name="scenario"></a>Сценарий
Чтобы лучше проиллюстрировать процесс создания групп безопасности сети, в этом документе будет использоваться представленный ниже сценарий.

![Сценарий виртуальной сети](./media/virtual-networks-create-nsg-scenario-include/figure1.png)

В этом сценарии мы создадим группу безопасности сети для каждой подсети в виртуальной сети **TestVNet**, как описано ниже: 

* **NSG-FrontEnd**. Интерфейсная группа безопасности сети будет применена к подсети *FrontEnd* и будет содержать два правила:    
  * **rdp-rule**. Разрешает трафик по протоколу RDP к подсети *FrontEnd*.
  * **web-rule**. Разрешает трафик по протоколу HTTP к подсети *FrontEnd*.
* **NSG-BackEnd**. Внутренняя группа безопасности сети будет применена к подсети *BackEnd* и будет содержать два правила:    
  * **sql-rule**. Разрешает трафик SQL только из подсети *FrontEnd*.
  * **web-rule**. Запрещает весь трафик из подсети *BackEnd* в Интернет.

Сочетание этих правил образует сценарий, подобный сети периметра, где внутренняя подсеть может получать только входящий трафик для SQL из интерфейсной подсети и не имеет доступа к Интернету, а интерфейсная подсеть может взаимодействовать с Интернетом и получать только входящие запросы HTTP.

