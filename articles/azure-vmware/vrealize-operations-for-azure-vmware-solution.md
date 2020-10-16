---
title: Настройка операций Вреализе для решения VMware для Azure
description: Узнайте, как настроить операции Вреализе для частного облака решения Azure VMware.
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 9e512d107ddc4d9bca28323658d09f4b4b378dc3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91580678"
---
# <a name="set-up-vrealize-operations-for-azure-vmware-solution"></a>Настройка операций Вреализе для решения VMware для Azure


Вреализе Operations Manager — это платформа управления операциями, позволяющая администраторам инфраструктуры VMware отслеживать системные ресурсы. Эти системные ресурсы могут быть уровня приложения или инфраструктуры (как физических, так и виртуальных) объектов. Исторически большинство администраторов VMware использовали Вреализе операции для мониторинга и управления компонентами частного облака VMware — vCenter, ESXi, НСКС-T, vSAN и гибридное облачное расширение (ХККС). Каждое частное облако решения Azure VMware подготавливается с помощью выделенного vCenter, НСКС-T, vSAN и ХККС развертывания. 

Тщательно проверьте [перед началом](#before-you-begin) и [предварительными требованиями](#prerequisites) . Затем мы рассмотрим две стандартные топологии развертывания для Вреализе Operations Manager с помощью решения VMware для Azure:

> [!div class="checklist"]
> * [Локальные операции Вреализе Управление развертыванием решения Azure VMware](#on-premises-vrealize-operations-managing-azure-vmware-solution-deployment)
> * [Операции Вреализе, выполняемые в развертывании решения VMware в Azure](#vrealize-operations-running-on-azure-vmware-solution-deployment)

## <a name="before-you-begin"></a>Перед началом
* Ознакомьтесь с [документацией по вреализе Operations Manager](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) , чтобы узнать больше о развертывании операций вреализе. 
* Ознакомьтесь с [циклом учебников](tutorial-network-checklist.md) по программно определяемому центру обработки данных (SDDC) Решения Azure VMware.
* При необходимости ознакомьтесь с документацией по продукту [Вреализе Operations Remote Controller](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-263F9219-E801-4383-8A59-E84F3D01ED6B.html) для локальных операций вреализе, управляющих развертыванием решения VMware в Azure. 



## <a name="prerequisites"></a>Предварительные требования
* VPN или Azure ExpressRoute должны быть настроены между локальной средой и решением Azure VMware (SDDC).
* Частное облако решения Azure VMware развернуто в Azure.



## <a name="on-premises-vrealize-operations-managing-azure-vmware-solution-deployment"></a>Локальные операции Вреализе Управление развертыванием решения Azure VMware
У большинства клиентов есть локальное развертывание операций Вреализе, используемых для управления одним или несколькими локальными доменами vCenter. Когда клиенты подготавливают новое частное облако Azure VMware в Azure, они обычно подключают свою локальную среду с помощью решения Azure VMware, используя Azure ExpressRoute или VPN-решение уровня 3, как показано ниже.   

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-1.png" alt-text="Локальные операции Вреализе Управление развертыванием решения Azure VMware"  border="false":::

Чтобы расширить возможности операций Вреализе в частном облаке решения Azure VMware, создайте [экземпляр адаптера для ресурсов частного облака](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.config.doc/GUID-640AD750-301E-4D36-8293-1BFEB67E2600.html) решения Azure VMware, чтобы собираются данные из частного облака решения Azure VMware и перенесите их в локальные операции вреализе. Локальный экземпляр Вреализе Operations Manager может напрямую подключаться к vCenter и диспетчеру НСКС-T в решении VMware Azure или при необходимости развернуть удаленный сборщик операций Вреализе в частном облаке решения Azure VMware. Удаленный сборщик операций Вреализе сжимает и шифрует данные, собранные из частного облака решения Azure VMware, перед отправкой через сеть ExpressRoute или VPN в Вреализе Operations Manager, выполняющихся локально. 

> [!TIP]
> Пошаговое руководство по установке Вреализе Operations Manager см. в [документации по VMware](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) . 



## <a name="vrealize-operations-running-on-azure-vmware-solution-deployment"></a>Операции Вреализе, выполняемые в развертывании решения VMware в Azure

Другой вариант развертывания — развертывание экземпляра Вреализе Operations Manager в одном из кластеров vSphere в частном облаке решения Azure VMware, как показано ниже. 

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-2.png" alt-text="Локальные операции Вреализе Управление развертыванием решения Azure VMware" border="false":::

После развертывания экземпляра решения Azure VMware для операций Вреализе можно настроить операции Вреализе для получения данных из vCenter, ESXi, НСКС-T, vSAN и ХККС. 

> [!TIP]
> Пошаговое руководство по установке Вреализе Operations Manager см. в [документации по VMware](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) .


## <a name="known-limitations"></a>Известные ограничения

- **cloudadmin@vsphere.local**Пользователь в решении Azure VMware имеет [ограниченные привилегии](concepts-role-based-access-control.md). Сбор гостевых памяти с помощью средств VMware не поддерживается для виртуальных машин в решении VMware для Azure. В этом случае продолжают работать активные и потребленные использования памяти.
- Оптимизация рабочей нагрузки для бизнеса на основе узла не работает, так как решения VMware для Azure управляют конфигурациями кластера, включая параметры DRS.
- Оптимизация рабочей нагрузки для размещения между кластерами в SDDC с использованием бизнеса на основе кластера полностью поддерживается с Вреализе Operations Manager 8,0 и выше. Однако оптимизация рабочей нагрузки не учитывает пулы ресурсов и размещает виртуальные машины на уровне кластера. Пользователь может вручную исправить это в интерфейсе vCenter Server решения Azure VMware.
- Вы не можете войти в Вреализе Operations Manager с помощью решения Azure VMware vCenter Server учетные данные. 
- Решение VMware для Azure не поддерживает подключаемый модуль Operations Manager Вреализе.

При подключении службы vCenter для Azure VMware к Вреализе Operations Manager с помощью vCenter Server облачной учетной записи вы увидите следующее предупреждение:

:::image type="content" source="./media/vrealize-operations-manager/warning-adapter-instance-creation-succeeded.png" alt-text="Локальные операции Вреализе Управление развертыванием решения Azure VMware":::

Это предупреждение возникает, если **cloudadmin@vsphere.local** пользователь в решении Azure VMware не имеет достаточных привилегий для выполнения всех vCenter Server действий, необходимых для регистрации. Однако привилегии достаточно для того, чтобы экземпляр адаптера произйдет сбор данных, как показано ниже:

:::image type="content" source="./media/vrealize-operations-manager/adapter-instance-to-perform-data-collection.png" alt-text="Локальные операции Вреализе Управление развертыванием решения Azure VMware":::

Дополнительные сведения см. в разделе [привилегии, необходимые для настройки экземпляра адаптера vCenter](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.core.doc/GUID-3BFFC92A-9902-4CF2-945E-EA453733B426.html).

<!-- LINKS - external -->


<!-- LINKS - internal -->




