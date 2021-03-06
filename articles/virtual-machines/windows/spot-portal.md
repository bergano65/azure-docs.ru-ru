---
title: Развертывание виртуальных машин Azure с помощью портала
description: Узнайте, как использовать Azure PowerShell для развертывания плашечных виртуальных машин, чтобы сэкономить на затратах.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/20/2019
ms.author: cynthn
ms.openlocfilehash: 682ee86c373c715080ef1baf82b473242a2e43db
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74782051"
---
# <a name="preview-deploy-spot-vms-using-the-azure-portal"></a>Предварительная версия: развертывание плашечных виртуальных машин с помощью портал Azure

Использование [плашечных виртуальных машин](spot-vms.md) позволяет использовать преимущества нашей неиспользуемой емкости с значительной экономией затрат. В любой момент, когда Azure нужна емкость, инфраструктура Azure будет выключать плашечные виртуальные машины. Таким образом, точечные виртуальные машины отлично подходят для рабочих нагрузок, которые могут обрабатывать прерывания, такие как задания пакетной обработки, среды разработки и тестирования, большие вычислительные рабочие нагрузки и многое другое.

Цены для плашечных виртуальных машин являются переменными в зависимости от региона и SKU. Дополнительные сведения см. в статье цены на виртуальные машины для [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) и [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Дополнительные сведения о настройке максимальной цены см. в разделе [точки на виртуальных машинах — цены](spot-vms.md#pricing).

Вы можете задать максимальную цену, которую вы хотите платить, в час, для виртуальной машины. Максимальная цена на плашечную виртуальную машину можно установить в долларах США (USD), используя до 5 десятичных разрядов. Например, значение `0.05701`будет максимальной ценой $0,05701 долл. США в час. Если задать максимальную цену `-1`, виртуальная машина не будет удалена на основе цены. Цена на виртуальную машину будет соответствовать текущей цене или цене для стандартной виртуальной машины, которая когда-либо будет меньше, если есть доступная емкость и квота.

> [!IMPORTANT]
> Экземпляры смесевых цветов в настоящее время доступны в общедоступной предварительной версии.
> Эта предварительная версия не рекомендуется для рабочих нагрузок в рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> На ранних этапах общедоступной предварительной версии экземпляры смесевых цветов будут иметь фиксированную цену, поэтому на основе цен не будет никаких вытеснения.

## <a name="create-the-vm"></a>Создание виртуальной машины

Процесс создания виртуальной машины, использующей плашечные виртуальные машины, аналогичен описанному в [кратком руководстве](quick-create-portal.md). При развертывании виртуальной машины можно выбрать использование экземпляра точки Azure.


На вкладке " **основы** " в разделе " **сведения об экземпляре** " **нет** по умолчанию для использования экземпляра точки Azure.

![Снимок экрана для выбора нет, не использовать экземпляр точки Azure](media/spot-portal/no.png)

Выберите **Да**, раздел разворачивается, и вы можете выбрать [тип и политику вытеснения](spot-vms.md#eviction-policy). 

![Снимок экрана для выбора "Да", использовать экземпляр точки Azure](media/spot-portal/yes.png)


## <a name="next-steps"></a>Дальнейшие действия

Вы также можете создавать плашечные виртуальные машины с помощью [PowerShell](spot-powershell.md).