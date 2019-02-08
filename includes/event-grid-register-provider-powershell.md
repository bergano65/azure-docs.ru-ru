---
title: включение файла
description: включение файла
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 07/05/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 68a208af1a9aa9e73f2af99021d195f264fb21f1
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/02/2019
ms.locfileid: "55664443"
---
## <a name="enable-event-grid-resource-provider"></a>Включение поставщика ресурсов службы "Сетка событий"

Если вы еще не использовали службу "Сетка событий" в подписке Azure, вам, возможно, потребуется зарегистрировать поставщик ресурсов этой службы. Выполните следующую команду:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.EventGrid
```

Регистрация может занять некоторое время. Чтобы проверить состояние, выполните следующую команду:

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.EventGrid
```

Когда состояние `RegistrationStatus` изменится на `Registered`, вы сможете продолжить работу.
