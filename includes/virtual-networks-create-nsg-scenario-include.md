---
title: включение файла
description: включение файла
services: virtual-network
author: rockboyfor
ms.service: virtual-network
ms.topic: include
origin.date: 04/13/2018
ms.date: 06/11/2018
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 588aa260f2ece543445bfd4da7ef4682dab8334c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60309969"
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
