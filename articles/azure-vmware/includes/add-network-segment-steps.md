---
title: Добавление сегмента сети NSX-T
description: Инструкции по добавлению сегмента сети NSX-T в Решении Azure VMware.
ms.topic: include
ms.date: 11/09/2020
ms.openlocfilehash: 5b97f0b280fa12eff39c9601bb73e439dba8e9fd
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335057"
---
<!-- Used in manage-dhcp.md and tutorial-nsx-t-network-segment.md -->

1. В NSX-T **Networking** > **Segments** (Manager > Сеть > Сегменты), а затем выберите **Add Segment** (Добавить сегмент). 

   :::image type="content" source="../media/nsxt/nsxt-segments-overview.png" alt-text="Снимок экрана, на котором показано, как добавить новый сегмент":::

1. Выберите **Добавить сегмент** и введите его имя.

1. Выберите шлюз уровня 1 (TNTxx-T1) в качестве **подключенного шлюза** и оставьте значение Flexible (Гибкий) в поле **Type** (Тип).

1. Выберите предварительно настроенную **зону транспортировки** для наложения (TNTxx-OVERLAY-TZ), а затем выберите **Set Subnets** (Задать подсети). 

   :::image type="content" source="../media/nsxt/nsxt-create-segment-specs.png" alt-text="Укажите имя сегмента, подключенный шлюз, тип и зону транспортировки, а затем выберите Set Subnets (Задать подсети).":::

1. Введите IP-адрес шлюза и выберите **Добавить**. 

   >[!IMPORTANT]
   >IP-адрес должен находиться в неперекрывающемся блоке адресов RFC1918, что обеспечит подключение к виртуальным машинам в новом сегменте.

   :::image type="content" source="../media/nsxt/nsxt-create-segment-gateway.png" alt-text="Укажите IP-адрес шлюза для нового сегмента, а затем выберите ADD (Добавить).":::

1. Нажмите кнопку **Apply** (Применить), а затем — **Save** (Сохранить).

1. Выберите **No** (Нет), чтобы отказаться от дальнейшей настройки сегмента. 

   :::image type="content" source="../media/nsxt/nsxt-create-segment-continue-no.png" alt-text="Отклонение настройки недавно созданного сегмента сети, выбрав &quot;Нет&quot;.":::

1. Убедитесь в наличии нового сегмента сети. В этом примере новый сегмент сети — это **ls01**.

   1. В NSX-T Manager выберите **Networking** > **Segments** (Сеть > Сегменты). 

      :::image type="content" source="../media/nsxt/nsxt-new-segment-overview-2.png" alt-text="Проверка наличия нового сегмента сети в NSX-T.":::

   1. В vCenter выберите **Networking > SDDC-Datacenter** (Сеть > Центр обработки данных SDDC).

      :::image type="content" source="../media/nsxt/vcenter-with-ls01-2.png" alt-text="Проверка наличия нового сегмента сети в vCenter.":::