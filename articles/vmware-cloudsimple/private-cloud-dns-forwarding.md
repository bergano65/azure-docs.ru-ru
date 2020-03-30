---
title: Решение Azure VMware - Переадресировка DNS из частного облака в локон
description: Описывает, как включить сервер CloudSimple Private Cloud DNS для перспективного поиска внутренних ресурсов
author: sharaths-cs
ms.author: b-shsury
ms.date: 02/29/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: aa2af4302613aad23bfd78b4883bbb46c5e5ddbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76961131"
---
# <a name="enable-cloudsimple-private-cloud-dns-servers-to-forward-dns-lookup-of-on-premises-resources-to-your-dns-servers"></a>Включить облачные облачные DNS-серверы Cloud DNS для переадресовывания поиск ресурсов DNS на ваши DNS-серверы

Частные облачные DNS-серверы могут перенаправить поиск DNS на ваши dNS-серверы.  Включение поиска позволяет компонентам Private Cloud vSphere искать любые службы, работающие в вашей предварительной среде, и общаться с ними с помощью полностью квалифицированных доменных имен (ФЗДН).

## <a name="scenarios"></a>Сценарии 

Переадресировка DNS для вашего собственного DNS-сервера позволяет использовать приватное облако для следующих сценариев:

* Используйте Private Cloud в качестве установки аварийного восстановления для решения VMware
* Используйте на территории Active Directory в качестве источника идентификации для вашего private Cloud vSphere
* Используйте HCX для миграции виртуальных машин из закрытых в частное облако

## <a name="before-you-begin"></a>Перед началом

Сетевое подключение должно присутствовать от вашей сети Private Cloud к вашей предварительной сети для переадресовки DNS на работу.  Можно настроить сетевое соединение с помощью:

* [Подключайтесь от находины к CloudSimple с помощью ExpressRoute](on-premises-connection.md)
* [Настройка VPN шлюза от сайта к сайту](https://docs.microsoft.com/azure/vmware-cloudsimple/vpn-gateway#set-up-a-site-to-site-vpn-gateway)

Порты брандмауэра должны быть открыты на этом соединении для переадресовки DNS для работы.  Порты используются TCP порт 53 или UDP порт 53.

> [!NOTE]
> Если вы используете сайт-к-сайт VPN, ваш внутренний DNS сервер подсеть должна быть добавлена как часть на местах префиксы.

## <a name="request-dns-forwarding-from-private-cloud-to-on-premises"></a>Запрос DNS-пересылки из частного облака в локоте

Чтобы включить переадресировку DNS из частного облака в локоте, отправьте [запрос на поддержку,](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)предоставив следующую информацию.

* Тип выпуска: **Технический**
* Подписка: **Подписка, в которой развернут сервис CloudSimple**
* Сервис: **Решение VMware от CloudSimple**
* Тип проблемы: **Консультирование или Как я могу...**
* Подтип проблемы: **Нужна помощь с NW**
* Предоставьте доменное имя вашего предварительного домена в панели деталей.
* Предоставьте список ваших закрытых DNS-серверов, на которые поиск будет перенаправлен из вашего частного облака в деталях панели.

## <a name="next-steps"></a>Дальнейшие действия

* [Подробнее о конфигурации брандмауэра на территории](on-premises-firewall-configuration.md)
* [Конфигурация сервера DNS](on-premises-dns-setup.md)
