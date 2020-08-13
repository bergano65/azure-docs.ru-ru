---
title: включить файл
description: включить файл
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 08/07/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: b7c3719b1539aa585dbc030bb8dfe732e73c81ac
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2020
ms.locfileid: "88185080"
---
## <a name="trusted-microsoft-services"></a>Доверенные службы Майкрософт
При включении параметра **разрешить доверенным службам Майкрософт обходить этот параметр брандмауэра** доступ к ресурсам концентраторов событий предоставляется следующим службам.

| Доверенная служба | Поддерживаемые сценарии использования | 
| --------------- | ------------------------- | 
| Сетка событий Azure | Разрешает службе "Сетка событий Azure" отправку событий в концентраторы событий в пространстве имен концентраторов событий. |
| Azure Monitor (параметры диагностики) | Позволяет Azure Monitor отправить диагностические данные в концентраторы событий в пространстве имен концентраторов событий. |