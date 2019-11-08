---
title: Рекомендации по публикации шаблона решения "приложения Azure" | Azure Marketplace
description: В этой статье описываются требования к публикации шаблона приложения в Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: ellacroi
manager: nunoc
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 9/25/2019
ms.author: ellacroi
ms.openlocfilehash: 934a5e050e190c9a1f90bb3a22c2d1323a3ccecf
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73808297"
---
# <a name="azure-applications-solution-template-offer-publishing-guide"></a>Руководство по публикации предложения "Приложения Azure: шаблон решения Azure"

Шаблоны решений — это один из основных способов публикации решения в Azure Marketplace. Сведения в этом руководстве помогут понять требования к этому предложению. 

Тип предложения "Приложение Azure: шаблон решения" используется, если в решении требуются дополнительные возможности автоматизации развертывания и настройки, недоступные в обычной виртуальной машине. Вы можете автоматизировать подготовку одной или нескольких виртуальных машин с помощью шаблонов решения для приложений Azure. Вы также можете подготавливать ресурсы сети и хранилища. Тип предложения "Приложения Azure: шаблоны решений" предоставляет преимущества автоматизации для отдельных виртуальных машин и целых решений на основе IaaS.

Эти шаблоны решений не являются предложениями по транзакциям, но их можно использовать для развертывания платных предложений виртуальных машин за счет использования коммерческого рынка корпорации Майкрософт. Пользователь видит призыв к действию — "Получить сейчас".


## <a name="requirements-for-solution-templates"></a>Требования к шаблонам решений

| **Требования** | **Дополнительные сведения**  |
| ---------------  | -----------  |
|Выставление счетов и ценообразование    |  Ресурсы будут подготовлены в подписке Azure клиента. Виртуальные машины с оплатой по мере использования (ПАЙГО) будут транзакционно работать с клиентом через корпорацию Майкрософт через подписку Azure клиента (ПАЙГО).  <br/> Хотя в случае использования собственной лицензии (BYOL) корпорация Майкрософт будет выставлять счета на расходы на инфраструктуру по подписке клиента, вы будете напрямую выставлять клиенту счета на лицензирование ПО.   |
|Совместимый с Azure виртуальный жесткий диск (VHD)  |   Виртуальные машины должны быть созданы на платформе Windows или Linux.  См. дополнительные сведения о [создании совместимых с Azure виртуальных жестких дисков](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md). |
| Определение потребления услуг клиентами | Определение потребления услуг клиентами требуется включить для всех шаблонов решений, публикуемых в Azure Marketplace. См. дополнительные сведения об [определении потребления услуг клиентами партнеров Azure](./azure-partner-customer-usage-attribution.md).  |
| Используйте управляемые диски | По умолчанию для сохраненных дисков виртуальных машин IaaS в Azure используется параметр [управляемые диски](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) . В шаблонах решений необходимо использовать управляемые диски. <br> <br> 1. Следуйте [указаниям](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments) и [примерам](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md) использования управляемых дисков в шаблонах Azure ARM для обновления шаблонов решений. <br> <br> 2. выполните приведенные ниже инструкции, чтобы импортировать базовый виртуальный жесткий диск управляемых дисков в учетную запись хранения, чтобы опубликовать VHD как образ в Marketplace: <br> <ul> <li> [PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [ИНТЕРФЕЙС КОМАНДНОЙ СТРОКИ](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>Дальнейшие действия
Если вы еще не сделали это, [зарегистрируйтесь](https://azuremarketplace.microsoft.com/sell) в Marketplace.

Если вы зарегистрированы и создаете предложение или работаете над существующим, войдите на [Портал Cloud Partner](https://cloudpartner.azure.com), чтобы завершить процесс.
