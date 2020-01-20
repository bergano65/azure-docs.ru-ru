---
title: включить файл
description: включить файл
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: ea7396117935c13698a8c6cc6ef4029cc82b90bc
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279480"
---
Код всех функций конкретного приложения-функции хранится в корневой папке проекта, содержащей файл конфигурации главного узла и одну или несколько вложенных папок. Каждая вложенная папка содержит код для отдельной функции. Структура папок показана в следующем представлении:

```
FunctionApp
 | - host.json
 | - MyFirstFunction
 | | - function.json
 | | - ...  
 | - MySecondFunction
 | | - function.json
 | | - ...  
 | - SharedCode
 | - bin
```

В версии 2. x и более поздних версиях среды выполнения функций все функции в приложении функции должны совместно использовать один и тот же стек языка.  

Файл [Host. JSON](../articles/azure-functions/functions-host-json.md) содержит конфигурации, зависящие от среды выполнения, и находится в корневой папке приложения-функции. Папка *bin* содержит пакеты и другие файлы библиотеки, необходимые приложению-функции. См. требования конкретного языка для проекта приложения-функции:

* [Библиотека классов C# (.csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [Скрипт C# (CSX)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [Скрипт F#](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)
