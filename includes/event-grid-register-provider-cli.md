---
title: включить файл
description: включить файл
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 08/17/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: b48e5e28b15d17796138d493a7d9c628791df2e6
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87830140"
---
## <a name="enable-the-event-grid-resource-provider"></a>Включение поставщика ресурсов службы "Сетка событий"

Если вы еще не использовали службу "Сетка событий" в подписке Azure, вам, возможно, потребуется зарегистрировать поставщик ресурсов этой службы. Выполните следующую команду для регистрации поставщика:

```azurecli-interactive
az provider register --namespace Microsoft.EventGrid
```

Регистрация может занять некоторое время. Чтобы проверить состояние, выполните следующую команду:

```azurecli-interactive
az provider show --namespace Microsoft.EventGrid --query "registrationState"
```

Когда состояние `registrationState` изменится на `Registered`, вы сможете продолжить работу.