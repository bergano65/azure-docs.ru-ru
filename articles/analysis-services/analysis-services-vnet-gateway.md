---
title: Нанастройка служб анализа Azure для источников данных VNet Документы Майкрософт
description: Узнайте, как настроить сервер аналитических служб Azure для использования шлюза для источников данных в виртуальной сети Azure (VNet).
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 384f4c2ee3021678c530aa5c98eaf4dbfd661e95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572276"
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>Использование шлюза для источников данных в виртуальной сети Azure

В этой статье описывается служба анализа Azure **AlwaysUseGateway** сервера свойства для использования, когда источники данных находятся на [Azure Виртуальной сети (VNet)](../virtual-network/virtual-networks-overview.md).

## <a name="server-access-to-vnet-data-sources"></a>Доступ сервера к источникам данных виртуальной сети

Если доступ к источникам данных осуществляется через виртуальную сеть, сервер служб Azure Analysis Services должен подключаться к этим источникам данных так, как если бы они размещались в вашей локальной среде. Вы можете настроить свойство сервера **AlwaysUseGateway,** чтобы указать сервер для доступа ко всем источникам данных через [шлюз On-premises.](analysis-services-gateway.md) 

Источники данных управляемых экземпляров данных Azure S'L, управляемые экземплярами, работают в Azure VNet с закрытым IP-адресом. Если общедоступный конечный пункт включен на экземпляре, шлюз не требуется. Если общелокт не включен, требуется шлюз данных, и свойство AlwaysUseGateway должно быть установлено на верность.

> [!NOTE]
> Это свойство эффективно только при установке и настройке [шлюза данных.](analysis-services-gateway.md) Шлюз может находиться в виртуальной сети.

## <a name="configure-alwaysusegateway-property"></a>Настройка свойства AlwaysUseGateway

1. В SSMS > сервер> **Свойства** > **Общие**, выберите **Показать Расширенный (Все) Свойства**.
2. Для параметра **ASPaaS\AlwaysUseGateway** выберите **true**.

    ![Свойство AlwaysUseGateway](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>См. также
[Подключение к источникам данных на месте](analysis-services-gateway.md)   
[Установка и настройка шлюза данных](analysis-services-gateway-install.md)   
[Виртуальная сеть Azure](../virtual-network/virtual-networks-overview.md)   

