---
title: Руководство. Создание сегмента сети NSX-T в Решении Azure VMware
description: Узнайте, как создать сегменты сети NSX-T, которые используются для виртуальных машин в vCenter.
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: 780cac15efc043b9ae44b77af1234adca3fec5a1
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91254539"
---
# <a name="tutorial-create-an-nsx-t-network-segment-in-azure-vmware-solution"></a>Руководство по созданию сегмента сети NSX-T в Решении Azure VMware

Сегменты сети, созданные в NSX-T Manager, используются в качестве сетей для виртуальных машин в vCenter. Виртуальные машины, созданные в vCenter, помещаются в сегменты сети, созданные в NSX-T, и они видимы в vCenter.

В этом руководстве описано следующее:

> [!div class="checklist"]
> * Переход в NSX-T Manager для добавления сегментов сети.
> * Добавление сегмента сети.
> * Просмотр нового сегмента сети в vCenter.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством необходимо частное облако Решения Azure VMware с доступом к интерфейсам управления vCenter и NSX-T Manager. Дополнительные сведения см. в руководстве [Настройка сети для частного облака VMware в Azure](tutorial-configure-networking.md).

## <a name="provision-a-network-segment-in-nsx-t"></a>Подготовления сегмента сети в NSX-T

1. В vCenter для частного облака выберите **SDDC-Datacenter > Networks** (Центр обработки данных SDDC > Сети) и обратите внимание на то, что сетей здесь пока нет.

   :::image type="content" source="media/nsxt/vcenter-without-ls01.png" alt-text="В vCenter для частного облака выберите SDDC-Datacenter > Networks (Центр обработки данных SDDC > Сети) и обратите внимание на то, что сетей здесь пока нет.":::

1. В NSX-T Manager для вашего частного облака выберите **Сеть**.

   :::image type="content" source="media/nsxt/nsxt-network-overview.png" alt-text="В vCenter для частного облака выберите SDDC-Datacenter > Networks (Центр обработки данных SDDC > Сети) и обратите внимание на то, что сетей здесь пока нет.":::

1. Выберите **Сегменты**.

   :::image type="content" source="media/nsxt/nsxt-select-segments.png" alt-text="В vCenter для частного облака выберите SDDC-Datacenter > Networks (Центр обработки данных SDDC > Сети) и обратите внимание на то, что сетей здесь пока нет.":::

1. На странице обзора сегментов NSX-T выберите **Добавить сегмент**. В рамках предоставления доступа к частному облаку создаются три сегмента, которые нельзя использовать для ВМ.  Для выполнения этой задачи необходимо добавить новый сегмент сети.

   :::image type="content" source="media/nsxt/nsxt-segments-overview.png" alt-text="В vCenter для частного облака выберите SDDC-Datacenter > Networks (Центр обработки данных SDDC > Сети) и обратите внимание на то, что сетей здесь пока нет.":::

1. Назовите сегмент, выберите предварительно сконфигурированный шлюз Tier1 (TNTxx-T1) в качестве **подключенного шлюза**, оставьте для пункта **Тип** значение "Гибкий", выберите предварительно сконфигурированное наложение **Зона транспортировки** (TNTxx-OVERLAY-TZ), а затем выберите Set Subnets (Задать подсети). Все остальные параметры в этом разделе, а также **порты** и **профили сегментов** можно установить на значения по умолчанию, как и конфигурацию.

   :::image type="content" source="media/nsxt/nsxt-create-segment-specs.png" alt-text="В vCenter для частного облака выберите SDDC-Datacenter > Networks (Центр обработки данных SDDC > Сети) и обратите внимание на то, что сетей здесь пока нет.":::

1. Задайте IP-адрес шлюза для нового сегмента, а затем выберите **Добавить**. IP-адрес, который вы используете, должен находиться в неперекрывающемся блоке адресов RFC1918, что обеспечивает возможность подключения к виртуальным машинам в новом сегменте.

   :::image type="content" source="media/nsxt/nsxt-create-segment-gateway.png" alt-text="В vCenter для частного облака выберите SDDC-Datacenter > Networks (Центр обработки данных SDDC > Сети) и обратите внимание на то, что сетей здесь пока нет.":::

1. Примените новый сегмент сети, выбрав **Применить**, а затем сохраните конфигурацию с помощью кнопки **Сохранить**.

   :::image type="content" source="media/nsxt/nsxt-create-segment-apply.png" alt-text="В vCenter для частного облака выберите SDDC-Datacenter > Networks (Центр обработки данных SDDC > Сети) и обратите внимание на то, что сетей здесь пока нет.":::

   :::image type="content" source="media/nsxt/nsxt-create-segment-save.png" alt-text="В vCenter для частного облака выберите SDDC-Datacenter > Networks (Центр обработки данных SDDC > Сети) и обратите внимание на то, что сетей здесь пока нет.":::

1. Новый сегмент сети создан. Отклоните параметр, чтобы продолжить настройку сегмента, выбрав **Нет**.

   :::image type="content" source="media/nsxt/nsxt-create-segment-continue-no.png" alt-text="В vCenter для частного облака выберите SDDC-Datacenter > Networks (Центр обработки данных SDDC > Сети) и обратите внимание на то, что сетей здесь пока нет.":::

1. Убедитесь, что новый сегмент сети присутствует в NSX-T, выбрав **Сети > Сегменты** и просмотрев новый сегмент в списке (в данном случае "ls01").

   :::image type="content" source="media/nsxt/nsxt-new-segment-overview-2.png" alt-text="В vCenter для частного облака выберите SDDC-Datacenter > Networks (Центр обработки данных SDDC > Сети) и обратите внимание на то, что сетей здесь пока нет.":::

1. Убедитесь, что новый сегмент сети присутствует в vCenter, выбрав **Networking > SDDC-Datacenter** (Сети > Центр обработки данных SDDC) и просмотрев новый сегмент в списке (в данном случае "ls01").

   :::image type="content" source="media/nsxt/vcenter-with-ls01-2.png" alt-text="В vCenter для частного облака выберите SDDC-Datacenter > Networks (Центр обработки данных SDDC > Сети) и обратите внимание на то, что сетей здесь пока нет.":::

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы создали сегменты сети NSX-T которые используются для виртуальных машин в vCenter. Теперь можно [создать библиотеку содержимого для развертывания виртуальных машин в Решении Azure VMware](deploy-vm-content-library.md) и подготовить виртуальную машину в сети, созданную по инструкциям из этого руководства.

Или же перейдите к следующему руководству, чтобы узнать, как в Решении Azure VMware создать пиринг ExpressRoute Global Reach с частным облаком.

> [!div class="nextstepaction"]
> [Настройка пиринга между локальной средой и частным облаком](tutorial-expressroute-global-reach-private-cloud.md)

<!-- LINKS - external-->

<!-- LINKS - internal -->
