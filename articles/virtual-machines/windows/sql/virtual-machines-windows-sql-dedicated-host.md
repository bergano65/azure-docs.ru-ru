---
title: ВМ сервера S'L на специальном хосте Azure
description: Узнайте подробности о запуске VM сервера S'L на специальном хосте Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: edb2d3fa670475d9b08fe05494035949181a9240
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834353"
---
# <a name="sql-server-vm-on-an-azure-dedicated-host"></a>ВМ сервера S'L на специальном хосте Azure 

В этой статье подробно описана специфика использования VM сервера S'L с [выделенным хостом Azure.](/azure/virtual-machines/windows/dedicated-hosts) Дополнительную информацию о лазурном размещенном хосте можно найти в блоге [Представляя Azure Dedicated Host](https://azure.microsoft.com/blog/introducing-azure-dedicated-host/). 

## <a name="overview"></a>Обзор
[Azure Dedicated Host](/azure/virtual-machines/windows/dedicated-hosts) — это служба, которая предоставляет физические серверы, способные размещать одну или несколько виртуальных машин, предназначенную для одной подписки Azure. Выделенные хосты — это те же физические серверы, которые используются в центрах обработки данных Корпорации Майкрософт, предоставляемые в качестве ресурса. Вы можете предоставить выделенные хосты в пределах области, зоны доступности и домена неисправностей. Затем вы можете размещать ВМ непосредственно в подготовленных хостах, в любой конфигурации лучше всего отвечает вашим потребностям.

## <a name="limitations"></a>Ограничения

- Виртуальные наборы масштабов машин в настоящее время не поддерживаются на выделенных хостах.
- Поддерживаются следующие серии VM: DSv3 и ESv3. 

## <a name="licensing"></a>Лицензирование

При добавлении VM сервера S'L в выделенный хост Azure можно выбрать один из двух вариантов лицензирования. 

  - **Лицензирование VM:** Это существующая опция лицензирования, где вы платите за каждую лицензию S'L Server VM индивидуально. 
  - **Выделенное лицензирование хоста**: Новая модель лицензирования, доступная для специального хоста Azure, где лицензии S'L Server объединяются и оплачиваются на уровне хоста. 


Варианты на уровне хоста для использования существующих лицензий S'L Server: 
  - Гибридная выгода для предприятия сервера S'L Server Azure
    - Доступно для клиентов с SA или подпиской.
    - Лицензия все доступные физические ядра и наслаждаться неограниченной виртуализации (до макс vCPUs поддерживается хостом).
        - Для получения дополнительной информации о применении гибридной выгоды [Azure Hybrid Benefit FAQ](https://azure.microsoft.com/pricing/hybrid-benefit/faq/)Azure для размещения Azure, см. 
  - Лицензии сервера S'L, приобретенные до 1 октября
      - Издание S'L Server Enterprise имеет как варианты лицензии на уровне хоста, так и по-VM. 
      - В издании S'L Server Standard имеется только опция лицензии by VM. 
          - Для получения подробной информации [см.](https://www.microsoft.com/licensing/product-licensing/products) 
  - Если не выбрана опция, выделенная на уровень хоста, на уровне отдельных VMs, как и с мультитенантными VMs,.



## <a name="provisioning"></a>Подготовка  
Предоставление VM сервера S'L Server для выделенного узла ничем не отличается от любой другой виртуальной машины Azure. Это можно сделать с помощью [Azure PowerShell,](../dedicated-hosts-powershell.md) [портала Azure](../dedicated-hosts-portal.md)и [Azure CLI.](../../linux/dedicated-hosts-cli.md)

Процесс добавления существующего VM сервера S'L к выделению узла требует простоев, но не повлияет на данные и не будет иметь потери данных. Тем не менее, все базы данных, включая системные базы данных, должны быть подкреплены до переезда.

## <a name="virtualization"></a>Виртуализация 

Одним из преимуществ выделенного хоста является неограниченная виртуализация. Например, вы можете иметь лицензии на 64 vCores, но вы можете настроить узла, чтобы иметь 128 vCores, так что вы получите в два раза vCores, но платите только за половину лицензий сервера S'L. 

Так как это ваш хост, вы имеете право установить виртуализацию с коэффициентом 1:2. 

## <a name="faq"></a>часто задаваемые вопросы

**В: Как работает гибридная льгота Azure для лицензий Windows Server/S'L Server на специальном хосте Azure?**

О: Клиенты могут использовать значение существующих лицензий Windows Server и S'L Server с программными гарантиями или квалификационными лицензиями на подписку, чтобы оплатить сниженную ставку на Azure Dedicated Host с помощью Azure Hybrid Benefit. Клиенты Windows Server Datacenter и S'L Server Enterprise Edition получают неограниченную виртуализацию (развертывание как можно больше виртуальных машин Windows Server на хостах с учетом физической емкости базового сервера), когда они лицензируют весь хост используйте гибридные преимущества Azure.  Все рабочие нагрузки Windows Server и S'L Server в Azure Dedicated Host также имеют право на расширенные обновления безопасности для Windows Server и S'L Server 2008/R2 без дополнительной платы. 

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. в следующих статьях: 

* [Обзор сервера S'L на Windows VM](virtual-machines-windows-sql-server-iaas-overview.md)
* [Часто задаваемые вопросы для сервера S'L на Windows VM](virtual-machines-windows-sql-server-iaas-faq.md)
* [Руководство по ценообразованию для сервера S'L на Windows VM](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Выпуск примечаний для сервера S'L на Windows VM](virtual-machines-windows-sql-server-iaas-release-notes.md)


