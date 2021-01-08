---
title: Регистрация поставщика ресурсов для Решения Azure VMware
description: Инструкции по регистрации поставщика ресурсов для Решения Azure VMware.
ms.topic: include
ms.date: 12/24/2020
ms.openlocfilehash: 7d24ce86f24c941c7d48d3b73576dcdfda120f51
ms.sourcegitcommit: 489ce69c0ff3f5188889ecfef5ffa76f7121e0d3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/24/2020
ms.locfileid: "97770841"
---
<!-- Used in avs-deployment.md and tutorial-create-private-cloud.md -->

Чтобы использовать Решение Azure VMware, необходимо сначала зарегистрировать поставщик ресурсов в подписке.  

### <a name="azure-cli"></a>Azure CLI 

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```


### <a name="azure-portal"></a>Портал Azure
 
1. Войдите на [портал Azure](https://portal.azure.com).

1. В меню портала Azure выберите **Все службы**.

1. В поле **Все службы** введите **подписка**, а затем выберите **Подписки**.

1. Выберите подписку из списка подписок для просмотра.

1. Выберите **Поставщики ресурсов** и введите **Microsoft.AVS** в поле поиска. 
 
1. Если поставщик ресурсов не зарегистрирован, выберите **Зарегистрировать**.