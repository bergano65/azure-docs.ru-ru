---
title: включить файл
description: включить файл
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ddab6927044ce638e50ff3ad79aa3c35d046c820
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "69520876"
---
Следующая конфигурация была использована для приведенных ниже ступенек:

  | | |
  |---|---|
  |Компьютер| Ubuntu Server 18.04|
  |Зависимости| strongSwan |


Чтобы установить необходимую конфигурацию strongSwan, используйте следующие команды:

```
sudo apt install strongswan
```

```
sudo apt install strongswan-pki
```

```
sudo apt install libstrongswan-extra-plugins
```

Используйте следующую команду для установки интерфейса командной строки Azure:

```
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

[Дополнительные инструкции по установке Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-apt?view=azure-cli-latest)
