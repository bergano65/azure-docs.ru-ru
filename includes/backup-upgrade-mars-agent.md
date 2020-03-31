---
title: Вопросы об агенте службы Azure Backup
description: Эта информация объясняет, почему следует обновить агент резервного копирования Azure и где загрузить обновление.
services: backup
cloud: ''
suite: ''
author: dcurwin
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 03/03/2020
ms.author: dacurwin
ms.openlocfilehash: bd298f758d6109b908db01dd2ae3b97e5e2f714a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673178"
---
## <a name="upgrade-the-mars-agent"></a>Обновление агента MARS

Версии служб восстановления Microsoft Azure (MARS) Агент ниже 2.0.9083.0 имеют зависимость от службы управления доступом Azure. Агент MARS также называют агентом резервного копирования Azure.

В 2018 году microsoft [утилизировала сервис управления доступом Azure.](../articles/active-directory/azuread-dev/active-directory-acs-migration.md) Начиная с 19 марта 2018 года, все версии агента MARS ниже 2.0.9083.0 будут испытывать сбои резервного копирования. Чтобы избежать сбоев резервного копирования или устранить их, [установите последнюю версию агента MARS](https://support.microsoft.com/help/4538314/update-for-azure-backup-for-microsoft-azure-recovery-services-agent). Чтобы определить серверы, требующие обновления агента MARS, выполните последующие действия в [службе обновления служб восстановления Microsoft Azure (MARS).](../articles/backup/upgrade-mars-agent.md)

Агент MARS используется для резервного копирования файлов и папок и системных данных состояния в Azure. System Center DPM и Azure Backup Server используют агент MARS для резервного копирования данных в Azure.
