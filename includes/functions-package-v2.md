---
title: Включить имя файла
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "77205703"
---
Добавьте поддержку в предпочтительной среде разработки, используя следующие методы.

| Среда разработки  | Тип приложения      | Добавление поддержки |
|--------------------------|-----------------------|----------------|
| Visual Studio            | Библиотека классов C#      | [Установка пакета NuGet](../articles/azure-functions/functions-bindings-register.md#vs) |
| Visual Studio Code       | На основе [основных средств](../articles/azure-functions/functions-run-local.md) | [Регистрация пакета расширений](../articles/azure-functions/functions-bindings-register.md#extension-bundles)<br><br>Рекомендуется установить [расширение инструментов Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) . |
| Любой другой редактор или интегрированная среда разработки     | На основе [основных средств](../articles/azure-functions/functions-run-local.md) | [Регистрация пакета расширений](../articles/azure-functions/functions-bindings-register.md#extension-bundles) |
| Портал Azure             | Только в сети на портале | Устанавливает при добавлении привязки<br /><br /> См. статью [Обновление расширений](../articles/azure-functions/install-update-binding-extensions-manual.md) для обновления существующих расширений привязки без повторной публикации приложения функции. |
