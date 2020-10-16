---
title: включить файл
description: включить файл
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2020
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 2eb82eb8b4a64cbbfef91539f46ba084aa73f207
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "77205703"
---
Добавьте поддержку в предпочитаемой среде разработки, используя указанные ниже методы.

| Среда разработки  | Тип приложения      | Добавление поддержки |
|--------------------------|-----------------------|----------------|
| Visual Studio            | Библиотека классов C#      | [Установка пакета NuGet](../articles/azure-functions/functions-bindings-register.md#vs) |
| Visual Studio Code       | На базе [основных средств](../articles/azure-functions/functions-run-local.md) | [Регистрация пакета расширений](../articles/azure-functions/functions-bindings-register.md#extension-bundles)<br><br>Рекомендуем установить [расширение инструментов Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack). |
| Любой другой редактор или IDE     | На базе [основных средств](../articles/azure-functions/functions-run-local.md) | [Регистрация пакета расширений](../articles/azure-functions/functions-bindings-register.md#extension-bundles) |
| Портал Azure             | Только в Интернете на портале | Устанавливается при добавлении привязки<br /><br /> См. дополнительные сведения об [обновлении существующих расширений привязки](../articles/azure-functions/install-update-binding-extensions-manual.md) без необходимости переиздавать проект приложения-функции. |
