---
title: CI/CD для Azure Веснного облака
description: CI/CD для Azure Веснного облака
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 3e392cee1269294e3a01cfc1b24e3fee9ddbc0d9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87037497"
---
# <a name="cicd-for-azure-spring-cloud"></a>CI/CD для Azure Веснного облака

Средства непрерывной интеграции и непрерывной поставки позволяют разработчикам быстро развертывать обновления для существующих приложений с минимальными усилиями и рисками. Azure DevOps помогает организовывать и контролировать эти ключевые задания. Сейчас Azure Веснного облака не предлагает конкретный подключаемый модуль Azure DevOps.  Однако вы можете интегрировать приложения с пружинным облаком с DevOps, используя [задачу Azure CLI](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-cli?view=azure-devops). В этой статье показано, как использовать задачу Azure CLI с Azure "Весна Cloud" для интеграции с Azure DevOps.

## <a name="create-an-azure-resource-manager-service-connection"></a>Создание подключения к службе Azure Resource Manager

Ознакомьтесь с [этой статьей](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) , чтобы узнать, как создать подключение службы Azure Resource Manager к проекту Azure DevOps. Не забудьте выбрать ту же подписку, которую вы используете для своего экземпляра облачной службы Azure весны.

## <a name="azure-cli-task-templates"></a>Azure CLI шаблонов задач

### <a name="deploy-artifacts"></a>Развертывание артефактов

Вы можете создавать и развертывать проекты с помощью серии `tasks` . Сначала этот фрагмент кода определяет задачу Maven для создания приложения, а затем — вторую задачу, которая развертывает JAR-файл с помощью расширения Azure "Весна Cloud" Azure CLI.

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

Развертывание можно выполнить непосредственно в Azure без отдельного этапа сборки.

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
