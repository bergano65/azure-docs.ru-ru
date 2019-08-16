---
title: включение файла
description: включение файла
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ddab6927044ce638e50ff3ad79aa3c35d046c820
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/15/2019
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
