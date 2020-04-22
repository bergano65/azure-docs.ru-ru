---
title: Используйте графический справочник Azure Automation SDK
description: В этой статье описывается, как использовать графический справочник Azure Automation SDK.
services: automation
ms.subservice: process-automation
ms.date: 07/20/2018
ms.topic: conceptual
ms.openlocfilehash: 21f6ff8078d5a1db88b2fde33c9063a56b3ee43a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682904"
---
# <a name="use-the-azure-automation-graphical-runbook-sdk"></a>Используйте графический справочник Azure Automation SDK

[Графические руны](automation-graphical-authoring-intro.md) помогают управлять сложностями базового кода рабочего процесса Windows PowerShell или PowerShell. Графическое написание SDK Microsoft Azure Automation позволяет разработчикам создавать и отсылать графические альбомы для использования в Azure Automation. В этой статье описаны основные шаги в создании графического runbook из вашего кода.

## <a name="prerequisites"></a>Предварительные требования

Импортируйте `Microsoft.Azure.Automation.GraphicalRunbook.Model` пакет в свой проект.

## <a name="create-a-runbook-object-instance"></a>Создание экземпляра объекта runbook

Укажите ссылку на сборку `Orchestrator.GraphRunbook.Model` и создайте экземпляр класса `Orchestrator.GraphRunbook.Model.GraphRunbook`:

```csharp
using Orchestrator.GraphRunbook.Model;
using Orchestrator.GraphRunbook.Model.ExecutableView;

var runbook = new GraphRunbook();
```

## <a name="add-runbook-parameters"></a>Добавление параметров runbook

Создайте экземпляры объектов `Orchestrator.GraphRunbook.Model.Parameter` и добавьте их в runbook:

```csharp
runbook.AddParameter(
 new Parameter("YourName") {
  TypeName = typeof(string).FullName,
   DefaultValue = "World",
   Optional = true
 });

runbook.AddParameter(
 new Parameter("AnotherParameter") {
  TypeName = typeof(int).FullName, ...
 });
```

## <a name="add-activities-and-links"></a>Добавление действий и ссылок

Создайте экземпляры действий соответствующих типов и добавьте их в runbook:

```csharp
var writeOutputActivityType = new CommandActivityType {
 CommandName = "Write-Output",
  ModuleName = "Microsoft.PowerShell.Utility",
 InputParameterSets = new [] {
  new ParameterSet {
   Name = "Default",
    new [] {
     new Parameter("InputObject"), ...
    }
  },
  ...
 }
};

var outputName = runbook.AddActivity(
 new CommandActivity("Output name", writeOutputActivityType) {
  ParameterSetName = "Default",
   Parameters = new ActivityParameters {
    {
     "InputObject",
     new RunbookParameterValueDescriptor("YourName")
    }
   },
   PositionX = 0,
   PositionY = 0
 });

var initializeRunbookVariable = runbook.AddActivity(
 new WorkflowScriptActivity("Initialize variable") {
  Process = "$a = 0",
   PositionX = 0,
   PositionY = 100
 });
```

Действия выполняются следующими классами в пространстве `Orchestrator.GraphRunbook.Model` имен.

|Class  |Действие  |
|---------|---------|
|CommandActivity     | Вызывает команду PowerShell (командлет, функция и т. д.).        |
|InvokeRunbookActivity     | Вызывает другой runbook изнутри.        |
|JunctionActivity     | Ожидает завершения всех входящих ветвей.        |
|WorkflowScriptActivity     | Выполняет блок кода PowerShell или рабочего процесса PowerShell (в зависимости от типа runbook) в контексте runbook. Это эффективное средство, но не злоупотребляйте им. В пользовательском интерфейсе этот блок сценария будет отображаться как текст. Подсистема выполнения будет обрабатывать этот предоставленный блок по принципу "черного ящика" и не предпримет попыток проанализировать его содержимое за исключением базовой проверки синтаксиса. Если необходимо просто вызвать одну команду PowerShell, воспользуйтесь CommandActivity.        |

> [!NOTE]
> Не извлекайте свои собственные действия из предоставленных классов. Azure Automation не может использовать runbooks с пользовательскими типами действий.

Вы должны `CommandActivity` `InvokeRunbookActivity` предоставить и параметры в качестве дескрипторов значения, а не прямые значения. Дескрипторы значений определяют, как производить фактические значения параметров. Сейчас указаны следующие дескрипторы значений:


|Дескриптор  |Определение  |
|---------|---------|
|ConstantValueDescriptor     | Ссылается на жестко заданное постоянное значение.        |
|RunbookParameterValueDescriptor     | Ссылается на параметр runbook по имени.        |
|ActivityOutputValueDescriptor     | Ссылается на вышестоящие выходные данные действия, позволяя одному действию "подписаться" на данные, создаваемые другим действием.        |
|AutomationVariableValueDescriptor     | Ссылается на ресурс переменной службы автоматизации по имени.         |
|AutomationCredentialValueDescriptor     | Ссылается на ресурс сертификата службы автоматизации по имени.        |
|AutomationConnectionValueDescriptor     | Ссылается на ресурс подключения службы автоматизации по имени.        |
|PowerShellExpressionValueDescriptor     | Указывает выражение PowerShell свободной формы, которое будет вычисляться непосредственно перед вызовом действия.  <br/>Это эффективное средство, но не злоупотребляйте им. В пользовательском интерфейсе это выражение будет отображаться как текст. Подсистема выполнения будет обрабатывать этот предоставленный блок по принципу "черного ящика" и не предпримет попыток проанализировать его содержимое за исключением базовой проверки синтаксиса. По возможности отдавайте предпочтение более конкретным дескрипторам значений.      |

> [!NOTE]
> Не извлекайте свои собственные дескрипторы значений из предоставленных классов. Azure Automation не может использовать runbooks с пользовательскими типами дескрипторов значений.

Создайте экземпляры связей, соединяющих действия, и добавьте их в runbook:

```csharp
runbook.AddLink(new Link(activityA, activityB, LinkType.Sequence));

runbook.AddLink(
 new Link(activityB, activityC, LinkType.Pipeline) {
  Condition = string.Format("$ActivityOutput['{0}'] -gt 0", activityB.Name)
 });
```

## <a name="save-the-runbook-to-a-file"></a>Сохранение runbook в файл

Сериализуйте runbook в строку с помощью `Orchestrator.GraphRunbook.Model.Serialization.RunbookSerializer`:

```csharp
var serialized = RunbookSerializer.Serialize(runbook);
```

Эту строку можно сохранить в файле с расширением **.graphrunbook.** Соответствующий runbook можно импортировать в Azure Automation.
Серийный формат может измениться в `Orchestrator.GraphRunbook.Model.dll`будущих версиях . Мы гарантируем обратную совместимость: любой runbook, сериализованный с использованием более старой версии `Orchestrator.GraphRunbook.Model.dll`, может быть десериализован с использованием любой более поздней версии. Прямая совместимость не гарантируется: runbook, сериализованный с использованием более поздней версии, не может быть десериализован с использованием старых версий.

## <a name="next-steps"></a>Следующие шаги

Чтобы узнать больше о графических runbooks в Azure [Автоматизация,](automation-graphical-authoring-intro.md)см Графический автор введение .