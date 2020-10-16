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
ms.openlocfilehash: 2d0e171807985deaebe8faa625d6b767c2a3efd7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "86218366"
---
Для приведенных ниже действий использовалась следующая конфигурация.

- Компьютер: Ubuntu Server 18,04
- Зависимости: strongSwan


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
