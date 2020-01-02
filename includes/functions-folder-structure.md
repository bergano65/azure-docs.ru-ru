---
title: включение файла
description: включение файла
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 09/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: fd220a4a955a8b2cd583c2fe9e3899cb36c1587d
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74935901"
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
* [Сценарий C# (CSX)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [Скрипт F#](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)
