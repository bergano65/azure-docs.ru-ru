---
title: Использование локального шлюза данных для источников данных виртуальной сети Azure | Документация Майкрософт
description: Узнайте, как настроить сервер, чтобы использовать шлюз для источников данных в виртуальной сети.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: d1993996c0f982624d6976b50871fa8c4592e2f6
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301220"
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>Использование шлюза для источников данных в виртуальной сети Azure

В этой статье описывается свойство сервера **AlwaysUseGateway**, которое следует использовать при размещении источников данных в [виртуальной сети Azure](../virtual-network/virtual-networks-overview.md).

## <a name="server-access-to-vnet-data-sources"></a>Доступ сервера к источникам данных виртуальной сети

Если доступ к источникам данных осуществляется через виртуальную сеть, сервер служб Azure Analysis Services должен подключаться к этим источникам данных так, как если бы они размещались в вашей локальной среде. Можно настроить свойство сервера **AlwaysUseGateway** , чтобы указать сервер для доступа ко всем источникам данных через [локальный шлюз](analysis-services-gateway.md). 

Управляемый экземпляр Базы данных SQL Azure источники данных выполняются в виртуальной сети Azure с частным IP-адресом. Если на экземпляре включена общедоступная конечная точка, шлюз не требуется. Если общедоступная конечная точка не включена, требуется локальный шлюз данных, а свойство AlwaysUseGateway должно иметь значение true.

> [!NOTE]
> Это свойство действует, только если установлен и настроен [локальный шлюз данных](analysis-services-gateway.md) . Шлюз может находиться в виртуальной сети.

## <a name="configure-alwaysusegateway-property"></a>Настройка свойства AlwaysUseGateway

1. Последовательно выберите SSMS > сервер > **Свойства** > **Общие** > **Показать дополнительные (все) свойства**.
2. Для параметра **ASPaaS\AlwaysUseGateway** выберите **true**.

    ![Свойство AlwaysUseGateway](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>См. также
[Подключение к локальным источникам данных](analysis-services-gateway.md)   
[Установка и настройка локального шлюза данных](analysis-services-gateway-install.md)   
[Виртуальная сеть Azure](../virtual-network/virtual-networks-overview.md)   

