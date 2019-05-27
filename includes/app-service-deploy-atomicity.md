---
title: включение файла
description: включение файла
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/08/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 8709956adee06e4e783ac5a7b317b2c4dec43e73
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66137114"
---
## <a name="what-happens-to-my-app-during-deployment"></a>Что происходит с моим приложением во время развертывания?

Все официально поддерживаемые методы развертывания объединяет одно: они вносят изменения в файлы в папке `/home/site/wwwroot` приложения. Именно эти файлы выполняются в рабочей среде. Поэтому развертывание может завершиться сбоем из-за заблокированных файлов или приложение в рабочей среде может вести себя непредсказуемо во время развертывания, так как не все файлы обновляются одновременно. Избежать этих проблем можно несколькими способами.

- Остановите приложение или включите для него автономный режим на время развертывания. Дополнительные сведения см. в разделе [Dealing with locked files during deployment](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment) (Устранение проблем из-за заблокированных файлов при развертывании).
- Выполняйте развертывание в [промежуточный слот](../articles/app-service/deploy-staging-slots.md) с включенным [автоматическим переключением](../articles/app-service/deploy-staging-slots.md#configure-auto-swap). 
- Используйте [запуск из пакета](https://github.com/Azure/app-service-announcements/issues/84) вместо этого.
