---
title: Предоставление конфигураций после развертывания с помощью расширений — Azure | Документация Майкрософт
description: Узнайте, как использовать расширения шаблона Azure Resource Manager для предоставления конфигураций после развертывания.
services: azure-resource-manager
documentationcenter: na
author: mumian
editor: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/14/2018
ms.author: jgao
ms.openlocfilehash: eb46966c3a28b3fa4c2b23668109b7c5d23a609b
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53414344"
---
# <a name="provide-post-deployment-configurations-by-using-extensions"></a>Предоставление конфигураций после развертывания с помощью расширений

Расширения шаблона — это небольшие приложения, которые выполняют задачи конфигурации и автоматизации после развертывания ресурсов Azure. Самые популярные — расширения виртуальных машин. Ознакомьтесь со статьей [Обзор расширений и компонентов виртуальной машины под управлением Windows](../virtual-machines/extensions/features-windows.md) или [Обзор расширений и компонентов виртуальных машин под управлением Linux](../virtual-machines/extensions/features-linux.md).

## <a name="extensions"></a>расширения.

Ниже приведены имеющиеся расширения:

- [Microsoft.Compute/virtualMachines/extensions](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachines/extensions)
- [Microsoft.Compute virtualMachineScaleSets/extensions](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions)
- [Microsoft.HDInsight clusters/extensions](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/2018-06-01-preview/clusters/extensions)
- [Microsoft.Sql servers/databases/extensions](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions) 
- [Microsoft.Web/sites/siteextensions](https://docs.microsoft.com/azure/templates/microsoft.web/2016-08-01/sites/siteextensions)

Чтобы узнать доступные расширения, перейдите по [ссылке на шаблон](https://docs.microsoft.com/azure/templates/). В **фильтре по названию** введите **расширение**.

Подробнее об использовании этих расширений см. в следующих статьях:

- [Руководство. Развертывание расширений виртуальной машины с помощью шаблонов Azure Resource Manager](./resource-manager-tutorial-deploy-vm-extensions.md).
- [Руководство. Импорт BACPAC-файлов SQL с помощью шаблонов Azure Resource Manager](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md)

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Руководство. Развертывание расширений виртуальной машины с помощью шаблонов Azure Resource Manager](./resource-manager-tutorial-deploy-vm-extensions.md).
