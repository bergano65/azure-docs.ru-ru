---
title: Регистрация поставщика ресурсов для Решения Azure VMware
description: Инструкции по регистрации поставщика ресурсов для Решения Azure VMware.
ms.topic: include
ms.date: 09/21/2020
ms.openlocfilehash: 96d15546c5102a69a0b19f92de33d35d2e9ab6c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91575747"
---
<!-- Used in avs-deployment.md and tutorial-create-private-cloud.md -->

Чтобы использовать Решение Azure VMware, необходимо сначала зарегистрировать поставщик ресурсов в подписке.

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

>[!TIP]
>Вы также можете использовать графический пользовательский интерфейс для регистрации поставщика ресурсов **Microsoft.AVS**.  Дополнительные сведения см. в разделе [Регистрация поставщика ресурсов](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider).  
