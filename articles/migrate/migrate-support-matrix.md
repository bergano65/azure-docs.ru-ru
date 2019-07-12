---
title: Таблица поддержки Azure служба "Миграция"
description: Предоставляет сводку параметров поддержки и ограничения для службы "Миграция Azure".
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: raynew
ms.openlocfilehash: b2ca1b9118ecc3d112a49bb4c79b413c46fe67cb
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811561"
---
# <a name="azure-migrate-support-matrix"></a>Таблица поддержки Azure служба "Миграция"

Можно использовать [службы "Миграция Azure"](migrate-overview.md) оценка и миграция машин в облако Microsoft Azure. В этой статье перечислены параметры общей поддержки и ограничения для развертывания и сценариев "Миграция Azure".


## <a name="azure-migrate-versions"></a>Версии Azure служба "Миграция"

Существует две версии службы "Миграция Azure".

- **Текущая версия**: С помощью этой версии, вы можете создать новые проекты "Миграция Azure", обнаружение локальных оценивает и координировать оценки и миграции. [Узнайте больше](whats-new.md#azure-migrate-new-version).
- **Предыдущая версия**: Для клиента с помощью предыдущей версии "Миграция Azure" (поддерживалась только оценка локальных виртуальных машин VMware) теперь следует использовать текущую версию. В предыдущей версии нельзя создавать новые проекты "Миграция Azure" или выполнять новые операции обнаружения.

## <a name="supported-migration-scenarios"></a>Поддерживаемые сценарии миграции

В таблице перечислены поддерживаемые сценарии миграции.

**Развертывание** | **Сведения о*** 
--- | --- 
**Оценка на предприятии** | Оценка локальных рабочих нагрузок и данных виртуальных машин VMware и виртуальных машин Hyper-V. Оценка с помощью оценки сервера миграции Azure и Microsoft Data Migration Assistant (DMA), а также сторонние средства, которые включают Cloudamize, Технический Corent и Turbonomic Server.
**В локальной миграции в Azure** | Перенос рабочих нагрузок и данных, выполняющимся на физические серверы, виртуальные машины VMware, виртуальных машин Hyper-V и на экземпляры AWS/GCP, в Azure. Миграция с помощью оценки сервера миграции Azure и Azure Database Migration Service (DMS), а также с помощью сторонних средств, которые включают Carbonite и CorentTech.

Конкретное программное средство поддержки можно представить следующим образом.

**Средство** | **Оценка/Миграция** | **Сведения**
--- | --- | ---
"Миграция Azure" Оценка Server | Оценка | Испытать сервер оценки для [Hyper-V](tutorial-prepare-hyper-v.md) и [VMware](tutorial-prepare-vmware.md).
Cloudamize | Оценка | [Узнайте больше](https://www.cloudamize.com/platform#tab-0).
CorentTech | Оценка | [Узнайте больше](https://www.corenttech.com/).
Turbonomic | Оценка | [Узнайте больше](https://turbonomic.com/solutions/technologies/azure-cloud/).
Azure миграции сервера | Миграция | Испытайте миграции сервера для [Hyper-V](tutorial-migrate-hyper-v.md) и [VMware](tutorial-migrate-vmware.md).
Carbonite | Миграция | [Узнайте больше](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure).
CorentTech | Миграция | [Узнайте больше](https://www.corenttech.com/).


## <a name="azure-migrate-projects"></a>Проекты Azure служба "Миграция"

**Поддержка** | **Сведения**
--- | ---
Subscription | Может иметь одного проекта "Миграция Azure" в подписке.
Разрешения Azure | Необходимо иметь разрешения владельца или участника в подписке Создание проекта "Миграция Azure".
Виртуальные машины VMware  | Оценка до 35 000 виртуальных машин VMware в одном проекте.
Виртуальные машины Hyper-V | Оценка до 10 000 виртуальных машин Hyper-V в одном проекте.

Проект можно включать виртуальные машины VMware и виртуальных машин Hyper-V, в установленных пределах для оценки.


## <a name="vmware-assessment-and-migration"></a>VMware внутренней оценки и миграции

[Просмотрите](migrate-support-matrix-vmware.md) оценки сервера миграции Azure и миграция сервера Матрица поддержки для виртуальных машин VMware.

## <a name="hyper-v-assessment-and-migration"></a>Hyper-V внутренней оценки и миграции

[Просмотрите](migrate-support-matrix-hyper-v.md) оценки сервера миграции Azure и миграция сервера Матрица поддержки для виртуальных машин Hyper-V.


## <a name="next-steps"></a>Следующие шаги

- [Оценка виртуальных машин VMware](tutorial-assess-vmware.md) для миграции.
- [Оценка виртуальных машин Hyper-V](tutorial-assess-hyper-v.md) для миграции.

