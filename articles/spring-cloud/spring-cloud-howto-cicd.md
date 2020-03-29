---
title: CI/CD для весеннего облака Azure
description: CI/CD для весеннего облака Azure
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: brendm
ms.openlocfilehash: f329fb5472c5a2eab6f22a2e81b19d90e7045330
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278516"
---
# <a name="cicd-for-azure-spring-cloud"></a>CI/CD для весеннего облака Azure

Непрерывная интеграция и инструменты непрерывной доставки позволяют разработчикам быстро развертывать обновления для существующих приложений с минимальными усилиями и рисками. Azure DevOps помогает вам организовывать и контролировать эти ключевые задания. В настоящее время Azure Spring Cloud не предлагает конкретного плагина Azure DevOps.  Тем не менее, вы можете интегрировать приложения Spring Cloud с DevOps с помощью [задачи Azure CLI.](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-cli?view=azure-devops) В этой статье будет показана возможность использования задачи Azure CLI с облачным облаком Azure Spring для интеграции с Azure DevOps.

## <a name="create-an-azure-resource-manager-service-connection"></a>Создание подключения к службе управления ресурсами Azure

Прочитайте [эту статью,](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) чтобы узнать, как создать подключение службы управления ресурсами Azure к проекту Azure DevOps. Обязательно выберите ту же подписку, что и для экземпляра облачных служб Azure Spring.

## <a name="azure-cli-task-templates"></a>Шаблоны задач Azure CLI

### <a name="deploy-artifacts"></a>Развертывание артефактов

Вы можете создавать и развертывать `tasks`свои проекты с помощью серии . Этот фрагмент сначала определяет задачу Maven для создания приложения, а затем вторую задачу, которая развертывает файл JAR с помощью расширения Azure Spring Cloudure CLI.

```yaml
steps:
- task: Maven@3
  inputs:
    mavenPomFile: 'pom.xml'
- task: AzureCLI@1
  inputs:
    azureSubscription: <your service connection name>
    scriptLocation: inlineScript
    inlineScript: |
      az extension add -y --name spring-cloud
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --jar-path ./target/your-result-jar.jar
      # deploy other app
```

### <a name="deploy-from-source"></a>Развертывание из источника

Развертывание можно непосредственно в Azure без отдельного шага сборки.

```yaml
- task: AzureCLI@1
  inputs:
    azureSubscription: <your service connection name>
    scriptLocation: inlineScript
    inlineScript: |
      az extension add -y --name spring-cloud
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name>

      # or if it is a multi-module project
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --target-module relative/path/to/module
```
