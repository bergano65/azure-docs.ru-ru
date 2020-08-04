---
title: Руководство по созданию сегмента сети NSX-T в Решении Azure VMware (AVS)
description: В этом руководстве вы создали сегменты сети NSX-T, которые используют для виртуальных машин в vCenter
ms.topic: tutorial
ms.date: 07/16/2020
ms.openlocfilehash: 5654fbb6a063d4dfeb541c20407f9a09dff1509f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87093975"
---
# <a name="tutorial-create-an-nsx-t-network-segment-in-azure-vmware-solution-avs"></a>Руководство по созданию сегмента сети NSX-T в Решении Azure VMware (AVS)

Сегменты сети, созданные в NSX-T Manager, используются в качестве сетей для виртуальных машин в vCenter. Виртуальные машины, созданные в vCenter, помещаются в сегменты сети, созданные в NSX-T, и они видимы в vCenter.

В этом руководстве описано следующее:

> [!div class="checklist"]
> * Переход в NSX-T Manager для добавления сегментов сети.
> * Добавление сегмента сети.
> * Просмотр нового сегмента сети в vCenter.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством необходимо частное облако AVS с доступом к интерфейсам управления vCenter и NSX-T Manager. См. [Руководство по настройке сети для частного облака VMware в Azure](tutorial-configure-networking.md)

## <a name="provision-a-network-segment-in-nsx-t"></a>Подготовления сегмента сети в NSX-T

1. В vCenter для частного облака выберите **SDDC-Datacenter > Networks** (Центр обработки данных SDDC > Сети) и обратите внимание на то, что сетей здесь пока нет.

   :::image type="content" source="media/nsxt/vcenter-without-ls01.png" alt-text="Выбор "SDDC-Datacenter > Networks" (Центр обработки данных SDDC > Сети)":::

1. В NSX-T Manager для вашего частного облака выберите **Сеть**.

   :::image type="content" source="media/nsxt/nsxt-network-overview.png" alt-text="Выберите сеть, чтобы перейти к представлению "NSX-T Manager Network Overview" (Обзор сети NSX-T Manager)":::.

1. Выберите **Сегменты**.

   :::image type="content" source="media/nsxt/nsxt-select-segments.png" alt-text="Выбор пункта "Сегменты" на странице "Обзор сети".":::

1. На странице обзора сегментов NSX-T выберите **Добавить сегмент**. В рамках предоставления доступа к частному облаку создаются три сегмента, которые нельзя использовать для ВМ.  Для выполнения этой задачи необходимо добавить новый сегмент сети.

   :::image type="content" source="media/nsxt/nsxt-segments-overview.png" alt-text="Выбор пункта "Добавить сегмент" на странице обзора "Сегмент сети".":::

1. Назовите сегмент, выберите предварительно сконфигурированный шлюз Tier1 (TNTxx-T1) в качестве **подключенного шлюза**, оставьте для пункта **Тип** значение "Гибкий", выберите предварительно сконфигурированное наложение **Зона транспортировки** (TNTxx-OVERLAY-TZ), а затем выберите Set Subnets (Задать подсети). Все остальные параметры в этом разделе, а также **порты** и **профили сегментов** можно установить на значения по умолчанию, как и конфигурацию.

   :::image type="content" source="media/nsxt/nsxt-create-segment-specs.png" alt-text="Задание имени сегмента, подключенного шлюза, типа и зону транспортировки, а затем выбор пункта Set Subnets (Задать подсети).":::

1. Задайте IP-адрес шлюза для нового сегмента, а затем выберите **Добавить**. IP-адрес, который вы используете, должен находиться в неперекрывающемся блоке адресов RFC1918, что обеспечивает возможность подключения к виртуальным машинам в новом сегменте.

   :::image type="content" source="media/nsxt/nsxt-create-segment-gateway.png" alt-text="Указание IP-адреса шлюза сегмента в нотации CIDR и выбор пункта "Добавить".":::

1. Примените новый сегмент сети, выбрав **Применить**, а затем сохраните конфигурацию с помощью кнопки **Сохранить**.

   :::image type="content" source="media/nsxt/nsxt-create-segment-apply.png" alt-text="Применение нового сегмента сети к конфигурации NSX-T, нажав кнопку "Применить".":::

   :::image type="content" source="media/nsxt/nsxt-create-segment-save.png" alt-text="Сохранение нового сегмента сети в конфигурации NSX-T, нажав кнопку "Сохранить".":::

1. Новый сегмент сети создан. Отклоните параметр, чтобы продолжить настройку сегмента, выбрав **Нет**.

   :::image type="content" source="media/nsxt/nsxt-create-segment-continue-no.png" alt-text="Отклонение настройки недавно созданного сегмента сети, выбрав "Нет".":::

1. Убедитесь, что новый сегмент сети присутствует в NSX-T, выбрав **Сети > Сегменты** и просмотрев новый сегмент в списке (в данном случае "ls01").

   :::image type="content" source="media/nsxt/nsxt-new-segment-overview-2.png" alt-text="Проверка наличия нового сегмента сети в NSX-T.":::

1. Убедитесь, что новый сегмент сети присутствует в vCenter, выбрав **Networking > SDDC-Datacenter** (Сети > Центр обработки данных SDDC) и просмотрев новый сегмент в списке (в данном случае "ls01").

   :::image type="content" source="media/nsxt/vcenter-with-ls01-2.png" alt-text="Проверка наличия нового сегмента сети в vCenter.":::

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы создали сегменты сети NSX-T которые используются для виртуальных машин в vCenter. Теперь вы можете просмотреть статью [ Руководство по созданию библиотеки содержимого для развертывания виртуальных машин в решении Azure VMware (AVS)](tutorial-deploy-vm-content-library.md), чтобы получить сведения о создании библиотеки содержимого в vCenter и подготовке виртуальной машины в сети, указанной в этом учебнике.

<!-- LINKS - external-->

<!-- LINKS - internal -->
