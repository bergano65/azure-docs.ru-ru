---
title: Матрица поддержки Azure Migrate
description: Предоставляет сводку параметров поддержки и ограничений для службы Azure Migrate.
ms.topic: conceptual
ms.date: 03/22/2020
ms.author: raynew
ms.openlocfilehash: 0f766bf95bb7e26d942e7dde3f315bbef6d5dc5c
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535202"
---
# <a name="azure-migrate-support-matrix"></a>Матрица поддержки Azure Migrate

Можно использовать [службу Azure Migrate](migrate-overview.md) для оценки и переноса машин в облако Microsoft Azure. В этой статье кратко излагаются общие параметры и ограничения для сценариев и развертываний Azure Migrate.

## <a name="supported-assessmentmigration-scenarios"></a>Поддерживаемые сценарии оценки/миграции

В таблице кратко излагаются поддерживаемые сценарии обнаружения, оценки и миграции.

**Развертывание** | **Сведения** 
--- | --- 
**Обнаружение в специальном приложении** | Вы можете обнаружить приложения, роли и функции, работающие на VMware VMs. В настоящее время эта функция ограничивается только открытием. Оценка в настоящее время находится на уровне машины. Мы пока не предлагаем оценку приложения, роли или конкретной функции. 
**Оценка на территории** | Оцените рабочие нагрузки и данные, работающие на VMware VMs, Hyper-V V M и физических серверах. Оцените с помощью Azure Migrate Server Assessment и помощника по миграции данных Microsoft Data (DMA), а также других инструментов и предложений ISV.
**Миграция на территории Лазурного были** | Перенос рабочих нагрузок и данных, работающих на физических серверах, VMware VMs, Hyper-V VMs, физических серверах и облачных VMS в Azure. Мигрируйте с помощью Службы оценки данных Azure Migrate Server и Службы миграции баз данных Azure (DMS), а также с другими инструментами и предложениями ISV.


## <a name="supported-tools"></a>Поддерживаемые средства

Конкретная поддержка инструмента обобщена в таблице.

**Инструмент** | **Оценка** | **Миграция** 
--- | --- | ---
Оценка сервера службы "Миграция Azure" | Оцените [VMware VMs,](tutorial-prepare-vmware.md) [Hyper-V V Ms](tutorial-prepare-hyper-v.md)и [физические серверы.](tutorial-prepare-physical.md) |  Недоступно (NA)
Миграция сервера службы "Миграция Azure" | Н/Д | Мигрируйте [VMware VMs,](tutorial-migrate-vmware.md) [Hyper-V VMs](tutorial-migrate-hyper-v.md)и [физические серверы.](tutorial-migrate-physical-virtual-machines.md)
[Carbonite](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure) | Н/Д | Мигрируйте VMware VMs, Hyper-V VMs, физические серверы, общедоступные облачные нагрузки. 
[Облако](https://www.cloudamize.com/platform#tab-0)| Оцените VMw, V-V- M, физические серверы, общедоступные облачные нагрузки. | Н/Д
[Corent Technology](https://go.microsoft.com/fwlink/?linkid=2084928) | Оценить и перенести VMw, Hyper-V VMs, физические серверы, общедоступные облачные нагрузки. |  Мигрируйте VMware VMs, Hyper-V VMs, физические серверы, общедоступные облачные нагрузки.
[Устройство 42](https://go.microsoft.com/fwlink/?linkid=2097158) | Оцените VMw, V-V- M, физические серверы, общедоступные облачные нагрузки.| Н/Д
[DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | Оценка внутренних баз данных сервера S'L Server. | Н/Д
[DMS](https://docs.microsoft.com/azure/dms/dms-overview) | Н/Д | Мигрировать сервер, Oracle, MyS'L, PostgreS'L, MongoDB. 
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Оценка виртуальной настольной инфраструктуры (VDI) | Н/Д
[Movere](https://go.microsoft.com/fwlink/?linkid=2109528) | Оценка VMs VM, Hyper-V VMs, Xen VMs, физических машин, рабочих станций (включая VDI), общедоступных облачных нагрузок | Н/Д
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | Н/Д | Мигрируйте VMs-м вМ, Hyper-V, Xen VMs, VMs-м кВМ, физические машины, общедоступные облачные нагрузки 
[Turbonomic](https://go.microsoft.com/fwlink/?linkid=2094295)  | Оцените VMw, V-V- M, физические серверы, общедоступные облачные нагрузки. | Н/Д
[UnifyCloud](https://go.microsoft.com/fwlink/?linkid=2097195) | Оцените VMware VMs, Hyper-V VMs, физические серверы, общедоступные облачные рабочие нагрузки и базы данных S'L Server. | Н/Д
[Помощник по миграции Webapp](https://appmigration.microsoft.com/) | Оценка веб-приложений | Мигрировать веб-приложения.


## <a name="azure-migrate-projects"></a>Проекты Azure Migrate

**Поддержка** | **Сведения**
--- | ---
Подписка | В подписке можно иметь несколько проектов Azure Migrate.
Разрешения Azure | Для создания проекта Azure Migrate в подписке необходимы разрешения для авторов или владельцев.
Виртуальные машины VMware  | Оцените до 35 000 VMware VMs в одном проекте.
Виртуальные машины Hyper-V    | Оцените до 35 000 ви-в-м hyper-V в одном проекте.

Проект может включать в себя как VMware VMs и Hyper-V VMs, вплоть до пределов оценки.

## <a name="azure-permissions"></a>Разрешения Azure

Для того чтобы Azure Migrate работал с Azure, вам нужны эти разрешения, прежде чем начать оценку и миграцию машин.

**Задача** | **Разрешения** | **Сведения**
--- | --- | ---
Создание проекта службы "Миграция Azure" | Учетная запись Azure должна иметь разрешения на создание проекта. | Настройка для [VMware](tutorial-prepare-vmware.md#assign-permissions-to-create-project), [Hyper-V](tutorial-prepare-hyper-v.md#assign-permissions-to-create-project), или [физических серверов.](tutorial-prepare-physical.md#assign-permissions-to-create-project)
Регистрация устройства службы "Миграция Azure"| Azure Migrate использует легкий [прибор Azure Migrate](migrate-appliance.md) для оценки машин с оценкой Azure Migrate Server, а также для запуска [бесагентной миграции](server-migrate-overview.md) VMs-мв с помощью миграции серверов Azure Migrate. Этот прибор обнаруживает машины и отправляет метаданные и данные о производительности в Azure Migrate.<br/><br/> Во время регистрации провайдеры регистрации (Microsoft.OffAzure, Microsoft.Migrate и Microsoft.KeyVault) регистрируются по подписке, выбранной в приборе, так что подписка работает с поставщиком ресурсов. Чтобы зарегистрироваться, вам нужен доступ к автору или владельцу по подписке.<br/><br/> **VMware**- Во время посадки Azure Migrate создает два приложения Azure Active Directory (Azure AD). Первое приложение общается между агентами приборов и службой Azure Migrate. Приложение не имеет разрешений на вызовы ресурсов Azure или доступ к ресурсам RBAC. Второе приложение получает доступ к Убежище ключей Azure, созданное в подписке пользователя только для миграции без агента VMware. При миграции без агента Azure Migrate создает Хранилище ключей для управления ключами доступа к учетной записи хранения репликации в подписке. Он имеет доступ к RBAC в Хранилище ключей Azure (в клиенте-клиенте), когда открытие инициируется с прибора.<br/><br/> **Hyper-V**-Во время посадки. Azure Migrate создает одно приложение Azure AD. Приложение общается между агентами приборов и службой Azure Migrate. Приложение не имеет разрешений на вызовы ресурсов Azure или доступ к ресурсам RBAC. | Настройка для [VMware](tutorial-prepare-vmware.md#assign-permissions-to-register-the-appliance), [Hyper-V](tutorial-prepare-hyper-v.md#assign-permissions-to-register-the-appliance), или [физических серверов.](tutorial-prepare-physical.md#assign-permissions-to-register-the-appliance)
Создайте хранилище ключей для миграции без агентов VMware | Для переноса VMware VMs с бесагентной миграцией azure Migrate Server Azure Migrate Azure Migrate создает Ключевое Хранилище для управления ключами к учетной записи хранения репликации в подписке. Для создания хранилища вы устанавливаете разрешения (владелец, или Администратор пользовательского доступа) в группе ресурсов, в которой находится проект Azure Migrate. | [Настройка](tutorial-prepare-vmware.md#assign-permissions-to-create-a-key-vault) разрешений.

## <a name="supported-geographies-public-cloud"></a>Поддерживаемые географические регионы (общественное облако)

В общедоступном облаке можно создать проект Azure Migrate в ряде географических регионов. Хотя вы можете создавать проекты только в этих географических регионах, вы можете оценить или перенести машины для других целевых местоположений. Географический регион проекта используется только для хранения обнаруженных метаданных.

**География** | **Место хранения метаданных**
--- | ---
Азиатско-Тихоокеанский регион | Восточная Азия или Юго-Восточная Азия
Австралия | Австралия Восток или Австралия юго-восток
Бразилия | Южная Бразилия
Canada | Канада Центральной или Канада Восток
Европа | Северная или Западная Европа
Франция | Центральная Франция
Индия | Центральная Индия или южная Индия
Япония |  Япония Восток или Япония Запад
Корея | Корея Центральная или Корея южная
United Kingdom | Южная часть Соединенного Королевства или западная часть Соединенного Королевства
США | Центральные США или Запад США 2


## <a name="supported-geographies-azure-government"></a>Поддерживаемые географические регионы (Правительство Лазурного государства)

**Задача** | **География** | **Сведения**
--- | --- | ---
Создание проекта | США | Метаданные хранятся в США Gov Аризона, ГУБЕРНАТОР США Вирджиния
Целевая оценка | США | Целевые регионы: США Gov Аризона, США Gov Вирджиния / США Gov Техас
Репликация цели | США | Целевые регионы: США DoD Central, США DoD Востоке, США Gov Аризона, США Gov Айова, США Gov Техас, США Gov Вирджиния


## <a name="vmware-assessment-and-migration"></a>Оценка и миграция VMware

[Просмотрите](migrate-support-matrix-vmware.md) матрицу оценки миграции серверов Azure и поддержки миграции серверов для VMware VMs.

## <a name="hyper-v-assessment-and-migration"></a>Оценка и перенос виртуальных машин Hyper-V

[Просмотрите](migrate-support-matrix-hyper-v.md) матрицу оценки миграции серверов Azure и поддержки миграции серверов для Hyper-VM.



## <a name="azure-migrate-versions"></a>Версии службы "Миграция Azure"

Существует две версии службы "Миграция Azure".

- **Текущая версия**: Используя эту версию, вы можете создавать новые проекты Azure Migrate, открывать предварительные оценки и организовывать оценки и миграции. [Подробнее](whats-new.md).
- **Предыдущая версия**: Для клиента, использующего предыдущую версию Azure Migrate (поддерживалась только оценка предприимных VM VMw), теперь следует использовать текущую версию. В предыдущей версии нельзя создавать новые проекты Azure Migrate или выполнять новые открытия.

## <a name="next-steps"></a>Следующие шаги

- [Оцените VMw для](tutorial-assess-vmware.md) миграции.
- [Оценка Hyper-V VM](tutorial-assess-hyper-v.md) для миграции.

