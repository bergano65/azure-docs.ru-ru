---
author: MikeRayMSFT
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.topic: include
ms.date: 01/15/2021
ms.author: mikeray
ms.openlocfilehash: 17a8c9580a8e69213c7f34e8ec889f7e46c6d17d
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/22/2021
ms.locfileid: "98696076"
---
В этом разделе объясняется, как применить ограничение контекста безопасности (SCC). В предварительной версии это ослабляет ограничения безопасности. 

1. Скачайте настраиваемое ограничение контекста безопасности (SCC). Используйте одно из следующих значений: 
   - [GitHub](https://github.com/microsoft/azure_arc/tree/main/arc_data_services/deploy/yaml/arc-data-scc.yaml) 
   - ([RAW](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml))
   - `curl` Следующая команда скачивает Arc-Data-SCC. YAML:

      ```console
      curl https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml -o arc-data-scc.yaml
      ```

1. Создайте SCC.

   ```console
   oc create -f arc-data-scc.yaml
   ```

1. Примените SCC к учетной записи службы.

   > [!NOTE]
   > Используйте то же пространство имен здесь и в `azdata arc dc create` приведенной ниже команде. Пример: `arc` .

   ```console
   oc adm policy add-scc-to-user arc-data-scc --serviceaccount default --namespace arc
   ```