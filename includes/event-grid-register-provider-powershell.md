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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66156032"
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
