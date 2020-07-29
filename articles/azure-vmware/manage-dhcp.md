---
title: Управление DHCP
description: В этой статье объясняется, как управлять DHCP в решении Azure VMware (AVS).
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 80791dd2041fb9d6fbc7c67f2d7d7b2d0b6c977e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84148367"
---
# <a name="how-to-manage-dhcp-in-azure-vmware-solution-avs-preview"></a>Как управлять DHCP в предварительной версии решения Azure VMWare (AVS)

НСКС-T предоставляет возможность настройки DHCP для частного облака. Если вы планируете использовать НСКС-T для размещения DHCP-сервера, см. раздел [Создание DHCP-сервера](#create-dhcp-server). В противном случае, если в сети имеется сторонний внешний DHCP-сервер и вы хотите ретранслировать запросы к этому DHCP-серверу, см. раздел [Создание службы DHCP-ретрансляции](#create-dhcp-relay-service).

## <a name="create-dhcp-server"></a>Создание DHCP-сервера

Выполните следующие действия, чтобы настроить DHCP-сервер на НСКС — T.

В НСКС Manager перейдите на вкладку **Сетевые подключения** и в разделе **Управление IP-адресами**выберите **DHCP** . Нажмите кнопку **Добавить сервер** . Затем укажите имя сервера и IP-адрес сервера. После этого нажмите кнопку **сохранить**.

:::image type="content" source="./media/manage-dhcp/dhcp-server-settings.png" alt-text="добавить DHCP-сервер" border="true":::

### <a name="connect-dhcp-server-to-the-tier-1-gateway"></a>Подключите DHCP-сервер к шлюзу уровня 1.

Выберите **шлюзы уровня 1**, выберите шлюз и щелкните **изменить** .

:::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="Выберите шлюз для использования" border="true":::

Добавьте подсеть, выбрав параметр " **нет набора выделений IP-адресов** ".

:::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="Добавление подсети" border="true":::

На следующем экране в раскрывающемся списке **Тип** выберите **локальный сервер DHCP** . Для **DHCP-сервера**выберите **по умолчанию DHCP** и нажмите кнопку **сохранить**.

:::image type="content" source="./media/manage-dhcp/set-ip-address-management.png" alt-text="Выберите параметры для DHCP-сервера" border="true":::

В окне **шлюз уровня 1** выберите **сохранить**. На следующем экране отобразятся **сохраненные изменения**, а затем нажмите кнопку **Закрыть редактирование** для завершения.

### <a name="add-a-network-segment"></a>Добавление сегмента сети

После создания DHCP-сервера необходимо добавить к нему сегменты сети.

В НСКС-T перейдите на вкладку " **сеть** " и выберите " **сегменты** " в разделе " **Подключение**". Выберите **Добавить сегмент**. Присвойте сегменту имя и подключение к шлюзу уровня 1. Затем выберите **задать подсети** , чтобы настроить новую подсеть. 

:::image type="content" source="./media/manage-dhcp/add-segment.png" alt-text="Добавить новый сегмент сети" border="true":::

В окне **задать подсети** выберите **добавить подсеть**. Введите IP-адрес шлюза и диапазон DHCP и нажмите кнопку **Добавить** , а затем — **Применить** .

:::image type="content" source="./media/manage-dhcp/add-subnet-segment.png" alt-text="Добавить сегмент сети" border="true":::

По завершении нажмите кнопку **сохранить** , чтобы завершить добавление сегмента сети.

:::image type="content" source="./media/manage-dhcp/segments-complete.png" alt-text="сегменты завершены" border="true":::

## <a name="create-dhcp-relay-service"></a>Создание службы ретранслятора DHCP

В окне NXT-T выберите вкладку **сеть** и в разделе **Управление IP-адресами**выберите **DHCP**. Выберите **Добавить сервер**. Выберите ретранслятор DHCP для **типа сервера** и введите имя сервера и IP-адрес сервера ретрансляции. Выберите **Сохранить**, чтобы сохранить изменения.

:::image type="content" source="./media/manage-dhcp/create-dhcp-relay.png" alt-text="Создание сервера ретранслятора DHCP" border="true":::

Выберите **шлюзы уровня 1** в разделе **Подключение**. Щелкните вертикальное многоточие на шлюзе уровня 1 и выберите **изменить**.

:::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway-relay.png" alt-text="Изменение шлюза уровня 1" border="true":::

Выберите **не устанавливать выделение IP-** адресов, чтобы определить выделение IP-адреса.

:::image type="content" source="./media/manage-dhcp/edit-ip-address-allocation.png" alt-text="изменить выделение IP-адресов" border="true":::

В диалоговом окне в поле **тип**выберите **DHCP-сервер ретранслятора**. В раскрывающемся списке **ретранслятор DHCP** выберите сервер DHCP-ретрансляции. По завершении нажмите кнопку **сохранить** .

:::image type="content" source="./media/manage-dhcp/set-ip-address-management-relay.png" alt-text="Настройка управления IP-адресами" border="true":::

Укажите IP-адрес диапазона DHCP в сегменте:

> [!NOTE]
> Эта конфигурация необходима для реализации функций ретранслятора DHCP в сегменте DHCP-клиента. 

В разделе **Подключение**выберите **сегменты**. Щелкните вертикальные многоточие и выберите **изменить**. Вместо этого, если вы хотите добавить новый сегмент, можно выбрать **Добавить сегмент** , чтобы создать новый сегмент.

:::image type="content" source="./media/manage-dhcp/edit-segments.png" alt-text="Изменение подсети" border="true":::

Добавьте сведения о сегменте. Выберите значение в разделе **подсети** или **Задайте** подсети, чтобы добавить или изменить подсеть.

:::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="сегменты сети" border="true":::

Щелкните вертикальные многоточие и выберите **изменить**. Если необходимо создать новую подсеть, выберите **добавить подсеть** , чтобы создать шлюз и настроить диапазон DHCP. Укажите диапазон пула IP-адресов и нажмите кнопку **Применить**, а затем выберите **сохранить** .

:::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="изменение подсетей" border="true":::

Теперь сегменту назначается пул DHCP-серверов.

:::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="Пул DHCP-серверов, назначенный сегменту" border="true":::
