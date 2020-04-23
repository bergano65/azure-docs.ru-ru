---
title: Шаблон решений приложений Azure предлагает руководство по публикации Лазурный рынок
description: В этой статье описываются требования к публикации шаблона приложения в Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 6533fa930716552c91fffd13b196bdbf78158816
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084861"
---
# <a name="azure-applications-solution-template-offer-publishing-requirements"></a>Приложения Azure: шаблон решения предлагает требования к публикации

В этой статье разъясняется требования к типу предложения шаблона решения, который является одним из способов публикации предложения приложения Azure в Azure Marketplace. Тип предложения шаблона решений требует, чтобы [шаблон менеджера ресурсов Azure (ARM template)](../azure-resource-manager/templates/overview.md) автоматически развертывал инфраструктуру решений.

Используйте тип предложения решения приложения Azure, когда требуются следующие условия:

- Решение требует дополнительной автоматизации развертывания и конфигурации за пределами одного VM, например комбинации вс-миноносов, сетей и ресурсов хранения данных.
- Ваш клиент будет управлять решением самостоятельно.

Призыв к действию, который пользователь видит для этого типа предложения, — «Получить сейчас».

## <a name="requirements-for-solution-template-offers"></a>Требования к предложениям шаблона решений

| **Требования** | **Сведения**  |
| ---------------  | -----------  |
|Выставление счетов и ценообразование    |  Предложения шаблонов решений не являются предложениями по сделке, но могут быть использованы для развертывания платных VM-предложений, выставленных на коммерческом рынке Майкрософт. Ресурсы, развертываемые в шаблоне ARM решения, будут выделены в подписке клиента Azure. Виртуальные машины с оплатой по мере использования (PAYGO) будут осуществляться с клиентом через корпорацию Майкрософт, выставленные по подписке клиента Azure.<br/> Хотя в случае использования собственной лицензии (BYOL) корпорация Майкрософт будет выставлять счета на расходы на инфраструктуру по подписке клиента, вы будете напрямую выставлять клиенту счета на лицензирование ПО.   |
|Совместимый с Azure виртуальный жесткий диск (VHD)  |   Виртуальные машины должны быть созданы на платформе Windows или Linux. Дополнительные сведения см. в разделе: <ul> <li>[Создайте предложение приложения Azure](./partner-center-portal/create-new-azure-apps-offer.md)(для Windows VHD)</li><li>[Дистрибутивы Linux одобрены на Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) (для Linux VHD).</li></ul> |
| Определение потребления услуг клиентами | Определение потребления услуг клиентами требуется включить для всех шаблонов решений, публикуемых в Azure Marketplace. См. дополнительные сведения об [определении потребления услуг клиентами партнеров Azure](./azure-partner-customer-usage-attribution.md).  |
| Используйте управляемые диски | [Управляемые диски](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) — это опция по умолчанию для упорных дисков IaaS VMs в Azure. Управляемые диски необходимо использовать в шаблонах решений. <br> <br> 1. Следуйте [рекомендациям](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments) и [примерам](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md) использования управляемых дисков в шаблонах Azure ARM для обновления шаблонов решений. <br> <br> 2. Следуйте инструкциям ниже импортировать базовый VHD управляемых дисков на учетную запись хранения для публикации VHD в качестве изображения в Marketplace: <br> <ul> <li> [PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [CLI](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>Дальнейшие действия

- Если вы еще не сделали этого, [узнайте](https://azuremarketplace.microsoft.com/sell) о Azure Marketplace.
- [Войдите в Центр Партнеров,](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) чтобы создать или завершить свое предложение.
- [Создайте предложение приложения Azure](./partner-center-portal/create-new-azure-apps-offer.md) для получения дополнительной информации.
