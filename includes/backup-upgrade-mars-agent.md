---
title: Обновление агента Azure Backup
description: В этих сведениях объясняется, почему необходимо обновить агент Azure Backup и где можно загрузить обновление.
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
ms.openlocfilehash: 227ec87081817b273b66684e31a0ebd0e11134a0
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197132"
---
## <a name="upgrade-the-mars-agent"></a>Обновление агента MARS

Версии агента Службы восстановления Microsoft Azure (MARS) ниже 2.0.9083.0 имеют зависимость от службы контроля доступа Azure. Агент MARS также называется агентом Azure Backup.

В 2018 Майкрософт не является [устаревшей службой контроля доступа Azure](../articles/active-directory/azuread-dev/active-directory-acs-migration.md). Начиная с 19 марта 2018 во всех версиях агента MARS ниже 2.0.9083.0 будут возникать сбои резервного копирования. Чтобы избежать сбоев резервного копирования или устранить их, [установите последнюю версию агента MARS](https://go.microsoft.com/fwlink/?linkid=229525). Чтобы указать серверы, для которых требуется обновление агента MARS, выполните действия, описанные в [блоге по резервному копированию, чтобы обновить агенты Mars](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/).

Агент MARS используется для резервного копирования файлов и папок и данных состояния системы в Azure. System Center DPM и Azure Backup Server используют агент MARS для резервного копирования данных в Azure.
