---
title: Решение Azure VMware от CloudSimple - Подключение частного облака к сети Azure с помощью ExpressRoute
description: Описывает, как подключить среду облачного облака Cloud Кигот с виртуальной сетью Azure с помощью ExpressRoute
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9bb68ec68f4de646239477ceeaac50a7a33989fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77015241"
---
# <a name="connect-your-cloudsimple-private-cloud-environment-to-the-azure-virtual-network-using-expressroute"></a>Подключите среду облачного облака CloudSimple в виртуальную сеть Azure с помощью ExpressRoute

Частное облако CloudSimple может быть подключено к виртуальной сети Azure с помощью Azure ExpressRoute.  Это соединение с высокой пропускной способностью с низкой задержкой позволяет получить доступ к службам, работающим в подписке Azure, из среды Private Cloud.

Виртуальное сетевое соединение позволяет:

* Используйте Azure в качестве резервной мишени для виртуальных машин в частном облаке.
* Развертывание серверов KMS в подписке Azure для шифрования хранилища данных Private Cloud vSAN.
* Используйте гибридные приложения, где веб-уровень приложения работает в общедоступном облаке, в то время как уровни приложений и баз данных работают в вашем частном облаке.

![Подключение Azure ExpressRoute к виртуальной сети](media/cloudsimple-azure-network-connection.png)

## <a name="set-up-a-virtual-network-connection"></a>Настройка виртуального сетевого соединения

Чтобы настроить виртуальное сетевое соединение с приватным облаком, вам нужен ключ авторизации, уривер-модель и доступ к подписке Azure. Эта информация доступна на странице виртуального подключения к сети на портале CloudSimple. Для инструкций [см. Получение внимательной информации для виртуальной сети Azure в связи CloudSimple.](virtual-network-connection.md) Если у вас возникли проблемы с получением информации, отправьте <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">запрос на поддержку.</a>

> [!TIP]
> Если у вас уже есть виртуальная сеть Azure, подсеть шлюза и виртуальный сетевой шлюз, вы можете перейти к шагу 4.

1. Создайте виртуальную сеть в подписке Azure и убедитесь, что выбранное адресное пространство отличается от адресного пространства вашего частного облака.  Если у вас уже есть виртуальная сеть Azure, вы можете использовать существующую.  Для получения подробной информации [см. Создать виртуальную сеть с помощью портала Azure](../virtual-network/quick-create-portal.md).
2. Создайте подсеть шлюза в виртуальной сети Azure.  Если в виртуальной сети Azure уже есть подсеть шлюза, вы можете использовать существующую. Для получения подробной информации [см.](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet)
3. Создайте виртуальный сетевой шлюз в виртуальной сети.  Если у вас есть существующий виртуальный сетевой шлюз, вы можете использовать существующий. Для получения подробной информации [см.](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway)
4. Создайте соединение между виртуальной сетью и приватным облаком, выкупив ключ авторизации, описанный в [Connect, виртуальную сеть к схеме - различная подписка.](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)

> [!WARNING]
> Если вы используете существующий виртуальный сетевой шлюз и он имеет подключение ExpressRoute к тому же местоположению, что и схема CloudSimple ExpressRoute, соединение не будет установлено.  Создайте новую виртуальную сеть и выполните предыдущие шаги.

## <a name="test-the-virtual-network-connection"></a>Проверьте виртуальное сетевое соединение

После создания соединения можно проверить состояние соединения, выбрав **свойства** в **настройках.**  Состояние и положение должно показать **успешно**.

![Статус подключения](media/azure-expressroute-connection.png)

Для тестирования виртуального сетевого соединения:

1. Создайте виртуальную машину в подписке Azure.
2. Найдите IP-адрес вашего Private Cloud vCenter (обратитесь к приветственной электронной почте).
3. Пинг ваш Cloud vCenter из виртуальной машины, созданной в виртуальной сети Azure.
4. Пинг ваш Azure виртуальная машина из виртуальной машины работает в вашем частном облаке vCenter.

Если у вас возникли проблемы с созданием соединения, отправьте <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">запрос на поддержку.</a>
