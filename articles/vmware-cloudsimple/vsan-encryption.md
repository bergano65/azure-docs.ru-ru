---
title: Решение Azure VMware от CloudSimple - Накончать шифрование vSAN для частного облака
description: Описывает, как настроить функцию шифрования программного обеспечения vSAN, чтобы ваше облачное частное облако работало с сервером управления ключами, работающим в виртуальной сети Azure.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 638b60bd3612fa25350ecef0a738fea75c2f53d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77020647"
---
# <a name="configure-vsan-encryption-for-cloudsimple-private-cloud"></a>Накончать шифрование vSAN для облачного частного облака

Вы можете настроить функцию шифрования программного обеспечения vSAN, чтобы ваше облачное частное облако работало с сервером управления ключами, работающим в виртуальной сети Azure.

VMware требует использования внешнего инструмента KMIP 1.1, отвечающего стороннему серверу управления ключами (KMS) при использовании шифрования vSAN. Вы можете использовать любую поддерживаемую KMS, сертифицированную VMware и доступную для Azure.

В этом руководстве описывается, как использовать HyTrust KeyControl KMS, работающий в виртуальной сети Azure. Аналогичный подход может быть использован для любого другого сертифицированного стороннего решения KMS для vSAN.

Это решение KMS требует от вас:

* Установите, направляйте и управляйте сертифицированным vMware сторонним инструментом KMS в виртуальной сети Azure.
* Предоставьте собственные лицензии на инструмент KMS.
* Нанастройка и управление шифрованием vSAN в частном облаке с помощью стороннего инструмента KMS, работаемого в виртуальной сети Azure.

## <a name="kms-deployment-scenario"></a>Сценарий развертывания KMS

Кластер серверов KMS работает в виртуальной сети Azure и ip дотягивается от Private Cloud vCenter по настроенной подключению Azure ExpressRoute.

![.. /медиа/KMS кластер в виртуальной сети Azure](media/vsan-kms-cluster.png)

## <a name="how-to-deploy-the-solution"></a>Как развернуть решение

Процесс развертывания имеет следующие шаги:

1. [Проверить, что предпосылки выполнены](#verify-prerequisites-are-met)
2. [CloudSimple портал: Получить ExpressRoute Peering информации](#cloudsimple-portal-obtain-expressroute-peering-information)
3. [Портал Azure: Подключите виртуальную сеть к частному облаку](#azure-portal-connect-your-virtual-network-to-your-private-cloud)
4. [Портал Azure: Развертывание кластера KeyControl HyTrust в виртуальной сети](#azure-portal-deploy-a-hytrust-keycontrol-cluster-in-the-azure-resource-manager-in-your-virtual-network)
5. [HyTrust WebUI: Настройка KMIP-сервера](#hytrust-webui-configure-the-kmip-server)
6. [vCenter UI: Налаживайте шифрование vSAN для использования кластера KMS в виртуальной сети Azure](#vcenter-ui-configure-vsan-encryption-to-use-kms-cluster-in-your-azure-virtual-network)

### <a name="verify-prerequisites-are-met"></a>Проверка предпосылок выполнена

Проверить следующие перед развертыванием:

* Выбранный поставщик, инструмент и версия KMS находятся в списке совместимости vSAN.
* Выбранный поставщик поддерживает версию инструмента для запуска в Azure.
* Версия инструмента KMS Azure соответствует KMIP 1.1.
* Уже созданменеджер ресурсов Azure и виртуальная сеть.
* CloudSimple Private Cloud уже создан.

### <a name="cloudsimple-portal-obtain-expressroute-peering-information"></a>CloudSimple портал: Получить ExpressRoute пиринговой информации

Для продолжения настройки требуется ключ авторизации и элементная схема URI для ExpressRoute плюс доступ к подписке Azure. Эта информация доступна на странице виртуального подключения к сети на портале CloudSimple. Для получения инструкций смотрите [Настройка виртуального сетевого соединения с приватным облаком.](virtual-network-connection.md) Если у вас возникли проблемы с получением информации, откройте [запрос на поддержку.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

### <a name="azure-portal-connect-your-virtual-network-to-your-private-cloud"></a>Портал Azure: Подключите виртуальную сеть к частному облаку

1. Создайте виртуальный сетевой шлюз для виртуальной сети, следуя инструкциям в [настройке виртуального сетевого шлюза для ExpressRoute с помощью портала Azure.](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)
2. Свяжите свою виртуальную сеть с схемой CloudSimple ExpressRoute, следуя инструкциям в [подключении виртуальной сети к схеме ExpressRoute с помощью портала.](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)
3. Используйте информацию о схеме CloudSimple ExpressRoute, полученную в приветственном письме от CloudSimple, чтобы связать свою виртуальную сеть с схемой CloudSimple ExpressRoute в Azure.
4. Введите ключ авторизации и одноранговую схему URI, дайте соединению имя и **нажмите OK.**

![Обеспечить CS одноранговой цепи URI при создании виртуальной сети](media/vsan-azureportal01.png) 

### <a name="azure-portal-deploy-a-hytrust-keycontrol-cluster-in-the-azure-resource-manager-in-your-virtual-network"></a>Портал Azure: Развертывание кластера KeyControl HyTrust в менеджере ресурсов Azure в виртуальной сети

Чтобы развернуть кластер HyTrust KeyControl в менеджере ресурсов Azure в виртуальной сети, выполните следующие задачи. Подробности смотрите [в документации HyTrust.](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0)

1. Создайте группу сетевой безопасности Azure (nsg-hytrust) с определенными правилами входящего оборота, следуя инструкциям в документации HyTrust.
2. Создайте пару ключей SSH в Azure.
3. Развертывание начального узла KeyControl из изображения в Azure Marketplace.  Используйте общедоступный ключ ключа, который был создан, и выберите **nsg-hytrust** в качестве группы сетевой безопасности для узла KeyControl.
4. Преобразуйте личный IP-адрес KeyControl на статический IP-адрес.
5. SSH к KeyControl VM с помощью своего публичного IP-адреса и частного ключа ранее упомянутой ключевой пары.
6. При запросе в оболочке `No` SSH выберите узло в качестве начального узла KeyControl.
7. Добавьте дополнительные узлы KeyControl, повторив шаги `Yes` 3-5 этой процедуры и выбрав, когда предлагается добавить к существующему кластеру.

### <a name="hytrust-webui-configure-the-kmip-server"></a>HyTrust WebUI: Настройка сервера KMIP

Перейти к https://*публично-IP*, где *public-ip* является публичным IP-адресом узла KeyControl VM. Выполните следующие шаги из [документации HyTrust](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0).

1. [Настройка сервера KMIP](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/configuring-kmip-server.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____2)
2. [Создание комплекта сертификатов для шифрования VMware](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-user-for-vmcrypt.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____3)

### <a name="vcenter-ui-configure-vsan-encryption-to-use-kms-cluster-in-your-azure-virtual-network"></a>vCenter UI: Налаживайте шифрование vSAN для использования кластера KMS в виртуальной сети Azure

Следуйте инструкциям HyTrust для [создания кластера KMS в vCenter.](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-KMS-Cluster.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____4)

![Добавление деталей кластера KMS в vCenter](media/vsan-config01.png)

В vCenter перейдите в **кластер > нанастройка** и выберите **общий** вариант для vSAN. Включите шифрование и выберите кластер KMS, который ранее был добавлен в vCenter.

![Включить шифрование vSAN и настроить кластер KMS в vCenter](media/vsan-config02.png)

## <a name="references"></a>Ссылки

### <a name="azure"></a>Azure

[Настройка шлюза виртуальной сети для ExpressRoute с помощью портала Azure](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)

[Подключение виртуальной сети к каналу ExpressRoute с помощью портала](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)

### <a name="hytrust"></a>HyTrust

[HyTrust DataControl и Microsoft Azure](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0)

[Настройка сервера KMPI](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/configuring-kmip-server.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____2)

[Создание комплекта сертификатов для шифрования VMware](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-user-for-vmcrypt.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____3)

[Создание кластера KMS в vSphere](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-KMS-Cluster.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____4)
