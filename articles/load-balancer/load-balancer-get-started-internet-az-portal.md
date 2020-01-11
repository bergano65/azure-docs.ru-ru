---
title: Создание экземпляра Load Balancer с избыточным между зонами интерфейсным сервером — портал Azure
titleSuffix: Azure Load Balancer
description: Сведения о создании общедоступного экземпляра Load Balancer (цен. категория "Стандартный") с избыточным между зонами интерфейсным общедоступным IP-адресом с помощью портала Azure
services: load-balancer
documentationcenter: na
author: asudbring
manager: kumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: allensu
ms.openlocfilehash: bd85e062b2b45abc32269b94ce678ebf63fb3fc1
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894452"
---
#  <a name="create-a-standard-load-balancer-with-zone-redundant-frontend-using-azure-portal"></a>Создание экземпляра Load Balancer (цен. категория "Стандартный") с избыточным между зонами интерфейсным сервером с помощью портала Azure

В этой статье описываются действия по созданию общедоступного [Load Balancer (цен. категория "Стандартный")](https://aka.ms/azureloadbalancerstandard) с избыточным между зонами интерфейсным сервером с помощью общедоступного стандартного IP-адреса. IP-адрес одного внешнего интерфейса в Load Balancer уровня "Стандартный" является избыточным между зонами по умолчанию.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

> [!NOTE]
>  Поддержка зон доступности реализована для некоторых ресурсов, регионов и семейств размеров виртуальных машин Azure. Дополнительные сведения о начале работы и о том, какие ресурсы, регионы и семейства размеров виртуальных машин Azure поддерживаются для зон доступности, см. в разделе [Обзор зон доступности](https://docs.microsoft.com/azure/availability-zones/az-overview). Чтобы получить поддержку, обратитесь на форум [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) или [создайте запрос в службу поддержки Azure](../azure-portal/supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="log-in-to-azure"></a>Вход в Azure 

Войдите на портал Azure по адресу https://portal.azure.com.

## <a name="create-a-zone-redundant-load-balancer"></a>Создание избыточной в пределах зоны подсистемы балансировки нагрузки

1. В браузере откройте портал Azure [https://portal.azure.com](https://portal.azure.com) и выполните вход с помощью учетной записи Azure.
2. Вверху слева последовательно выберите **Создать ресурс** > **Сети** > **Load Balancer**.
3. В окне **Создание подсистемы балансировки нагрузки** в поле **Имя** введите **myPublicLB**.
4. В разделе **Тип** выберите **Общедоступный**.
5. В разделе "Номер SKU" выберите **Стандартный**.
6. Щелкните **Общедоступный IP-адрес**. На странице **Создать общедоступный IP-адрес** выберите **Создать**, а затем в поле "Имя" введите **myPublicIPStandard**.
    >[!NOTE] 
    > Общедоступный IP-адрес, созданный на этом шаге, имеет номер SKU категории "Стандартный" и является избыточным между зонами по умолчанию. 
8. В разделе **Расположение** выберите **Восточная часть США 2** и нажмите кнопку **ОК**. После этого подсистема балансировки нагрузки начнет развертывание. Этот процесс может занять несколько минут.

## <a name="next-steps"></a>Дальнейшие действия
- Дополнительные сведения см. в статье [Azure Load Balancer уровня "Стандартный" и зоны доступности](load-balancer-standard-availability-zones.md).



