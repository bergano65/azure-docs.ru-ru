---
title: Развертывание и настройка Решения Azure VMware
description: Узнайте, как использовать собранную на этапе планирования информацию для развертывания частного облака Решения Azure VMware.
ms.topic: tutorial
ms.date: 12/24/2020
ms.openlocfilehash: f2b6f3c4ad82117fee96e0c2e5973a7011384d48
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/25/2021
ms.locfileid: "98760889"
---
# <a name="deploy-and-configure-azure-vmware-solution"></a>Развертывание и настройка Решения Azure VMware

Выполняя шаги, представленные в этой статье, вы будете использовать для развертывания Решения Azure VMware сведения из [раздела о планировании](production-ready-deployment-steps.md). 

>[!IMPORTANT]
>Если вы еще не определили эти сведения, вернитесь к [разделу планирования](production-ready-deployment-steps.md), прежде чем продолжить работу.

## <a name="register-the-resource-provider"></a>Регистрация поставщика ресурсов

[!INCLUDE [register-resource-provider-steps](includes/register-resource-provider-steps.md)]


## <a name="deploy-azure-vmware-solution"></a>Развертывание Решения Azure VMware

Используйте сведения, которые вы собрали согласно инструкциям в статье [Планирование развертывания Решения Azure VMware](production-ready-deployment-steps.md).

>[!NOTE]
>Чтобы развернуть Решение Azure VMware, у вас должны быть разрешения уровня "Участник" или выше для подписки.

[!INCLUDE [create-avs-private-cloud-azure-portal](includes/create-private-cloud-azure-portal-steps.md)]

>[!NOTE]
>Полный обзор этого шага см. в видео [Azure VMware Solution: Deployment](https://www.youtube.com/embed/gng7JjxgayI) (Решение Azure VMware: развертывание).

## <a name="create-the-jump-box"></a>Создание переходной среды

>[!IMPORTANT]
>Если вы решили не задавать значение для параметра **Виртуальная сеть** на этапе начальной подготовки на экране **Создание частного облака**, выполните действия, описанные в статье [Руководство по настройке сети для частного облака VMware в Azure](tutorial-configure-networking.md), **перед тем** как продолжать работу с этим разделом.  

Развернув Решение Azure VMware, вы создадите переходную среду для виртуальной сети, которая подключается к vCenter и NSX. После настройки каналов ExpressRoute и Global Reach ExpressRoute переходная среда больше не потребуется.  Однако с ее помощью можно легко получить доступ к vCenter и NSX в Решении Azure VMware.  

:::image type="content" source="media/pre-deployment/jump-box-diagram.png" alt-text="Создание инсталляционного сервера в Решении Azure VMware" border="false" lightbox="media/pre-deployment/jump-box-diagram.png":::

Чтобы создать виртуальную машину в виртуальной сети, которую вы [указали или создали при развертывании](production-ready-deployment-steps.md#attach-virtual-network-to-azure-vmware-solution), выполните следующие действия: 

[!INCLUDE [create-avs-jump-box-steps](includes/create-jump-box-steps.md)]

## <a name="connect-to-a-virtual-network-with-expressroute"></a>Подключение к виртуальной сети с помощью ExpressRoute

>[!IMPORTANT]
>Если вы уже указали виртуальную сеть на экране развертывания в Azure, перейдите к следующему разделу.

Если вы не указали виртуальную сеть на шаге развертывания, однако хотите подключить службу ExpressRoute Решения Azure VMware к существующему шлюзу ExpressRoute, выполните следующие действия.

[!INCLUDE [connect-expressroute-to-vnet](includes/connect-expressroute-vnet.md)]

## <a name="verify-network-routes-advertised"></a>Проверка объявленных сетевых маршрутов

Переходная среда размещена в виртуальной сети, в которой Решение Azure VMware подключается через канал ExpressRoute.  Перейдите к сетевому интерфейсу переходной среды в Azure и [проверьте фактические маршруты](../virtual-network/manage-route-table.md#view-effective-routes).

В списке фактических маршрутов должны быть сети, созданные при развертывании Решения Azure VMware. Вы увидите несколько сетей, которые являются производными от [`/22`сети, указанной](production-ready-deployment-steps.md#ip-address-segment) [на шаге развертывания](#deploy-azure-vmware-solution), описанном ранее в этой статье.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-effective-routes.png" alt-text="Проверка сетевых маршрутов, объявляемых Решением Azure VMware виртуальной сети Azure" lightbox="media/pre-deployment/azure-vmware-solution-effective-routes.png":::

В этом примере на основе сети 10.74.72.0/22, настроенной во время развертывания, создаются производные сети /24.  Если отображаются примерно такие же данные, вы можете подключаться к vCenter в Решении Azure VMware.

## <a name="connect-and-sign-in-to-vcenter-and-nsx-t"></a>Подключение и вход в vCenter и NSX-T

Войдите в переходную среду, созданную на предыдущем шаге. После входа откройте веб-браузер, перейдите к консоли администрирования vCenter и NSX-T и выполните вход.  

IP-адреса и учетные данные консоли администрирования vCenter и NSX-T можно найти на портале Azure.  Выберите частное облако, а затем в представлении **Обзор** щелкните **Удостоверение > По умолчанию**. 

## <a name="create-a-network-segment-on-azure-vmware-solution"></a>Создание сегмента сети в Решении Azure VMware

Среда NSX-T используется для создания сегментов сети в среде Решения Azure VMware.  Сети, которые нужно создать, вы определили при прохождении [раздела планирования](production-ready-deployment-steps.md).  Если вы их не определили, вернитесь в [раздел планирования](production-ready-deployment-steps.md), прежде чем продолжить.

>[!IMPORTANT]
>Убедитесь, что указанный блок сетевых адресов CIDR не перекрывается с другими адресами в Azure или локальных средах.  

Выполните действия, описанные в [учебнике по созданию сегмента сети NSX-T в Решении Azure VMware](tutorial-nsx-t-network-segment.md), чтобы создать сегмент сети NSX-T в Решении Azure VMware.

## <a name="verify-advertised-nsx-t-segment"></a>Проверка объявленного сегмента NSX-T

Вернитесь к шагу [Проверка объявленных сетевых маршрутов](#verify-network-routes-advertised). В списке вы увидите другие маршруты, представляющие сегменты сети, созданные на предыдущем шаге.  

Для виртуальных машин назначьте сегменты, созданные на шаге [Создание сегмента сети в Решении Azure VMware](#create-a-network-segment-on-azure-vmware-solution).  

Так как требуется DNS, укажите DNS-сервер, который вы хотите использовать.  

- Если настроена служба ExpressRoute Global Reach, используйте любой DNS-сервер, предназначенный для локальных сред.  
- Если у вас есть DNS-сервер в Azure, используйте его.  
- Если его нет, используйте любой DNS-сервер, применяемый в переходной среде.

>[!NOTE]
>На этом шаге необходимо определить DNS-сервер, а настройку выполнять не требуется.

## <a name="optional-provide-dhcp-services-to-nsx-t-network-segment"></a>Предоставление служб DHCP для сегмента сети NSX-T (необязательно)

Если вы планируете использовать DHCP в сегментах NSX-T, продолжайте работу с этим разделом. В противном случае перейдите к разделу [Добавление виртуальной машины в сегмент сети NSX-T](#add-a-vm-on-the-nsx-t-network-segment).  

Теперь, когда вы создали сегмент сети NSX-T, вы можете создавать и администрировать DHCP в Решении Azure VMware двумя способами:

* Если вы используете NSX-T для размещения DHCP-сервера, необходимо [создать DHCP-сервер](manage-dhcp.md#create-a-dhcp-server) и [ретранслятор для этого сервера](manage-dhcp.md#create-dhcp-relay-service). 
* Если вы используете сторонний внешний DHCP-сервер в сети, необходимо [создать службу ретранслятора DHCP](manage-dhcp.md#create-dhcp-relay-service).  Для этого варианта [необходимо только настроить ретрансляцию](manage-dhcp.md#create-dhcp-relay-service).


## <a name="add-a-vm-on-the-nsx-t-network-segment"></a>Добавление виртуальной машины в сегмент сети NSX-T

В Решении Azure VMware для vCenter разверните виртуальную машину и используйте ее для проверки подключения от сетей Решения Azure VMware к:

- Интернету;
- Виртуальные сети Azure
- Локальная система.  

Разверните виртуальную машину так же, как и в любой другой среде vSphere.  Подключите виртуальную машину к одному из сегментов сети, созданных ранее в NSX-T.  

>[!NOTE]
>При настройке DHCP-сервера вы получаете конфигурацию сети для виртуальной машины (не забудьте настроить эту область).  Если планируется статистическая настройка, выполните ее так, как обычно.

## <a name="test-the-nsx-t-segment-connectivity"></a>Тестирование подключения сегмента NSX-T

Войдите на виртуальную машину, созданную на предыдущем шаге, и проверьте подключение.

1. Проверьте связь с IP-адресом в Интернете.
2. Перейдите на веб-сайт в веб-браузере.
3. Проверьте связь с переходной средой, расположенной в виртуальной сети Azure.

Теперь Решение Azure VMware готово к работе и вы успешно установили подключение к виртуальной сети Azure и Интернету, а также из них.

## <a name="next-steps"></a>Дальнейшие действия

В следующем разделе вы узнаете, как подключить Решение Azure VMware к локальной сети через ExpressRoute.
> [!div class="nextstepaction"]
> [Подключение Решения Azure VMware к локальной среде](azure-vmware-solution-on-premises.md)
