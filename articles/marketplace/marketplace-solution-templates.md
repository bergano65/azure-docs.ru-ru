---
title: Лазурные приложения Решения Шаблон Предложение Предложение Издательский руководство (ru) Лазурный рынок
description: В этой статье описываются требования к публикации шаблона приложения в Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: faff12adbf6c0f88f3161e1e4f2760da3b7966ca
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687518"
---
# <a name="azure-applications-solution-template-offer-publishing-guide"></a>Руководство по публикации предложения "Приложения Azure: шаблон решения Azure"

Шаблоны решений — это один из основных способов публикации решения в Azure Marketplace. Сведения в этом руководстве помогут понять требования к этому предложению. 

Тип предложения "Приложение Azure: шаблон решения" используется, если в решении требуются дополнительные возможности автоматизации развертывания и настройки, недоступные в обычной виртуальной машине. Вы можете автоматизировать подготовку одной или нескольких виртуальных машин с помощью шаблонов решения для приложений Azure. Вы также можете подготавливать ресурсы сети и хранилища. Тип предложения "Приложения Azure: шаблоны решений" предоставляет преимущества автоматизации для отдельных виртуальных машин и целых решений на основе IaaS.

Эти шаблоны решений не являются предложениями транзакций, но могут быть использованы для развертывания платных VM-предложений, выставленных на коммерческом рынке Корпорации Майкрософт. Пользователь видит призыв к действию — "Получить сейчас".


## <a name="requirements-for-solution-templates"></a>Требования к шаблонам решений

| **Требования** | **Сведения**  |
| ---------------  | -----------  |
|Выставление счетов и ценообразование    |  Ресурсы будут выделены в подписку на Azure клиента. Виртуальные машины с оплатой по мере использования (PAYGO) будут осуществляться с клиентом через корпорацию Майкрософт, выставленные по подписке клиента Azure (PAYGO).  <br/> Хотя в случае использования собственной лицензии (BYOL) корпорация Майкрософт будет выставлять счета на расходы на инфраструктуру по подписке клиента, вы будете напрямую выставлять клиенту счета на лицензирование ПО.   |
|Совместимый с Azure виртуальный жесткий диск (VHD)  |   Виртуальные машины должны быть созданы на платформе Windows или Linux.  Для получения дополнительной информации [см.](./partner-center-portal/create-new-azure-apps-offer.md) |
| Определение потребления услуг клиентами | Определение потребления услуг клиентами требуется включить для всех шаблонов решений, публикуемых в Azure Marketplace. См. дополнительные сведения об [определении потребления услуг клиентами партнеров Azure](./azure-partner-customer-usage-attribution.md).  |
| Используйте управляемые диски | [Управляемые диски](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) — это опция по умолчанию для упорных дисков IaaS VMs в Azure. Управляемые диски необходимо использовать в шаблонах решений. <br> <br> 1. Следуйте [рекомендациям](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments) и [примерам](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md) использования управляемых дисков в шаблонах Azure ARM для обновления шаблонов решений. <br> <br> 2. Следуйте инструкциям ниже импортировать базовый VHD управляемых дисков на учетную запись хранения для публикации VHD в качестве изображения в Marketplace: <br> <ul> <li> [PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [CLI](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>Следующие шаги

Если это еще не сделано, 

- [Узнайте](https://azuremarketplace.microsoft.com/sell) больше о рынке.

Чтобы зарегистрироваться в Партнерс-центре, начните создавать новое предложение или работать над существующим:

- [Войдите в Центр Партнеров,](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) чтобы создать или завершить свое предложение.
- Для получения дополнительной информации можно ознакомиться с [предложением приложения Azure.](./partner-center-portal/create-new-azure-apps-offer.md)

