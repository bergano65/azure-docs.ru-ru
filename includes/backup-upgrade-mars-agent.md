---
title: Обновление агента службы Azure Backup
description: Здесь объясняется, почему необходимо обновить агент службы Azure Backup и откуда можно скачать обновление.
services: backup
cloud: ''
suite: ''
author: markgalioto
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 8/29/2018
ms.author: markgal
ms.openlocfilehash: 56310b7356dd9e263238234cf3e28bd498fa70fc
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67185643"
---
## <a name="upgrade-the-mars-agent"></a>Обновление агента MARS

Версии агента Microsoft Azure Recovery Service (MARS) ранее 2.0.9083.0 зависят от Службы контроля доступа Azure (ACS). Агент MARS также называется агентом Azure Backup. В 2018 году Azure [отказался от службы контроля доступа Azure (ACS)](../articles/active-directory/develop/active-directory-acs-migration.md). Начиная с 19 марта 2018 г. использование любых версий агента MARS ниже 2.0.9083.0 будет приводить к сбою резервного копирования. Чтобы избежать сбоев резервного копирования или устранить их, [установите последнюю версию агента MARS](https://go.microsoft.com/fwlink/?linkid=229525). Для идентификации серверов, для которых требуется обновление агента MARS, следуйте инструкциям [по обновлению агентов MARS в блоге службы Backup](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/). Агент MARS используется для резервного копирования файлов, папок и данных состояния системы в Azure. System Center DPM и Azure Backup Server используют агент MARS для резервного копирования данных в Azure.
