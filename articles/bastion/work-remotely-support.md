---
title: 'Работа удаленно с помощью Бастиона: Лазурный бастион'
description: На этой странице описывается, как можно использовать Azure Bastion для удаленной работы из-за пандемии COVID-19.
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mialdrid
ms.openlocfilehash: 182195190fed70b46185f98f595de6b6c32bbffe
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619396"
---
# <a name="working-remotely-using-azure-bastion"></a>Работа удаленно с помощью Azure Bastion

Azure Bastion играет ключевую роль в поддержке удаленных сценариев работы, позволяя пользователям с подключением к Интернету получить доступ к виртуальным машинам Azure. В частности, это позволяет ИТ-администраторам управлять своими приложениями, работающими в Azure, в любое время и из любой точки мира.

>[!NOTE]
>В этой статье описывается, как можно использовать сеть Azure Bastion, Azure, сеть Майкрософт и партнерскую экосистему Azure для удаленной работы и устранения проблем сети, с которыми вы столкнулись из-за кризиса COVID-19.
>

## <a name="securely-access-virtual-machines"></a>Безопасный доступ к виртуальным машинам

В частности, Azure Bastion обеспечивает безопасное и бесшовное подключение RDP/SSH к виртуальным машинам в виртуальной сети Azure, непосредственно на портале Azure, без использования общедоступного IP-адреса. Для получения дополнительной информации об архитектуре Azure Bastion и ключевых функциях, ознакомьтесь с тем, [что такое Azure Bastion.](bastion-overview.md)

Azure Bastion развертывается в виртуальной сети, что означает, что компании могут настроить и управлять одним Azure Bastion для быстрого поддержки удаленного доступа пользователей к виртуальным машинам в виртуальной сети Azure. Для получения рекомендаций о том, как создавать и управлять Azure Bastion, обратитесь к [созданию узла бастиона.](bastion-create-host-portal.md)

## <a name="next-steps"></a>Следующие шаги

* Наверстудивать Azure Bastion с помощью [портала Azure,](bastion-create-host-portal.md) [PowerShell](bastion-create-host-powershell.md)или Azure CLI.

* Ознакомьте [часто задаваемые вопросы «Бастион»](bastion-faq.md) для получения дополнительной информации.
