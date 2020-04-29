---
title: Использование графического пакета SDK модуля Runbook службы автоматизации Azure
description: В этой статье описывается, как использовать графический пакет SDK Runbook для службы автоматизации Azure.
services: automation
ms.subservice: process-automation
ms.date: 07/20/2018
ms.topic: conceptual
ms.openlocfilehash: 0058c0a0cedf2ea3f6c32f8f8368cca5b8dc6e3c
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509012"
---
# <a name="use-the-azure-automation-graphical-runbook-sdk"></a>Использование графического пакета SDK модуля Runbook службы автоматизации Azure

[Графические модули Runbook](automation-graphical-authoring-intro.md) помогают управлять сложностью базового кода Windows PowerShell или рабочего процесса PowerShell. Пакет SDK для графической разработки Microsoft Azure службы автоматизации позволяет разработчикам создавать и редактировать графические модули Runbook для использования со службой автоматизации Azure. В этой статье описаны основные шаги по созданию графического модуля Runbook из кода.

## <a name="prerequisites"></a>Предварительные условия

Импортируйте `Microsoft.Azure.Management.Automation.GraphicalRunbook.Model` пакет в проект.

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

Действия реализуются следующими классами в `Orchestrator.GraphRunbook.Model` пространстве имен.

|Class  |Действие  |
|---------|---------|
|CommandActivity     | Вызывает команду PowerShell (командлет, функция и т. д.).        |
|InvokeRunbookActivity     | Вызывает другой runbook изнутри.        |
|JunctionActivity     | Ожидает завершения всех входящих ветвей.        |
|WorkflowScriptActivity     | Выполняет блок кода PowerShell или рабочего процесса PowerShell (в зависимости от типа runbook) в контексте runbook. Это эффективное средство, но не злоупотребляйте им. В пользовательском интерфейсе этот блок сценария будет отображаться как текст. Подсистема выполнения будет обрабатывать этот предоставленный блок по принципу "черного ящика" и не предпримет попыток проанализировать его содержимое за исключением базовой проверки синтаксиса. Если необходимо просто вызвать одну команду PowerShell, воспользуйтесь CommandActivity.        |

> [!NOTE]
> Не наследовать собственные действия от указанных классов. Служба автоматизации Azure не может использовать модули Runbook с настраиваемыми типами действий.

Необходимо предоставить `CommandActivity` параметры и `InvokeRunbookActivity` в виде дескрипторов значений, а не прямых значений. Дескрипторы значений определяют, как создавать фактические значения параметров. Сейчас указаны следующие дескрипторы значений:


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
> Не создавайте собственные дескрипторы значений из предоставленных классов. Служба автоматизации Azure не может использовать модули Runbook с пользовательскими типами дескрипторов значений.

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

Эту строку можно сохранить в файл с расширением **graphrunbook** . Соответствующий модуль Runbook можно импортировать в службу автоматизации Azure.
Сериализованный формат может измениться в будущих версиях `Orchestrator.GraphRunbook.Model.dll`. Мы гарантируем обратную совместимость: любой runbook, сериализованный с использованием более старой версии `Orchestrator.GraphRunbook.Model.dll`, может быть десериализован с использованием любой более поздней версии. Прямая совместимость не гарантируется: runbook, сериализованный с использованием более поздней версии, не может быть десериализован с использованием старых версий.

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о графических модулях Runbook в службе автоматизации Azure см. в статье [Введение в графические средства разработки](automation-graphical-authoring-intro.md).