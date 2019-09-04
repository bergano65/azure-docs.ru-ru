---
title: Матрица поддержки службы "миграция Azure"
description: Содержит сводку параметров поддержки и ограничений для службы "миграция Azure".
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: raynew
ms.openlocfilehash: 3fb36eb9b8507903d1aee00b82c375685a4dfa6c
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70279467"
---
# <a name="azure-migrate-support-matrix"></a>Матрица поддержки службы "миграция Azure"

[Службу "миграция Azure](migrate-overview.md) " можно использовать для оценки и миграции компьютеров в облако Microsoft Azure. В этой статье перечислены общие параметры поддержки и ограничения для сценариев и развертываний службы "миграция Azure".


## <a name="azure-migrate-versions"></a>Версии службы "Миграция Azure"

Существует две версии службы "Миграция Azure".

- **Текущая версия**. С помощью этой версии можно создавать проекты службы "миграция Azure", обнаруживать локальные оценки и управлять оценками и миграциями. [Узнайте больше](whats-new.md#azure-migrate-new-version).
- **Предыдущая версия**. Теперь пользователям, которые используют предыдущую версии службы "Миграция Azure" (где поддерживалась только оценка локальных виртуальных машин VMware), необходимо использовать текущую версию. В предыдущей версии нельзя создать новые проекты службы "миграция Azure" или выполнить новые операции обнаружения.

## <a name="supported-migration-scenarios"></a>Поддерживаемые сценарии миграции

В таблице перечислены поддерживаемые сценарии миграции.

**Развертывание** | **Сведения** 
--- | --- 
**Оценка в локальной среде** | Оцените локальные рабочие нагрузки и данные, работающие на виртуальных машинах VMware и виртуальных машинах Hyper-V. Оцените использование Azure Migrate Server для оценки и Помощник по миграции данных (Майкрософт) (DMA), а также средств сторонних производителей, включающих Cloudamize, службу технической сертификации и Turbonomic Server.
**Локальная миграция в Azure** | Перенос рабочих нагрузок и данных, работающих на физических серверах, виртуальных машинах VMware, виртуальных машинах Hyper-V, физических серверах и облачных виртуальных машинах в Azure. Выполните миграцию с помощью средства Azure Migrate Server для оценки и Azure Database Migration Service (DMS), а также с помощью сторонних средств, включающих карбоните и Коренттеч.

Ниже приведены сводные сведения о поддержке конкретных инструментов.

**Средство** | **Оценка и миграция** | **Сведения**
--- | --- | ---
Оценка сервера службы "Миграция Azure" | Оценка | Испытайте оценку сервера для [Hyper-V](tutorial-prepare-hyper-v.md) и [VMware](tutorial-prepare-vmware.md).
Cloudamize | Оценка | [Узнайте больше](https://www.cloudamize.com/platform#tab-0).
CorentTech | Оценка | [Узнайте больше](https://www.corenttech.com/).
Turbonomic | Оценка | [Узнайте больше](https://turbonomic.com/solutions/technologies/azure-cloud/).
Миграция сервера службы "Миграция Azure" | Перенос | Попробуйте выполнить миграцию сервера для [Hyper-V](tutorial-migrate-hyper-v.md) и [VMware](tutorial-migrate-vmware.md).
Carbonite | Перенос | [Узнайте больше](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure).
CorentTech | Перенос | [Узнайте больше](https://www.corenttech.com/).


## <a name="azure-migrate-projects"></a>Проекты службы "миграция Azure"

**Поддержка** | **Сведения**
--- | ---
Подписка | В подписке может быть несколько проектов службы "миграция Azure".
Разрешения Azure | Для создания проекта службы "миграция Azure" необходимо иметь разрешения участника или владельца в подписке.
Виртуальные машины VMware  | Оцените до 35 000 виртуальных машин VMware в одном проекте.
Виртуальные машины Hyper-V | Оцените 35 000 виртуальных машин Hyper-V в одном проекте.

Проект может содержать как виртуальные машины VMware, так и виртуальные машины Hyper-V, вплоть до ограничений оценки.


## <a name="vmware-assessment-and-migration"></a>Оценка и миграция VMware

[Ознакомьтесь](migrate-support-matrix-vmware.md) с таблицей "миграция сервера Azure для миграции серверов и миграция серверов" для виртуальных машин VMware.

## <a name="hyper-v-assessment-and-migration"></a>Оценка и перенос виртуальных машин Hyper-V

[Ознакомьтесь](migrate-support-matrix-hyper-v.md) с матрицей Azure Migration Server для оценки серверов и миграции серверов для виртуальных машин Hyper-V.


## <a name="next-steps"></a>Следующие шаги

- [Оцените виртуальные машины VMware](tutorial-assess-vmware.md) для миграции.
- [Оцените виртуальные машины Hyper-V](tutorial-assess-hyper-v.md) для миграции.

