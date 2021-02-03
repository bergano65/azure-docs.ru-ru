---
author: MikeRayMSFT
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.topic: include
ms.date: 01/15/2021
ms.author: mikeray
ms.openlocfilehash: 6c8dbeea83cba306cfb788cf447236088045ffc9
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99494024"
---
В этом разделе объясняется, как применить ограничение контекста безопасности (SCC). В предварительной версии это ослабляет ограничения безопасности. 

1. Скачайте настраиваемое ограничение контекста безопасности (SCC). Используйте одно из следующих значений: 
   - [GitHub](https://github.com/microsoft/azure_arc/tree/main/arc_data_services/deploy/yaml/arc-data-scc.yaml) 
   - [Raw](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml)
   - `curl`
   
      Следующая команда скачивает Arc-Data-SCC. YAML:

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

   > [!NOTE]
   > RedHat OpenShift 4,5 или более поздней версии изменяет способ применения SCC к учетной записи службы.
   > Используйте то же пространство имен здесь и в `azdata arc dc create` приведенной ниже команде. Пример: `arc` . 
   > 
   > Если вы используете RedHat OpenShift 4,5 или более поздней версии, выполните: 
   >
   >```console
   >oc create rolebinding arc-data-rbac --clusterrole=system:openshift:scc:arc-data-scc --serviceaccount=arc:default
   >```
