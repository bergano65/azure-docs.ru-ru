---
title: Список разрешенных портов для Avere vFXT для Azure
description: Список портов, используемых Avere vFXT для Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 394415ffc7593d058d8d0bf1c0040b88ec25073b
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50669931"
---
# <a name="required-ports"></a>Требуемые порты

Порты, перечисленные в этом разделе, используются для входящих и исходящих подключений vFXT.

Никогда не предоставляйте доступ к кластеру vFXT или экземпляру контроллера кластера напрямую через общедоступный Интернет.

## <a name="api"></a>API

| Входящий трафик: | | |
| --- | ---- | --- |
| TCP | 22  | SSH  |
| TCP | 443 | HTTPS|



| Исходящий трафик: |     |       |
|----------|-----|-------|
| TCP      | 443 | HTTPS |

 
## <a name="nfs"></a>NFS

| Входящий и исходящий  | | |
| --- | --- | ---|
| TCP/UDP | 111  | RPCBIND  |
| TCP/UDP | 2049 | NFS      |
| TCP/UDP | 4045 | NLOCKMGR |
| TCP/UDP | 4046 | MOUNTD   |
| TCP/UDP | 4047 | Состояние   |

