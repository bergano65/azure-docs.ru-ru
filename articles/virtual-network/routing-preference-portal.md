---
title: Настройка предпочтительного варианта маршрутизации для общедоступного IP-адреса — портал Azure
description: Узнайте, как создать общедоступный IP-адрес с использованием выбора предпочтительного варианта маршрутизации интернет-трафика
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2020
ms.author: mnayak
ms.openlocfilehash: 7db8dcc87606e5c58ddac8f609d1538989af5ed7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84689305"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-the-azure-portal"></a>Настройка предпочтительного варианта маршрутизации для общедоступного IP-адреса с помощью портала Azure

В этой статье показано, как настроить предпочтительный вариант маршрутизации через сеть ISP (параметр **Интернет**) для общедоступного IP-адреса. Созданный общедоступный IP-адрес можно связать со следующими ресурсами Azure для входящего и исходящего Интернет-трафика.

* Виртуальная машина
* Набор масштабирования виртуальных машин
* Служба Azure Kubernetes (AKS)
* подсистема балансировки нагрузки с доступом в Интернет;
* Шлюз приложений
* Брандмауэр Azure

По умолчанию предпочтительный вариант маршрутизации для общедоступного IP-адреса настроен на глобальную сеть Майкрософт для всех служб Azure и его можно связать с любой службой Azure.

> [!IMPORTANT]
> Выбор предпочтительного варианта маршрутизации сейчас предоставляется в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="register-the-feature-for-your-subscription"></a>Регистрация функции для подписки
Функция выбора предпочтительного варианта маршрутизации в настоящее время доступна в предварительной версии. Зарегистрируйте функцию для подписки с помощью Azure PowerShell следующим образом.
```azurepowershell
Register-AzProviderFeature -FeatureName AllowRoutingPreferenceFeature -ProviderNamespace Microsoft.Network
```

## <a name="create-a-public-ip-address-with-a-routing-preference"></a>Создание общедоступного IP-адреса с выбором предпочтительного варианта маршрутизации
1. Войдите на [портал Azure](https://preview.portal.azure.com/).
2. Выберите **Создать ресурс**. 
3. В поле поиска введите *общедоступный IP-адрес*.
3. В результатах поиска выберите **Общедоступный IP-адрес**. Затем на странице **Общедоступный IP-адрес** щелкните **Создать**.
3. В диалоговом окне **Предпочтение маршрутизации** выберите **Интернет**.

      ![Создание общедоступного IP-адреса](./media/routing-preference-portal/pip-new.png)

    > [!NOTE]
    > Общедоступные IP-адреса создаются с адресами IPv4 или IPv6. Но сейчас при выборе предпочтительного варианта маршрутизации поддерживается только IPV4.

Вы можете связать созданный выше общедоступный IP-адрес с виртуальной машиной [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) или [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Используйте раздел руководства, посвященный CLI: [Свяжите общедоступный IP-адрес с виртуальной машиной.](associate-public-ip-address-vm.md#azure-cli) Вы также можете связать общедоступный IP-адрес, созданный выше, с [Azure Load Balancer](../load-balancer/load-balancer-overview.md), назначив его конфигурации **внешнего интерфейса** подсистемы балансировки нагрузки. Общедоступный IP-адрес будет выполнять функцию виртуального IP-адреса с балансировкой нагрузки.

## <a name="next-steps"></a>Дальнейшие действия
- См. сведения об [общедоступном IP-адресе с предпочтительным вариантом маршрутизации](routing-preference-overview.md).
- [Настройка предпочтительного варианта маршрутизации для виртуальной машины](tutorial-routing-preference-virtual-machine-portal.md)
- [Настройка предпочтительного варианта маршрутизации для общедоступного IP-адреса с помощью PowerShell](routing-preference-powershell.md)
- См. дополнительные сведения об [общедоступных IP-адресах в Azure](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses).
- См. сведения обо всех [параметрах общедоступных IP-адресов](virtual-network-public-ip-address.md#create-a-public-ip-address).
