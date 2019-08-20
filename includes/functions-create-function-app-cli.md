---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/05/2019
ms.author: glenga
ms.openlocfilehash: c90587ebd7b0a608e41f19ab600f82dc484bad0a
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/12/2019
ms.locfileid: "68949939"
---
## <a name="create-the-local-function-app-project"></a>Создание локального проекта приложения-функции

Выполните следующую команду из командной строки, чтобы создать проект приложения-функции в папке `MyFunctionProj` текущего локального каталога. Также в `MyFunctionProj` создается репозиторий GitHub.

```command
func init MyFunctionProj
```

Когда появится запрос, выберите среду выполнения рабочей роли из следующих языков:

+ `dotnet`. Создает проект библиотеки классов .NET (CSPROJ-файл).
+ `node`. Создает проект на основе Node.js. Выберите `javascript` или `typescript`. 
+ `python`. Для проекта Python выполните инструкции из статьи [Создание функции, активируемой HTTP, в Azure](../articles/azure-functions/functions-create-first-function-python.md).
+ `powershell`. Для проекта PowerShell выполните инструкции из статьи [Создание первой функции PowerShell в Azure (предварительная версия)](../articles/azure-functions/functions-create-first-function-powershell.md). 


После создания проекта перейдите к его новой папке `MyFunctionProj`, используя следующую команду.

```command
cd MyFunctionProj
```
