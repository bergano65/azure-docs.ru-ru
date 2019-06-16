---
title: Выполнение рабочих нагрузок на виртуальных машинах в Azure | Документация Майкрософт
description: Узнайте, как для запуска рабочих нагрузок на виртуальных машинах Azure.
services: virtual-machines
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 06/06/2019
ms.author: cynthn
ms.openlocfilehash: 167370a4eebded3dba898ed47dede7a36900d976
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66808216"
---
# <a name="workloads-on-azure-virtual-machines"></a>Рабочие нагрузки на виртуальных машинах Azure

Виртуальные машины в Azure может поддерживать множество рабочих нагрузок. Каждая рабочая нагрузка может специализированные рекомендации по оптимальной модели для развертывания на виртуальных машинах в Azure. Вот некоторые из рекомендаций, которую мы можем предложить для специализированных рабочих нагрузок.

- [Высокопроизводительные вычисления](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) в Azure использует ЦП или GPU оптимизирован виртуальных машин для решения задач сложных, ресурсоемких вычислений. 
- [SAP](./sap/get-started.md) в Azure можно запускать критически важные рабочие нагрузки SAP и сценариев на платформе масштабируемой, совместимых и проверенные многими компаниями.
- [Oracle](./oracle/oracle-considerations.md) поддерживает выпуски Oracle DB 12.1 Standard и Enterprise в Azure в образах виртуальных машин на базе Oracle Linux. 
- [Повторное размещение больших ЭВМ](./mainframe-rehosting/overview.md) 



**Дальнейшие действия**

Узнайте о виртуальных машинах Azure с виртуальной Машины Azure краткие руководства по [Linux](../linux/quick-create-cli-nodejs.md) и [Windows](../windows/quick-create-powershell.md).