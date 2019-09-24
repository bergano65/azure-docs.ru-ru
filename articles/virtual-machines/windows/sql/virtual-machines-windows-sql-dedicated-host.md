---
title: SQL Server виртуальную машину на выделенном узле Azure
description: Сведения о запуске SQL Server виртуальной машины на выделенном узле Azure.
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
ms.openlocfilehash: 8b29bbce1511b022def522d46c74b99967a76ea3
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/23/2019
ms.locfileid: "71204527"
---
# <a name="sql-server-vm-on-an-azure-dedicated-host"></a>SQL Server виртуальную машину на выделенном узле Azure 

В этой статье подробно описаны особенности использования SQL Server виртуальной машины с [выделенным узлом Azure](/azure/virtual-machines/windows/dedicated-hosts). Дополнительные сведения о выделенном узле Azure можно найти в записи блога Введение в [выделенный узел Azure](https://azure.microsoft.com/blog/introducing-azure-dedicated-host/). 

## <a name="overview"></a>Обзор
[Выделенный узел Azure](/azure/virtual-machines/windows/dedicated-hosts) — это служба, которая предоставляет физические серверы — возможность размещения одной или нескольких виртуальных машин, выделенных для одной подписки Azure. Выделенные узлы — это те же физические серверы, которые используются в центрах обработки данных Майкрософт, предоставляемые в качестве ресурсов. Вы можете подготавливать выделенные узлы в регионе, зоне доступности и домене сбоя. Затем можно разместить виртуальные машины непосредственно в подготовленных узлах в любой конфигурации, которая лучше всего соответствует вашим потребностям.


[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-common-dedicated-hosts-preview.md)]


## <a name="licensing"></a>Лицензирование

При добавлении виртуальной машины SQL Server в выделенный узел Azure можно выбрать один из двух различных вариантов лицензирования. 

  - **Лицензирование виртуальной машины SQL**: Это существующий вариант лицензирования, где вы платите за каждую SQL Server лицензию на виртуальную машину по отдельности. 
  - **Лицензирование выделенного узла**: Новая модель лицензирования, доступная для выделенного узла Azure, где SQL Server лицензии объединены и оплачиваются на уровне узла. 


Параметры уровня узла для использования существующих лицензий SQL Server: 
  - Преимущество гибридного использования Azure выпуска SQL Server Enterprise
    - Доступно клиентам с SA или подпиской.
    - Лицензирование всех доступных физических ядер и использование неограниченной виртуализации (вплоть до максимума виртуальных ЦП, поддерживаемого узлом).
        - Дополнительные сведения о применении Преимущество гибридного использования Azure к выделенному узлу Azure см. в разделе [часто задаваемые вопросы о преимущество гибридного использования Azure](https://azure.microsoft.com/pricing/hybrid-benefit/faq/). 
  - Лицензии SQL Server, полученные до 1 октября
      - Выпуск SQL Server Enterprise Edition имеет параметры лицензии на уровне узла и на виртуальную машину. 
      - Для SQL Server Standard Edition доступен только вариант лицензия по виртуальной машине. 
          - Дополнительные сведения см. в разделе [условия продуктов корпорации Майкрософт](https://www.microsoft.com/licensing/product-licensing/products). 
  - Если не выбран параметр SQL Server выделенный уровень узла, то SQL Server АХБ можно выбрать на уровне отдельных виртуальных машин, как и для виртуальных машин с несколькими клиентами.



## <a name="provisioning"></a>Идет подготовка  
Подготовка SQL Server виртуальной машины к выделенному узлу не отличается от любой другой виртуальной машины Azure. Это можно сделать с помощью [Azure PowerShell](../dedicated-hosts-powershell.md), [портал Azure](../dedicated-hosts-portal.md)и [Azure CLI](../../linux/dedicated-hosts-cli.md).

Процесс добавления существующей SQL Server виртуальной машины в выделенный узел требует простоя, но не влияет на данные и не будет содержать потери данных. Тем не менее, перед перемещением необходимо создать резервную копию всех баз данных, включая системные базы данных.

## <a name="virtualization"></a>Виртуализация 

Одним из преимуществ выделенного узла является неограниченная виртуализация. Например, у вас могут быть лицензии на 64 виртуальных ядер, но можно настроить узел на наличие 128 виртуальных ядер, поэтому вы получаете удвоенный виртуальных ядер, но платите только за половину SQL Server лицензий. 

Так как это ваш узел, вы можете задать для виртуализации соотношение 1:2. 

## <a name="faq"></a>Часто задаваемые вопросы

**Вопрос. Как Преимущество гибридного использования Azure работает для лицензий Windows Server/SQL Server на выделенном узле Azure?**

Ответ. Клиенты могут использовать значения существующих лицензий Windows Server и SQL Server с лицензией Software Assurance или подходящих лицензий на подписку, чтобы заплатить по сниженному тарифу на выделенный узел Azure с помощью Преимущество гибридного использования Azure. Клиенты Windows Server Datacenter и SQL Server Enterprise Edition получают неограниченную виртуализацию (развертывать как можно больше виртуальных машин Windows Server на узле в соответствии с физической емкостью базового сервера), когда они производят лицензию на весь узел. и используйте Преимущество гибридного использования Azure.  Все рабочие нагрузки Windows Server и SQL Server на выделенном узле Azure также подходят для расширенных обновлений системы безопасности для Windows Server и SQL Server 2008/R2 без дополнительной платы. 

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения см. в следующих статьях: 

* [Обзор SQL Server на виртуальных машинах Windows](virtual-machines-windows-sql-server-iaas-overview.md).
* [Часто задаваемые вопросы о SQL Server на виртуальной машине Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Руководство по ценам для SQL Server на виртуальной машине Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Заметки о выпуске для SQL Server на виртуальной машине Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


