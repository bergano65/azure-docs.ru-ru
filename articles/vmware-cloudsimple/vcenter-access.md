---
title: Решение VMware для Azure по Клаудсимпле-Access vSphere Client
description: Описание доступа к vCenter вашего частного облака.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 18d9463bc512257034860e1188372879524924f7
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70196131"
---
# <a name="access-your-private-cloud-vcenter-portal"></a>Доступ к порталу для работы с частным облаком vCenter

Вы можете запустить портал для частного облака vCenter на портале портал Azure или Клаудсимпле.  Портал vCenter позволяет управлять инфраструктурой VMware в частном облаке.

## <a name="before-you-begin"></a>Перед началом работы

Необходимо установить сетевое подключение, а разрешение DNS-имен должно быть включено для доступа к порталу vCenter.  Вы можете установить сетевое подключение к частному облаку, используя любой из приведенных ниже вариантов.

* [Подключение из локальной среды к Клаудсимпле с помощью ExpressRoute](on-premises-connection.md)
* [Настройка VPN-подключения к частному облаку Клаудсимпле](set-up-vpn.md)

Сведения о настройке разрешения DNS-имен для компонентов инфраструктуры частного облака VMware см. в [статье Настройка DNS для разрешения имен для доступа к службе VCenter облака с локальных рабочих станций](on-premises-dns-setup.md) .

## <a name="sign-in-to-azure"></a>Войдите в Azure

Войдите на портал Azure по адресу [https://portal.azure.com](https://portal.azure.com).

## <a name="access-vcenter-from-azure-portal"></a>Доступ к vCenter из портал Azure

Портал vCenter для частного облака можно запустить из портал Azure.

1. Выбор пункта **Все службы**.

2. Выполните поиск по запросу **Клаудсимпле Services**.

3. Выберите службу Клаудсимпле частного облака, к которой необходимо подключиться.

4. На странице **Обзор** щелкните **Просмотр частных облаков VMware**

    ![Общие сведения о службе Клаудсимпле](media/cloudsimple-service-overview.png)

5. Выберите частное облако в списке частных облаков и щелкните **запустить vSphere Client**.

    ![Запуск клиента vSphere](media/cloudsimple-service-launch-vsphere-client.png)

## <a name="access-vcenter-from-cloudsimple-portal"></a>Доступ к vCenter с портала Клаудсимпле

Портал vCenter частного облака можно запустить на портале Клаудсимпле.

1. Доступ к [порталу клаудсимпле](access-cloudsimple-portal.md).

2. В списке **ресурсов** выберите частное облако, к которому требуется получить доступ, и щелкните **запустить vSphere Client**.

    ![Запуск vSphere Client-Resources](media/cloudsimple-portal-resources-launch-vcenter.png)

3. Вы также можете запустить портал vCenter с экрана сводки частного облака.

    ![Запуск клиента vSphere — сводка](media/cloudsimple-resources-summary-launch-vcenter.png)

## <a name="next-steps"></a>Следующие шаги

* [Создание виртуальных ЛС и подсетей для частных облаков и управление ими](create-vlan-subnet.md)
* [Модель разрешений частного облака Клаудсимпле для VMware vCenter](learn-private-cloud-permissions.md)