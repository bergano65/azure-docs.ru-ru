---
title: Изменения в конфигурации доступ — Azure HDInsight кластера
description: Дополнительные сведения об изменениях для доступа к полям конфигурации конфиденциальных кластера.
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/12/2019
ms.openlocfilehash: 85a6737ee111975d4c7ecf078673280127bfd0fd
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59610247"
---
# <a name="changes-to-cluster-configuration-access"></a>Изменения, чтобы получить доступ к конфигурации кластера

Последний выпуск пакета SDK для Azure HDInsight предоставляет несколько важных изменений для поддержки более точный контроль ролевого доступа для получения конфиденциальной информации. По мере изменения часть из них, некоторые **действие может потребоваться** при использовании одного из соответствующих методов.

## <a name="sdk-for-net-500"></a>Пакет SDK для .NET 5.0.0

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet) будет **больше не возвращает конфиденциальные параметры** , такие как ключи к хранилищу (core-site) или учетных данных HTTP (шлюз).
    - Получить все конфигурации, включая конфиденциальные параметры с помощью `ConfigurationOperationsExtensions.List` Забегая вперед.  Обратите внимание на то, что пользователи с ролью «Читатель» не смогут использовать этот метод. Это обеспечивает детальный контроль, по которому пользователи можно получить доступ к конфиденциальной информации для кластера. 
    - Чтобы получить только учетные данные шлюза HTTP, используйте `ClusterOperationsExtensions.GetGatewaySettings`. 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet) Теперь считается устаревшим и будет заменен `ClusterOperationsExtensions.UpdateGatewaySettings`. 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet) и [ `DisableHttp` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) уже устарели. HTTP теперь всегда включена, поэтому эти методы больше не требуются.