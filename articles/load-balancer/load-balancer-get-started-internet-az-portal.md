---
title: Создание экземпляра Load Balancer (цен. категория "Стандартный") с избыточным между зонами интерфейсным сервером с помощью портала Azure
titlesuffix: Azure Load Balancer
description: Сведения о создании общедоступного экземпляра Load Balancer (цен. категория "Стандартный") с избыточным между зонами интерфейсным общедоступным IP-адресом с помощью портала Azure
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: kumud
ms.openlocfilehash: fc5dcd76ca8ecfb820a8d157fee9cc0a92bd2d5b
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53135699"
---
#  <a name="create-a-public-standard-load-balancer-with-zone-redundant-frontend-using-azure-portal"></a>Создание общедоступного экземпляра Load Balancer (цен. категория "Стандартный") с избыточным между зонами интерфейсным сервером с помощью портала Azure

В этой статье описываются действия по созданию общедоступного [балансировщика нагрузки уровня "Стандартный"](https://aka.ms/azureloadbalancerstandard) с избыточным между зонами интерфейсным сервером с помощью общедоступного стандартного IP-адреса. IP-адрес одного внешнего интерфейса в Load Balancer уровня "Стандартный" является избыточным между зонами по умолчанию.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

> [!NOTE]
 Поддержка зон доступности реализована для некоторых ресурсов, регионов и семейств размеров виртуальных машин Azure. Дополнительные сведения о начале работы и о том, какие ресурсы, регионы и семейства размеров виртуальных машин Azure поддерживаются для зон доступности, см. в разделе [Обзор зон доступности](https://docs.microsoft.com/azure/availability-zones/az-overview). Чтобы получить поддержку, обратитесь на форум [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) или [создайте запрос в службу поддержки Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="log-in-to-azure"></a>Вход в Azure 

Войдите на портал Azure по адресу https://portal.azure.com.

## <a name="create-a-zone-redundant-load-balancer"></a>Создание избыточной в пределах зоны подсистемы балансировки нагрузки

1. В браузере откройте портал Azure [http://portal.azure.com](http://portal.azure.com) и выполните вход с помощью учетной записи Azure.
2. Вверху слева последовательно выберите **Создать ресурс** > **Сети** > **Load Balancer**.
3. В окне **Создание подсистемы балансировки нагрузки** в поле **Имя** введите **myPublicLB**.
4. В разделе **Тип** выберите **Общедоступный**.
5. В разделе "Номер SKU" выберите **Стандартный**.
6. Щелкните **Общедоступный IP-адрес**. На странице **Создать общедоступный IP-адрес** выберите **Создать**, а затем в поле "Имя" введите **myPublicIPStandard**.
    >[!NOTE] 
    > Общедоступный IP-адрес, созданный на этом шаге, имеет номер SKU категории "Стандартный" и является избыточным между зонами по умолчанию. 
8. В разделе **Расположение** выберите **Восточная часть США 2** и нажмите кнопку **ОК**. После этого подсистема балансировки нагрузки начнет развертывание. Этот процесс может занять несколько минут.

## <a name="next-steps"></a>Дополнительная информация
- Дополнительные сведения см. в статье [Azure Load Balancer уровня "Стандартный" и зоны доступности](load-balancer-standard-availability-zones.md).



