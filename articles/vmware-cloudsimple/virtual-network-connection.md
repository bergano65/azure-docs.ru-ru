---
title: Подключите виртуальную сеть Azure к CloudSimple с помощью Решения ExpressRoute - Azure VMware от CloudSimple
description: Описывает, как получить вглядывающуюся информацию для подключения между виртуальной сетью Azure и средой CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6b20ee4e04a4443529ecceca8c6fc2206f7df39d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77563989"
---
# <a name="connect-azure-virtual-network-to-cloudsimple-using-expressroute"></a>Подключение виртуальной сети Azure к CloudSimple с помощью ExpressRoute

Сеть Private Cloud можно расширить до виртуальной сети Azure и ресурсов Azure. Подключение ExpressRoute позволяет получить доступ к ресурсам, работающим в подписке Azure, из вашего частного облака.

## <a name="request-authorization-key"></a>Запрос ключа авторизации

Ключ авторизации необходим для подключения ExpressRoute между приватным облаком и виртуальной сетью Azure. Чтобы получить ключ, подайте билет в <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">службу поддержки.</a>  Используйте следующую информацию в запросе:

* Тип выпуска: **Технический**
* Подписка: **Выберите подписку, в которой развернут сервис CloudSimple**
* Сервис: **Решение VMware от CloudSimple**
* Тип проблемы: **Запрос на обслуживание**
* Подтип проблемы: **ключ авторизации для подключения Azure VNET**
* Тема: **Запрос ключа авторизации для подключения Azure VNET**

## <a name="get-peering-information-from-cloudsimple-portal"></a>Получайте информацию о пиринге с портала CloudSimple

Чтобы настроить соединение, необходимо установить соединение между виртуальной сетью Azure и средой CloudSimple.  В рамках процедуры необходимо предоставить элементу URI и ключ авторизации. Получите ключ uri и авторизации с [портала CloudSimple.](access-cloudsimple-portal.md)  Выберите **сеть** в боковом меню, а затем выберите **подключение к сети Azure.** Или выберите **учетную запись** в боковом меню, а затем выберите **сетевое соединение Azure.**

Копирование одноранговой цепи URI и для ключа авторизации для каждого из регионов с помощью значка *копирования.* Для каждого региона CloudSimple, который необходимо подключить:

1. Нажмите **Копия,** чтобы скопировать URI. Вставьте его в файл, где он может быть доступен для добавления на портал Azure.  
2. Нажмите **Копия,** чтобы скопировать ключ авторизации и вставить его в файл, а также.

Копирование ключа авторизации и элемента речного контура URI, нахуруемого в **доступном** состоянии.  **Используемый** статус указывает на то, что ключ уже используется для создания виртуального сетевого соединения.

![Страница виртуального подключения к сети](media/virtual-network-connection.png)

Подробнее о настройке виртуальной сети Azure на ссылку CloudSimple можно узнать о том, как [подключить среду CloudSimple Private Cloud к виртуальной сети Azure с помощью ExpressRoute.](azure-expressroute-connection.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Виртуальное сетевое подключение Azure к частному облаку](azure-expressroute-connection.md)
* [Подключение к предприимительной сети с помощью Azure ExpressRoute](on-premises-connection.md)
