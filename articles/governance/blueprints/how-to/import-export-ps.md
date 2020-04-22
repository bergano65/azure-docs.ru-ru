---
title: Импортные и экспортные планы с PowerShell
description: Узнайте, как работать с определениями чертежей в качестве кода. Поделиться, контролировать исходные ресурсы и управлять ими с помощью экспортных и импортных команд.
ms.date: 09/03/2019
ms.topic: how-to
ms.openlocfilehash: dcdf48f8941198591b39d6cf89ec5e6dac7ba94c
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686835"
---
# <a name="import-and-export-blueprint-definitions-with-powershell"></a>Определения схем импорта и экспорта с PowerShell

Полностью управлять планами Azure можно через портал Azure. По мере продвижения организаций в использовании чертежей Azure им следует начать думать об определениях чертежей как об управляемом коде. Эта концепция часто упоминается как Инфраструктура как Код (IaC). Рассматривая определения чертежей как код, дополнительные преимущества помимо того, что предлагает портал Azure. Эти преимущества включают в себя:

- Обмен определениями чертежей
- Резервное копирование определений чертежей
- Повторное использование определений чертежей в разных арендаторах или подписках
- Размещение определений чертежей в элементе управления исходом
  - Автоматизированное тестирование определений чертежей в тестовых средах
  - Поддержка непрерывной интеграции и непрерывного развертывания (CI/CD) конвейеров

Какими бы ни были ваши причины, управление определениями чертежа как кода имеет свои преимущества. В этой статье показано, как использовать `Import-AzBlueprintWithArtifact` и `Export-AzBlueprintWithArtifact` команд в модуле [Az.Blueprint.](https://powershellgallery.com/packages/Az.Blueprint/)

## <a name="prerequisites"></a>Предварительные требования

Эта статья предполагает умеренное знание рабочих чертежей Azure. Если вы еще не сделали этого, проработайте следующие статьи:

- [Создание схемы на портале](../create-blueprint-portal.md)
- Узнайте о [этапах развертывания](../concepts/deployment-stages.md) и [жизненном цикле чертежей](../concepts/lifecycle.md)
- [Создание](../create-blueprint-powershell.md) и [управление](./manage-assignments-ps.md) определениями и назначениями с помощью PowerShell

Выполните инструкции из [этой статьи](./manage-assignments-ps.md#add-the-azblueprint-module), чтобы установить модуль **Az.Blueprint** из коллекции PowerShell и проверить его работу.

## <a name="folder-structure-of-a-blueprint-definition"></a>Структура Фолдеропределения определения чертежа

Прежде чем рассматривать экспортные и импортируемые чертежи, давайте посмотрим, как структурированы файлы, входят в определение чертежа. Определение чертежа должно храниться в собственной папке.

> [!IMPORTANT]
> Если значение не передается параметру **имени** `Import-AzBlueprintWithArtifact` cmdlet, используется имя папки, в которых хранится определение чертежа.

Наряду с определением чертежа, которое должно быть названо, `blueprint.json`являются артефактами, из которых состоит определение чертежа. Каждый артефакт должен быть в `artifacts`подфолдере под названием.
В совокупности структура определения чертежа как файлов JSON в папках должна выглядеть следующим образом:

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

## <a name="export-your-blueprint-definition"></a>Экспортировать определение чертежа

Шаги к экспорту определения чертежа просты. Экспорт определения чертежа может быть полезен для совместного использования, резервного копирования или входного элемента управления исходом.

- **План (обязательно)**
  - Определяет определение чертежа
  - Использование `Get-AzBlueprint` для получения эталонного объекта
- **Выходная дорожка** (обязательно)
  - Определяет способ сохранения определения чертежа файлов JSON
  - Выводные файлы находятся в подфолиате с названием определения чертежа
- **Версия** (необязательно)
  - Осваивай версию к выводу, если эталонный объект **Blueprint** содержит ссылки на более чем одну версию.

1. Получить ссылку на определение чертежа для `{subId}`экспорта из подписки представлены как:

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Get version '1.1' of the blueprint definition in the specified subscription
   $bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'MyBlueprint' -Version '1.1'
   ```

1. Используйте `Export-AzBlueprintWithArtifact` cmdlet для экспорта указанного определения чертежа:

   ```azurepowershell-interactive
   Export-AzBlueprintWithArtifact -Blueprint $bpDefinition -OutputPath 'C:\Blueprints'
   ```

## <a name="import-your-blueprint-definition"></a>Импортировать определение чертежа

После того как у вас есть либо [экспортируемое определение](#export-your-blueprint-definition) чертежа, либо созданное вручную определение чертежа в [требуемой структуре папок,](#folder-structure-of-a-blueprint-definition)вы можете импортировать это определение чертежа в другую группу управления или подписку.

Примеры встроенных определений чертежей можно найти [в репо Azure Blueprint GitHub.](https://github.com/Azure/azure-blueprints/tree/master/samples/001-builtins)

- **Имя** (обязательно)
  - Упогоняет название нового определения чертежа
- **Вхотогёра** (обязательно)
  - Определяет путь создания определения чертежа из
  - Должен соответствовать [требуемой структуре папки](#folder-structure-of-a-blueprint-definition)
- **ManagementGroupId** (необязательно)
  - Идентификатор группы управления для сохранения определения чертежа, если не текущий контекст по умолчанию
  - Либо **ManagementGroupId** или **SubscriptionId** должны быть указаны
- **ПодпискаId** (необязательно)
  - Идентификатор подписки для сохранения определения чертежа, если не текущий контекст по умолчанию
  - Либо **ManagementGroupId** или **SubscriptionId** должны быть указаны

1. Используйте `Import-AzBlueprintWithArtifact` cmdlet для импорта указанного определения чертежа:

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   Import-AzBlueprintWithArtifact -Name 'MyBlueprint' -ManagementGroupId 'DevMG' -InputPath 'C:\Blueprints\MyBlueprint'
   ```

После импорта определения чертежа [присвоите его PowerShell.](./manage-assignments-ps.md#create-blueprint-assignments)

Для получения информации о создании расширенных определений чертежей см.

- Используйте [статические и динамические параметры.](../concepts/parameters.md)
- Настройка [порядка секвенирования](../concepts/sequencing-order.md)чертежа.
- Защитите развертывание с [блокировкой ресурсов чертежа.](../concepts/resource-locking.md)
- [Управление чертежами как код](https://github.com/Azure/azure-blueprints/blob/master/README.md).

## <a name="next-steps"></a>Дальнейшие действия

- Ознакомьтесь со сведениями о [жизненном цикле схем](../concepts/lifecycle.md).
- Узнайте, как использовать [статические и динамические параметры](../concepts/parameters.md).
- Научитесь настраивать [последовательность схемы](../concepts/sequencing-order.md).
- Узнайте, как применять [блокировку ресурсов схемы](../concepts/resource-locking.md).
- Устраняйте проблемы, возникающие во время назначения схемы, с помощью [общих инструкций по устранению неполадок](../troubleshoot/general.md).