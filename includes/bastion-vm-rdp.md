---
title: включить файл
description: включить файл
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 10/12/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b079ede0845de3794507691bc3c252930e2a6604
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/13/2020
ms.locfileid: "91977209"
---
1. Откройте [портал Azure](https://portal.azure.com). Перейдите к виртуальной машине, к которой необходимо подключиться, и выберите **Подключить**. В раскрывающемся списке выберите **Бастион**.

   :::image type="content" source="./media/bastion-vm-rdp/connect-vm.png" alt-text="Выбор элемента &quot;Бастион&quot;" border="false":::

1. Когда вы выберете этот элемент в раскрывающемся списке, появится боковая панель с тремя вкладками: RDP, SSH и "Бастион". Так как бастион был подготовлен для виртуальной сети, вкладка "Бастион" активна по умолчанию. Выберите **Использовать бастион**.

   :::image type="content" source="./media/bastion-vm-rdp/use-bastion.png" alt-text="Выбор элемента &quot;Бастион&quot;" border="false":::

1. На странице **Подключиться с помощью Бастиона Azure** введите имя пользователя и пароль для виртуальной машины, а затем выберите **Подключить**.

   :::image type="content" source="./media/bastion-vm-rdp/host.png" alt-text="Выбор элемента &quot;Бастион&quot;" border="false":::

1. Подключение по RDP к этой виртуальной машине через Бастион будет открываться непосредственно на портале Azure (через HTML5) с использованием порта 443 и службы "Бастион".

   :::image type="content" source="./media/bastion-vm-rdp/connection.png" alt-text="Выбор элемента &quot;Бастион&quot;" border="false":::
