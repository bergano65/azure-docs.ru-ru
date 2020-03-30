---
title: Удаленная служба работы Azure Firewall
description: В этой статье показано, как Azure Firewall может поддерживать требования удаленной рабочей силы.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: victorh
ms.openlocfilehash: 5abe9344b0512433c48df50335cce5cf1e3e3547
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289645"
---
# <a name="azure-firewall-remote-work-support"></a>Удаленная служба работы Azure Firewall

Azure Firewall — это управляемая облачная служба безопасности сети, которая защищает ваши виртуальные сетевые ресурсы Azure. Это высокодоступная служба с полным отслеживанием состояния и неограниченными возможностями облачного масштабирования. 

## <a name="firewall-rules"></a>Правила брандмауэра

Вы можете использовать Azure Firewall для обеспечения входящего RDP-доступа к входящим RDP-инфраструктуре виртуальной компании Azure с использованием правил DNAure Firewall [DNAT.](rule-processing.md) Windows Virtual Desktop (WVD) не требует от вас открывать доступ к виртуальной сети. Тем не менее, необходимо разрешить набор исходящих сетевых подключений для виртуальных машин WVD, которые работают в вашей виртуальной сети. Для получения дополнительной информации, смотрите [Что такое Windows Виртуальный рабочий стол?](../virtual-desktop/overview.md#requirements)

Вы можете настроить этот исходящий доступ с помощью правил приложения Azure Firewall. Для получения дополнительной информации [см.](tutorial-firewall-deploy-portal.md)

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о [Windows Virtual Desktop](https://docs.microsoft.com/azure/virtual-desktop/).