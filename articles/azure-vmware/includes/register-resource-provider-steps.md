---
title: Регистрация поставщика ресурсов для Решения Azure VMware
description: Инструкции по регистрации поставщика ресурсов для Решения Azure VMware.
ms.topic: include
ms.date: 09/04/2020
ms.openlocfilehash: 08741181b778b44289868a9f3fb1c2368955248c
ms.sourcegitcommit: 8791f69d44150767807d215cafc4076f3ed43f9f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/08/2020
ms.locfileid: "89512384"
---
Чтобы использовать Решение Azure VMware, необходимо сначала зарегистрировать поставщик ресурсов в подписке.

```
azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

В [списке поставщиков и типов ресурсов](../../azure-resource-manager/management/resource-providers-and-types.md) описано, как еще можно зарегистрировать поставщик ресурсов.