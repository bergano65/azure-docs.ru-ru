---
title: включение файла
description: включение файла
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/12/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 93332002cd2ac513d125e0f9eb75dff4a2d8760c
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836831"
---
## <a name="what-happens-to-my-app-during-deployment"></a>Что происходит с моим приложением во время развертывания?

Все методы развертывания официально поддерживаемых вносить изменения в файлы `/home/site/wwwroot` папку приложения. Эти файлы являются теми же самыми элементами, которые выполняются в рабочей среде. Таким образом развертывание может завершиться сбоем из-за заблокированные файлы. Приложение в рабочей среде может также работать непредсказуемым образом во время развертывания, так как не все файлы обновляются одновременно. Избежать этих проблем можно несколькими способами.

- Остановите приложение или включите для него автономный режим на время развертывания. Дополнительные сведения см. в разделе [забытое заблокированные файлы во время развертывания](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- Выполняйте развертывание в [промежуточный слот](../articles/app-service/deploy-staging-slots.md) с включенным [автоматическим переключением](../articles/app-service/deploy-staging-slots.md#configure-auto-swap). 
- Используйте [запуск из пакета](https://github.com/Azure/app-service-announcements/issues/84) вместо непрерывного развертывания.
