---
title: Решение Azure VMware от CloudSimple - Клиент Access vSphere
description: Описывает, как получить доступ к vCenter вашего частного облака.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 18d9463bc512257034860e1188372879524924f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022670"
---
# <a name="access-your-private-cloud-vcenter-portal"></a>Доступ к порталу Private Cloud vCenter

Вы можете запустить свой частный облачный vCenter портал с портала Azure или портала CloudSimple.  портал vCenter позволяет управлять инфраструктурой VMware в личном облаке.

## <a name="before-you-begin"></a>Перед началом

Сетевое соединение должно быть установлено и разрешение имени DNS должно быть включено для доступа к порталу vCenter.  Вы можете установить сетевое подключение к частному облаку, используя любой из приведенных ниже вариантов.

* [Подключайтесь от находины к CloudSimple с помощью ExpressRoute](on-premises-connection.md)
* [Налажить VPN соединение с вашим облачным частным облаком](set-up-vpn.md)

Чтобы настроить разрешение dNS-имен компонентов инфраструктуры Private Cloud VMware, [см.](on-premises-dns-setup.md)

## <a name="sign-in-to-azure"></a>Вход в Azure

Войти на портал Azure по адресу [https://portal.azure.com](https://portal.azure.com).

## <a name="access-vcenter-from-azure-portal"></a>Доступ vCenter с портала Azure

Вы можете запустить портал vCenter вашего частного облака с портала Azure.

1. Выберите **все услуги**.

2. Поиск **облачных сервисов CloudSimple**.

3. Выберите сервис CloudSimple вашего частного облака, к которому вы хотите подключиться.

4. На странице **Обзор** нажмите **Посмотреть VMware Частные облака**

    ![Обзор сервиса CloudSimple](media/cloudsimple-service-overview.png)

5. Выберите частное облако из списка частных облаков и нажмите **Кнопка Запуска vSphere Клиента.**

    ![Запуск vSphere Клиент](media/cloudsimple-service-launch-vsphere-client.png)

## <a name="access-vcenter-from-cloudsimple-portal"></a>Доступ vCenter с портала CloudSimple

Вы можете запустить портал vCenter вашего частного облака с портала CloudSimple.

1. Доступ к [порталу CloudSimple](access-cloudsimple-portal.md).

2. Из **ресурсов** выберите частное облако, к которому вы хотите получить доступ, и нажмите на **Launch vSphere Client.**

    ![Запуск vSphere Клиент - Ресурсы](media/cloudsimple-portal-resources-launch-vcenter.png)

3. Вы также можете запустить портал vCenter с краткого экрана вашего частного облака.

    ![Запуск vSphere Клиент - Резюме](media/cloudsimple-resources-summary-launch-vcenter.png)

## <a name="next-steps"></a>Дальнейшие действия

* [Создание и управление VLAN/подсетями для ваших частных облаков](create-vlan-subnet.md)
* [Модель разрешения на частное облако Cloud Компании VMware vCenter](learn-private-cloud-permissions.md)