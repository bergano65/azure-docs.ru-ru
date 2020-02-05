---
title: Решения Azure VMware (AVS) — доступ к клиенту vSphere
description: В этой статье описывается, как получить доступ к vCenter из частного облака AVS.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ad018ea89b194d42ab1867a0569725c4c3680f7d
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022670"
---
# <a name="access-your-avs-private-cloud-vcenter-portal"></a>Доступ к порталу vCenter частного облака "AVS"

Портал vCenter частного облака "AVS" можно запустить с портала портал Azure или AVS. Портал vCenter позволяет управлять инфраструктурой VMware в частном облаке AVS.

## <a name="before-you-begin"></a>Перед началом работы

Необходимо установить сетевое подключение, а разрешение DNS-имен должно быть включено для доступа к порталу vCenter. Вы можете установить сетевое подключение к частному облаку AVS, используя любой из приведенных ниже вариантов.

* [Подключение из локальной среды к AVS с помощью ExpressRoute](on-premises-connection.md)
* [Настройка VPN-подключения к частному облаку AVS](set-up-vpn.md)

Сведения о настройке разрешения имен DNS для компонентов инфраструктуры частного облака версии AVS см. в [статье Настройка DNS для разрешения имен для доступа к закрытому облаку AVS из локальных рабочих станций](on-premises-dns-setup.md) .

## <a name="sign-in-to-azure"></a>Войдите в Azure

Войдите на портал Azure по адресу [https://portal.azure.com](https://portal.azure.com).

## <a name="access-vcenter-from-azure-portal"></a>Доступ к vCenter из портал Azure

Портал vCenter для частного облака AVS можно запустить из портал Azure.

1. Выбор пункта **Все службы**.

2. Поиск **служб AVS**.

3. Выберите службу AVS частного облака AVS, к которому необходимо подключиться.

4. На странице **Обзор** щелкните **Просмотр частных ОБЛАКов VMware AVS**

    ![Общие сведения о службе AVS](media/cloudsimple-service-overview.png)

5. Выберите частное облако AVS в списке частных облаков AVS и щелкните **запустить vSphere Client**.

    ![Запуск клиента vSphere](media/cloudsimple-service-launch-vsphere-client.png)

## <a name="access-vcenter-from-avs-portal"></a>Доступ к vCenter с портала AVS

Портал vCenter для частного облака AVS можно запустить с портала AVS.

1. Доступ к [порталу AVS](access-cloudsimple-portal.md).

2. В списке **ресурсов** выберите частное облако AVS, к которому требуется получить доступ, и щелкните **запустить клиент vSphere**.

    ![Запуск vSphere Client-Resources](media/cloudsimple-portal-resources-launch-vcenter.png)

3. Вы также можете запустить портал vCenter с экрана сводки частного облака AVS.

    ![Запуск клиента vSphere — сводка](media/cloudsimple-resources-summary-launch-vcenter.png)

## <a name="next-steps"></a>Дальнейшие действия

* [Создание виртуальных ЛС и подсетей для частных облаков AVS и управление ими](create-vlan-subnet.md)
* [Модель разрешений частного облака AVS для VMware vCenter](learn-private-cloud-permissions.md)