---
title: Руководство. Добавление сегмента сети NSX-T в Решении Azure VMware
description: Узнайте, как создать сегмент сети NSX-T, который будет использоваться для виртуальных машин в vCenter.
ms.topic: tutorial
ms.date: 11/09/2020
ms.openlocfilehash: 8ecb37a42e2986bd1c6261b8fe6c23382323b31d
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335054"
---
# <a name="tutorial-add-a-network-segment-in-azure-vmware-solution"></a>Руководство по Добавление сегмента сети в Решении Azure VMware 

Виртуальные машины, созданные в vCenter, помещаются в сегменты сети, созданные в NSX-T, и они видимы в vCenter.

В этом руководстве описано следующее:

> [!div class="checklist"]
> * Переход в NSX-T Manager для добавления сегментов сети.
> * Добавление сегмента сети.
> * Просмотр нового сегмента сети в vCenter.

## <a name="prerequisites"></a>Предварительные требования

Частное облако Решения Azure VMware с доступом к интерфейсам vCenter и NSX-T Manager. Дополнительные сведения см. в статье о [конфигурации сети](tutorial-configure-networking.md).

## <a name="add-a-network-segment"></a>Добавление сегмента сети

[!INCLUDE [add-network-segment-steps](includes/add-network-segment-steps.md)]

## <a name="next-steps"></a>Дальнейшие действия

В рамках этого руководства вы создали сегмент сети NSX-T, который будет использоваться для виртуальных машин в vCenter. 

Теперь вы можете выполнить следующие действия: 

- [Создание и администрирование DHCP для Решения Azure VMware](manage-dhcp.md)
- [Создание библиотеки содержимого для развертывания виртуальных машин в Решении Azure VMware](deploy-vm-content-library.md) 
- [Настройка пиринга между локальной средой и частным облаком](tutorial-expressroute-global-reach-private-cloud.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
