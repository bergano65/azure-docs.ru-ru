---
title: включить файл
description: включить файл
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 07/05/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 68a208af1a9aa9e73f2af99021d195f264fb21f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "67185903"
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
