---
title: Импорт и экспорт определений схем с помощью PowerShell
description: Узнайте, как работать с определениями схем в виде кода. Совместное использование, управление исходным кодом и управление ими с помощью команд экспорта и импорта.
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/03/2019
ms.topic: conceptual
ms.service: blueprints
ms.openlocfilehash: 30e734c99a87364acfba9a58d83fe9a377958607
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/06/2019
ms.locfileid: "71978440"
---
# <a name="import-and-export-blueprint-definitions-with-powershell"></a>Импорт и экспорт определений схем с помощью PowerShell

Схемы Azure можно полностью управлять с помощью портал Azure. По мере того, как организации начинают использовать схемы, они должны начать думать об определениях схем как управляемого кода. Эта концепция часто называется инфраструктурой как код (IaC). Рассматривая определения схемы как код, вы найдете дополнительные преимущества, помимо того, какие портал Azure предложения. К этим преимуществам относятся:

- Совместное использование определений схем
- Резервное копирование определений схем
- Повторное использование определений схем в разных клиентах или подписках
- Размещение определений схем в системе управления версиями
  - Автоматическое тестирование определений схем в тестовых средах
  - Поддержка конвейеров непрерывной интеграции и непрерывного развертывания (CI/CD)

По каким бы то ни было вашим причинам, Управление определениями схем в качестве кода имеет свои преимущества. В этой статье показано, как использовать команды `Import-AzBlueprintWithArtifact` и `Export-AzBlueprintWithArtifact` в модуле [AZ. чертеж](https://powershellgallery.com/packages/Az.Blueprint/) .

## <a name="prerequisites"></a>Предварительные требования

В этой статье предполагается умеренный опыт работы с чертежами Azure. Если вы еще не сделали этого, выполните действия, описанные в следующих статьях:

- [Создание схемы на портале](../create-blueprint-portal.md)
- Ознакомьтесь с [этапами развертывания](../concepts/deployment-stages.md) и [жизненным циклом](../concepts/lifecycle.md) проекта
- [Создание](../create-blueprint-powershell.md) определений [и назначений схем и](./manage-assignments-ps.md) их назначение с помощью PowerShell

Выполните инструкции из [этой статьи](./manage-assignments-ps.md#add-the-azblueprint-module), чтобы установить модуль **Az.Blueprint** из коллекции PowerShell и проверить его работу.

## <a name="folder-structure-of-a-blueprint-definition"></a>Структура папок определения схемы

Перед просмотром экспорта и импорта схем рассмотрим, как структурированы файлы, составляющие определение схемы. Определение схемы должно храниться в отдельной папке.

> [!IMPORTANT]
> Если значение параметра **Name** командлета `Import-AzBlueprintWithArtifact` не передается, используется имя папки, в которой хранится определение схемы.

Вместе с определением схемы, которое должно иметь имя `blueprint.json`, — это артефакты, из которых состоит определение схемы. Каждый артефакт должен находиться в подпапке с именем `artifacts`.
Вместе, структура определения схемы как JSON Files в папках должна выглядеть следующим образом:

```text
.
|
|- MyBlueprint/  _______________ # Root folder name becomes default name of blueprint definition
|  |- blueprint.json  __________ # The blueprint definition. Fixed name.
|
|  |- artifacts/  ______________ # Subfolder for all blueprint artifacts. Fixed name.
|     |- artifact.json  ________ # Blueprint artifact as JSON file. Artifact named from file.
|     |- ...
|     |- more-artifacts.json

```

## <a name="export-your-blueprint-definition"></a>Экспорт определения схемы

Действия по экспорту определения схемы просты. Экспорт определения схемы может быть полезен для совместного использования, резервного копирования или размещения в системе управления версиями.

- **Чертеж** [обязательный]
  - Указывает определение схемы
  - Чтобы получить ссылочный объект, используйте `Get-AzBlueprint`.
- **OutputPath** [обязательный]
  - Указывает путь для сохранения JSON определения схемы в
  - Выходные файлы находятся во вложенной папке с именем определения схемы.
- **Версия** (необязательно)
  - Указывает версию для **вывода, если эталонный** объект схемы содержит ссылки на более чем одну версию.

1. Получите ссылку на определение схемы для экспорта из подписки, представленной в виде `{subId}`:

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Get version '1.1' of the blueprint definition in the specified subscription
   $bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'MyBlueprint' -Version '1.1'
   ```

1. Используйте командлет `Export-AzBlueprintWithArtifact`, чтобы экспортировать указанное определение схемы:

   ```azurepowershell-interactive
   Export-AzBlueprintWithArtifact -Blueprint $bpDefinition -OutputPath 'C:\Blueprints'
   ```

## <a name="import-your-blueprint-definition"></a>Импорт определения схемы

После того как [экспортировано определение](#export-your-blueprint-definition) схемы или создано вручную определение схемы в [необходимой структуре папок](#folder-structure-of-a-blueprint-definition), можно импортировать определение схемы в другую группу управления или подписку.

Примеры встроенных определений схем см. в [репозитории Azure Blueprint GitHub](https://github.com/Azure/azure-blueprints/tree/master/samples/builtins).

- **Имя** [обязательный]
  - Указывает имя для нового определения схемы
- **InputPath** [обязательный]
  - Указывает путь для создания определения схемы из
  - Должно соответствовать [требуемой структуре папок](#folder-structure-of-a-blueprint-definition)
- **ManagementGroupId** (необязательно)
  - Идентификатор группы управления, в которую необходимо сохранить определение схемы, если не является текущим контекстом по умолчанию
  - Необходимо указать либо **ManagementGroupId** , либо **SubscriptionId** .
- **SubscriptionId** (необязательно)
  - Идентификатор подписки, в которую необходимо сохранить определение схемы, если не является текущим контекстом по умолчанию
  - Необходимо указать либо **ManagementGroupId** , либо **SubscriptionId** .

1. Используйте командлет `Import-AzBlueprintWithArtifact`, чтобы импортировать указанное определение схемы:

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   Import-AzBlueprintWithArtifact -Name 'MyBlueprint' -ManagementGroupId 'DevMG' -InputPath 'C:\Blueprints\MyBlueprint'
   ```

После импорта определения схемы [назначьте его с помощью PowerShell](./manage-assignments-ps.md#create-blueprint-assignments).

Дополнительные сведения о создании расширенных определений схем см. в следующих статьях:

- Используйте [статические и динамические параметры](../concepts/parameters.md).
- Настройка [порядка следования схем в последовательности](../concepts/sequencing-order.md).
- Защита развертываний с помощью [блокировки ресурсов](../concepts/resource-locking.md)схемы.
- [Управляйте чертежами в виде кода](https://github.com/Azure/azure-blueprints/blob/master/README.md).

## <a name="next-steps"></a>Следующие шаги

- Ознакомьтесь со сведениями о [жизненном цикле схем](../concepts/lifecycle.md).
- Узнайте, как использовать [статические и динамические параметры](../concepts/parameters.md).
- Научитесь настраивать [последовательность схемы](../concepts/sequencing-order.md).
- Узнайте, как применять [блокировку ресурсов схемы](../concepts/resource-locking.md).
- Устраняйте проблемы, возникающие во время назначения схемы, с помощью [общих инструкций по устранению неполадок](../troubleshoot/general.md).