---
title: Руководство. Создание сегмента сети NSX-T в Решении Azure VMware
description: Узнайте, как создать сегменты сети NSX-T, которые используются для виртуальных машин в vCenter.
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: f88f186d2af10bcc114d64920a3ac489ef7be54f
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92367675"
---
# <a name="tutorial-create-an-nsx-t-network-segment-in-azure-vmware-solution"></a>Руководство по созданию сегмента сети NSX-T в Решении Azure VMware

Виртуальные машины, созданные в vCenter, помещаются в сегменты сети, созданные в NSX-T, и они видимы в vCenter.

В этом руководстве описано следующее:

> [!div class="checklist"]
> * Переход в NSX-T Manager для добавления сегментов сети.
> * Добавление сегмента сети.
> * Просмотр нового сегмента сети в vCenter.

## <a name="prerequisites"></a>Предварительные требования

Частное облако Решения Azure VMware с доступом к интерфейсам управления vCenter и NSX-T Manager. Дополнительные сведения см. в статье о [конфигурации сети](tutorial-configure-networking.md).

## <a name="provision-a-network-segment-in-nsx-t"></a>Подготовления сегмента сети в NSX-T

1. В vCenter для частного облака выберите **SDDC-Datacenter > Networks** (Центр обработки данных SDDC > Сети) и обратите внимание на то, что сетей здесь пока нет.

   :::image type="content" source="media/nsxt/vcenter-without-ls01.png" alt-text="В vCenter для частного облака выберите SDDC-Datacenter > Networks (Центр обработки данных SDDC > Сети) и обратите внимание на то, что сетей здесь пока нет.":::

1. В NSX-T Manager для вашего частного облака выберите **Сеть**.

   :::image type="content" source="media/nsxt/nsxt-network-overview.png" alt-text="В NSX-T Manager для вашего частного облака выберите Networking (Сеть).":::

1. Выберите **Сегменты**.

   :::image type="content" source="media/nsxt/nsxt-select-segments.png" alt-text="Выбор пункта &quot;Сегменты&quot; на странице &quot;Обзор сети&quot;.":::

1. На странице обзора сегментов NSX-T выберите **Добавить сегмент**. В рамках предоставления доступа к частному облаку создаются три сегмента, которые нельзя использовать для ВМ.  Для выполнения этой задачи необходимо добавить новый сегмент сети.

   :::image type="content" source="media/nsxt/nsxt-segments-overview.png" alt-text="На странице обзора сегментов NSX-T выберите ADD SEGMENT (Добавить сегмент).":::

1. Назовите сегмент, выберите предварительно сконфигурированный шлюз Tier1 (TNTxx-T1) в качестве **подключенного шлюза** , оставьте для пункта **Тип** значение "Гибкий", выберите предварительно сконфигурированное наложение **Зона транспортировки** (TNTxx-OVERLAY-TZ), а затем выберите Set Subnets (Задать подсети). Все остальные параметры в этом разделе, а также **порты** и **профили сегментов** можно установить на значения по умолчанию, как и конфигурацию.

   :::image type="content" source="media/nsxt/nsxt-create-segment-specs.png" alt-text="Укажите имя сегмента, подключенный шлюз, тип и зону транспортировки, а затем выберите Set Subnets (Задать подсети).":::

1. Задайте IP-адрес шлюза для нового сегмента, а затем выберите **Добавить**. IP-адрес, который вы используете, должен находиться в неперекрывающемся блоке адресов RFC1918, что обеспечивает возможность подключения к виртуальным машинам в новом сегменте.

   :::image type="content" source="media/nsxt/nsxt-create-segment-gateway.png" alt-text="Укажите IP-адрес шлюза для нового сегмента, а затем выберите ADD (Добавить).":::

1. Примените новый сегмент сети, выбрав **Применить** , а затем сохраните конфигурацию с помощью кнопки **Сохранить**.

   :::image type="content" source="media/nsxt/nsxt-create-segment-apply.png" alt-text="Применение нового сегмента сети к конфигурации NSX-T, нажав кнопку &quot;Применить&quot;.":::

   :::image type="content" source="media/nsxt/nsxt-create-segment-save.png" alt-text="Сохранение нового сегмента сети в конфигурации NSX-T, нажав кнопку &quot;Сохранить&quot;.":::

1. Новый сегмент сети создан. Отклоните параметр, чтобы продолжить настройку сегмента, выбрав **Нет**.

   :::image type="content" source="media/nsxt/nsxt-create-segment-continue-no.png" alt-text="Отклонение настройки недавно созданного сегмента сети, выбрав &quot;Нет&quot;.":::

1. Убедитесь, что новый сегмент сети присутствует в NSX-T, выбрав **Сети > Сегменты** и просмотрев новый сегмент в списке (в данном случае "ls01").

   :::image type="content" source="media/nsxt/nsxt-new-segment-overview-2.png" alt-text="Проверка наличия нового сегмента сети в NSX-T.":::

1. Убедитесь, что новый сегмент сети присутствует в vCenter, выбрав **Networking > SDDC-Datacenter** (Сети > Центр обработки данных SDDC) и просмотрев новый сегмент в списке (в данном случае "ls01").

   :::image type="content" source="media/nsxt/vcenter-with-ls01-2.png" alt-text="Проверка наличия нового сегмента сети в vCenter.":::

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы создали сегменты сети NSX-T которые используются для виртуальных машин в vCenter. Теперь вы можете [создать библиотеку содержимого для развертывания виртуальных машин в Решении Azure VMware](deploy-vm-content-library.md). Кроме того, вы можете подготовить виртуальную машину в сети, созданной в рамках этого руководства.

Или же перейдите к следующему руководству, чтобы узнать, как в Решении Azure VMware создать пиринг ExpressRoute Global Reach с частным облаком.

> [!div class="nextstepaction"]
> [Настройка пиринга между локальной средой и частным облаком](tutorial-expressroute-global-reach-private-cloud.md)

<!-- LINKS - external-->

<!-- LINKS - internal -->
