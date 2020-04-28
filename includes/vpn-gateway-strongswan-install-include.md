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
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/27/2020
ms.locfileid: "69520876"
---
Для приведенных ниже действий использовалась следующая конфигурация.

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

Чтобы установить интерфейс командной строки Azure, используйте следующую команду:

```
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

[Дополнительные инструкции по установке Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-apt?view=azure-cli-latest)
