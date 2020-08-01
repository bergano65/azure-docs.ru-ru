---
title: Настройка операций Вреализе для решения VMware для Azure (AVS)
description: ''
ms.topic: how-to
ms.date: 08/06/2020
ms.openlocfilehash: 57b513b681c4d2522b1c92946aab80fe8ba95746
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87476197"
---
# <a name="set-up-vrealize-operations-for-azure-vmware-solution-avs"></a>Настройка операций Вреализе для решения VMware для Azure (AVS)


Вреализе Operations Manager — это платформа управления операциями, позволяющая администраторам инфраструктуры VMware отслеживать системные ресурсы. Эти системные ресурсы могут быть уровня приложения или инфраструктуры (как физических, так и виртуальных) объектов. Исторически большинство администраторов VMware использовали Вреализе операции для мониторинга и управления компонентами частного облака VMware — vCenter, ESXi, НСКС-T, vSAN и гибридное облачное расширение (ХККС). Каждое частное облако AVS подготавливается с помощью выделенного vCenter, НСКС-T, vSAN и ХККС развертывания. 

Тщательно проверьте [перед началом](#before-you-begin) и [предварительными требованиями](#prerequisites) . Затем мы рассмотрим две стандартные топологии развертывания для Вреализе Operations Manager с AVS:

> [!div class="checklist"]
> * [Локальные операции Вреализе для управления развертыванием AVS](#on-premises-vrealize-operations-managing-avs-deployment)
> * [Операции Вреализе, выполняемые при развертывании AVS](#vrealize-operations-running-on-avs-deployment)

## <a name="before-you-begin"></a>Подготовка к работе
* Ознакомьтесь с [документацией по вреализе Operations Manager](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) , чтобы узнать больше о развертывании операций вреализе. 
* Ознакомьтесь с основными [сериями руководств](tutorial-network-checklist.md)по определению программного обеспечения AVS для центра обработки данных (SDDC).
* При необходимости ознакомьтесь с документацией по продукту [Вреализе Operations Remote Controller](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-263F9219-E801-4383-8A59-E84F3D01ED6B.html) для локальных операций вреализе, управляющих развертыванием AVS. 



## <a name="prerequisites"></a>Предварительные требования
* VPN или Azure ExpressRoute должны быть настроены между локальным и AVS SDDC.
* Частное облако AVS развернуто в Azure.



## <a name="on-premises-vrealize-operations-managing-avs-deployment"></a>Локальные операции Вреализе для управления развертыванием AVS
У большинства клиентов есть локальное развертывание операций Вреализе, используемых для управления одним или несколькими локальными доменами vCenter. Когда клиенты подготавливают новое частное облако AVS в Azure, они обычно подключают свою локальную среду с AVS с помощью Azure ExpressRoute или VPN-решения уровня 3, как показано ниже.   

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-1.png" alt-text="Локальные операции Вреализе для управления развертыванием AVS"  border="false":::

Чтобы расширить возможности Вреализе операций в частном облаке AVS, создайте[ экземпляр адаптера для ресурсов частного облака AVS](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.config.doc/GUID-640AD750-301E-4D36-8293-1BFEB67E2600.html) , чтобы собираются данные из частного облака AVS и перенесите их в локальные операции вреализе. Локальный экземпляр Вреализе Operations Manager может напрямую подключаться к vCenter и НСКС-T Manager в AVS или при необходимости развернуть удаленный сборщик операций Вреализе в частном облаке AVS. Удаленный сборщик операций Вреализе сжимает и шифрует данные, собранные из частного облака AVS, перед отправкой через сеть ExpressRoute или VPN в Вреализе Operations Manager, выполняющихся локально. 

> [!TIP]
> Пошаговое руководство по установке Вреализе Operations Manager см. в [документации по VMware](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) . 



## <a name="vrealize-operations-running-on-avs-deployment"></a>Операции Вреализе, выполняемые при развертывании AVS

Другим вариантом развертывания является развертывание экземпляра Вреализе Operations Manager в одном из кластеров vSphere в частном облаке AVS, как показано ниже. 

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-2.png" alt-text="Операции Вреализе, выполняемые в AVS" border="false":::

После развертывания экземпляра AVS операций Вреализе можно настроить операции Вреализе для получения данных из vCenter, ESXi, НСКС-T, vSAN и ХККС. 

> [!TIP]
> Пошаговое руководство по установке Вреализе Operations Manager см. в [документации по VMware](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) .




<!-- LINKS - external -->


<!-- LINKS - internal -->




