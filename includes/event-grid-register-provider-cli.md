---
title: включение файла
description: включение файла
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 08/17/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: ace22a6896a121f5cd8af838c7b0e427bd0287dc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60427686"
---
## <a name="enable-event-grid-resource-provider"></a>Включение поставщика ресурсов службы "Сетка событий"

Если вы еще не использовали службу "Сетка событий" в подписке Azure, вам, возможно, потребуется зарегистрировать поставщик ресурсов этой службы. Выполните следующую команду для регистрации поставщика:

```azurecli-interactive
az provider register --namespace Microsoft.EventGrid
```

Регистрация может занять некоторое время. Чтобы проверить состояние, выполните следующую команду:

```azurecli-interactive
az provider show --namespace Microsoft.EventGrid --query "registrationState"
```

Когда состояние `registrationState` изменится на `Registered`, вы сможете продолжить работу.