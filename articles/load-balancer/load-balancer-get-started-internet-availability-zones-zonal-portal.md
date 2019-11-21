---
title: Создание Load Balancer с зональным внешним интерфейсом — портал Azure
titleSuffix: Azure Load Balancer
description: Узнайте, как создать экземпляр Load Balancer (цен. категория "Стандартный") с зональным интерфейсным сервером с помощью портала Azure
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: allensu
ms.openlocfilehash: 82a773b279780bc4eb784fa107d6b15bd0ff2672
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225342"
---
#  <a name="create-a-standard-load-balancer-with-zonal-frontend-using-azure-portal"></a>Создание экземпляра Load Balancer (цен. категория "Стандартный") с зональным интерфейсным сервером с помощью портала Azure

В этой статье приведены действия по созданию общедоступного экземпляра [Load Balancer (цен. категория "Стандартный")](https://aka.ms/azureloadbalancerstandard) в конфигурации с зональным внешним интерфейсом с IP-адресом. Чтобы понять, как работают зоны доступности с Load Balancer уровня "Стандартный", ознакомьтесь со статьей [Azure Load Balancer уровня "Стандартный" и зоны доступности](load-balancer-standard-availability-zones.md). 

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

> [!NOTE]
> Поддержка зон доступности реализована для некоторых ресурсов, регионов и семейств размеров виртуальных машин Azure. Дополнительные сведения о начале работы и о том, какие ресурсы, регионы и семейства размеров виртуальных машин Azure поддерживаются для зон доступности, см. в разделе [Обзор зон доступности](https://docs.microsoft.com/azure/availability-zones/az-overview). Чтобы получить поддержку, обратитесь на форум [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) или [создайте запрос в службу поддержки Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="log-in-to-azure"></a>Вход в Azure 

Войдите на портал Azure по адресу https://portal.azure.com.

## <a name="create-a-load-balancer-with-zonal-frontend-ip-address"></a>Создание подсистемы балансировки нагрузки с IP-адресом с зональным внешним интерфейсом

1. В браузере откройте портал Azure [https://portal.azure.com](https://portal.azure.com) и выполните вход с помощью учетной записи Azure.
2. Вверху слева последовательно выберите **Создать ресурс** > **Сети** > **Load Balancer**.
3. В окне **Создание подсистемы балансировки нагрузки** в поле **Имя** введите **myPublicLB**.
4. В разделе **Тип** выберите **Общедоступный**.
5. В разделе "Номер SKU" выберите **Стандартный**.
6. Щелкните **Выберите общедоступный IP-адрес**, затем — **Создать**. На странице **Создать общедоступный IP-адрес** в разделе имени введите **myPublicIPZonal**. Для номера SKU выберите **Стандартный**, а для зоны доступности — **1**.
    
>[!NOTE] 
> Общедоступный IP-адрес, созданный на этом шаге, имеет номер SKU категории "Стандартный" по умолчанию.

1. В разделе **Группа ресурсов** щелкните **Создать**, а затем введите имя группы ресурсов — **myResourceGroupZLB**.
1. В разделе **Расположение** выберите **Западная Европа** и нажмите кнопку **ОК**. После этого подсистема балансировки нагрузки начнет развертывание. Этот процесс может занять несколько минут.

    ![Создание избыточного между зонами экземпляра Load Balancer (цен. категория "Стандартный") с помощью портала Azure](./media/load-balancer-get-started-internet-availability-zones-zonal-portal/load-balancer-zonal-frontend.png)


## <a name="next-steps"></a>Дальнейшие действия
- Дополнительные сведения см. в статье [Azure Load Balancer уровня "Стандартный" и зоны доступности](load-balancer-standard-availability-zones.md).



