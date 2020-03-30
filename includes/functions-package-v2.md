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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77205703"
---
Добавьте поддержку в предпочитаемую вами среду разработки, используя следующие методы.

| Среда разработки  | Тип приложения      | Добавление поддержки |
|--------------------------|-----------------------|----------------|
| Visual Studio            | Библиотека классов C#      | [Установка пакета NuGet](../articles/azure-functions/functions-bindings-register.md#vs) |
| Visual Studio Code       | На основе [основных инструментов](../articles/azure-functions/functions-run-local.md) | [Регистрация пакета расширения](../articles/azure-functions/functions-bindings-register.md#extension-bundles)<br><br>Рекомендуется установка [расширения Azure Tools.](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) |
| Любой другой редактор/IDE     | На основе [основных инструментов](../articles/azure-functions/functions-run-local.md) | [Регистрация пакета расширения](../articles/azure-functions/functions-bindings-register.md#extension-bundles) |
| Портал Azure             | Онлайн только на портале | Устанавливается при добавлении привязки<br /><br /> Просмотрите [обновление расширений](../articles/azure-functions/install-update-binding-extensions-manual.md) для обновления существующих обязательных расширений без необходимости переиздания приложения функции. |
