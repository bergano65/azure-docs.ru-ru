---
title: Обновление агента службы Azure Backup
description: Здесь объясняется, почему необходимо обновить агент службы Azure Backup и откуда можно скачать обновление.
services: backup
cloud: ''
suite: ''
author: dcurwin
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 8/29/2018
ms.author: dacurwin
ms.openlocfilehash: c22fbcd07286ddffedd8fc2fdc12017b9338d7f7
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77161865"
---
## <a name="upgrade-the-mars-agent"></a>Обновление агента MARS

Версии агента Microsoft Azure Recovery Service (MARS) ранее 2.0.9083.0 зависят от Службы контроля доступа Azure (ACS). Агент MARS также называется агентом Azure Backup. В 2018 году Azure [отказался от службы контроля доступа Azure (ACS)](../articles/active-directory/azuread-dev/active-directory-acs-migration.md). Начиная с 19 марта 2018 г. использование любых версий агента MARS ниже 2.0.9083.0 будет приводить к сбою резервного копирования. Чтобы избежать сбоев резервного копирования или устранить их, [установите последнюю версию агента MARS](https://go.microsoft.com/fwlink/?linkid=229525). Для идентификации серверов, для которых требуется обновление агента MARS, следуйте инструкциям [по обновлению агентов MARS в блоге службы Backup](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/). Агент MARS используется для резервного копирования файлов, папок и данных состояния системы в Azure. System Center DPM и Azure Backup Server используют агент MARS для резервного копирования данных в Azure.
