---
title: Рекомендации по публикации шаблона решения "приложения Azure" | Azure Marketplace
description: В этой статье описываются требования к публикации шаблона приложения в Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 6533fa930716552c91fffd13b196bdbf78158816
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82084861"
---
# <a name="azure-applications-solution-template-offer-publishing-requirements"></a>Приложения Azure. требования к публикации предложения шаблона решения

В этой статье описываются требования к типу предложения шаблона решения. это один из способов публикации предложения приложения Azure в Azure Marketplace. Для типа предложения шаблона решения требуется [шаблон Azure Resource Manager (шаблон ARM)](../azure-resource-manager/templates/overview.md) для автоматического развертывания инфраструктуры решения.

Используйте шаблон решения для приложений Azure, если требуются следующие условия.

- Для решения требуется дополнительная автоматизация развертывания и настройки за пределами одной виртуальной машины, например сочетание виртуальных машин, сети и ресурсов хранения.
- Клиент собирается самостоятельно управлять самим решением.

Обращение к действию, которое видит пользователь для этого типа предложения, — «получить сейчас».

## <a name="requirements-for-solution-template-offers"></a>Требования к предложениям шаблонов решений

| **Requirements** | **Сведения**  |
| ---------------  | -----------  |
|Выставление счетов и ценообразование    |  Предложения шаблонов решений не являются предложениями Transact, но их можно использовать для развертывания платных предложений виртуальных машин за счет использования коммерческого рынка Майкрософт. Ресурсы, развертываемые с помощью шаблона ARM решения, будут подготовлены в подписке Azure клиента. Виртуальные машины с оплатой по мере использования (ПАЙГО) будут транзакционно работать с клиентом через Майкрософт через подписку Azure клиента.<br/> Хотя в случае использования собственной лицензии (BYOL) корпорация Майкрософт будет выставлять счета на расходы на инфраструктуру по подписке клиента, вы будете напрямую выставлять клиенту счета на лицензирование ПО.   |
|Совместимый с Azure виртуальный жесткий диск (VHD)  |   Виртуальные машины должны быть созданы на платформе Windows или Linux. Дополнительные сведения можно найти в разделе <ul> <li>[Создание предложения приложения Azure](./partner-center-portal/create-new-azure-apps-offer.md)(для виртуальных жестких дисков Windows)</li><li>[Дистрибутивы Linux, одобренные в Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) (для виртуальных жестких дисков Linux).</li></ul> |
| Определение потребления услуг клиентами | Определение потребления услуг клиентами требуется включить для всех шаблонов решений, публикуемых в Azure Marketplace. См. дополнительные сведения об [определении потребления услуг клиентами партнеров Azure](./azure-partner-customer-usage-attribution.md).  |
| Используйте управляемые диски | По умолчанию для сохраненных дисков виртуальных машин IaaS в Azure используется параметр [управляемые диски](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) . В шаблонах решений необходимо использовать управляемые диски. <br> <br> 1. Следуйте [указаниям](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments) и [примерам](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md) использования управляемых дисков в шаблонах Azure ARM для обновления шаблонов решений. <br> <br> 2. выполните приведенные ниже инструкции, чтобы импортировать базовый виртуальный жесткий диск управляемых дисков в учетную запись хранения, чтобы опубликовать VHD как образ в Marketplace: <br> <ul> <li> [PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [CLI](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>Дальнейшие шаги

- Если вы еще не сделали этого, [Ознакомьтесь](https://azuremarketplace.microsoft.com/sell) с Azure Marketplace.
- [Войдите в центр партнеров](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) , чтобы создать или завершить свое предложение.
- [Создайте предложение приложения Azure](./partner-center-portal/create-new-azure-apps-offer.md) для получения дополнительных сведений.
