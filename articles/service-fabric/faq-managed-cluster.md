---
title: Распространенные вопросы об управляемых кластерах Service Fabric
description: Часто задаваемые вопросы о Service Fabric управляемых кластерах, включая возможности, варианты использования и распространенные сценарии.
ms.topic: troubleshooting
ms.author: pepogors
author: peterpogorski
ms.date: 09/28/2020
ms.custom: references_regions
ms.openlocfilehash: 4dc41d2c13c834657534971041440bb744cfca38
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2020
ms.locfileid: "92319824"
---
# <a name="service-fabric-managed-clusters-frequently-asked-questions"></a>Распространенные вопросы об управляемых кластерах Service Fabric

Есть много часто задаваемых вопросов о том, что может сделать Service Fabric управляемые кластеры (Предварительная версия) и как они должны использоваться. В этом документе приведены ответы на многие из этих вопросов.

## <a name="general"></a>Общие сведения

### <a name="what-are-service-fabric-managed-clusters"></a>Что такое Service Fabric управляемых кластеров?

Service Fabric управляемые кластеры — это эволюция модели ресурсов кластеров Service Fabric, призванная упростить развертывание кластеров и управление ими. Service Fabric управляемый кластер использует Azure Resource Managerную модель инкапсуляции, чтобы пользователю было достаточно определить и развернуть только один ресурс кластера по сравнению с множеством независимых ресурсов, которые они должны развертывать сегодня (масштабируемый набор виртуальных машин, Load Balancer, IP-адрес и др.).

### <a name="what-regions-are-supported-in-the-preview"></a>Какие регионы поддерживаются в предварительной версии?

Поддерживаемые регионы для предварительной версии Service Fabric управляемых кластеров: `centraluseuap` , `eastus2euap` , `eastasia` ,, `northeurope` `westcentralus` и `eastus2` .

### <a name="can-i-do-an-in-place-migration-of-my-existing-service-fabric-cluster-to-a-managed-cluster-resource"></a>Можно ли выполнить перенос существующего кластера Service Fabric в управляемый кластерный ресурс на месте?

В настоящее время потребуется создать новый ресурс кластера Service Fabric, чтобы использовать новый тип ресурса кластера, управляемого Service Fabric.

### <a name="is-there-an-additional-cost-for-service-fabric-managed-clusters"></a>Существуют ли дополнительные затраты на Service Fabric управляемых кластеров?

Нет, нет дополнительных затрат, связанных с Service Fabric управляемым кластером за пределами затрат на базовые ресурсы вычислений, хранения и сетевых ресурсов, необходимых для кластера.

### <a name="is-there-a-new-sla-introduced-by-the-service-fabric-managed-cluster-resource"></a>Есть ли новое соглашение об уровне обслуживания, представленное Service Fabric ресурсом управляемого кластера?

Соглашение об уровне обслуживания не меняется от текущей модели ресурсов Service Fabric.

### <a name="what-is-the-difference-between-a-basic-and-standard-sku-cluster"></a>В чем разница между базовым и кластером SKU уровня "Стандартный"?

Базовый кластер SKU означает, что большинство конфигураций предоставляется поставщиком ресурсов Service Fabric. Кластеры уровня "базовый" предназначены для тестирования и подготовки в рабочей среде. Кластер уровня SKU "Стандартный" позволяет пользователям настраивать кластер в соответствии с потребностями. Дополнительные сведения см. в разделе [Service Fabric SKU управляемого кластера](./overview-managed-cluster.md#service-fabric-managed-cluster-skus) .

## <a name="cluster-deployment-and-management"></a>Развертывание кластера и управление им

### <a name="i-run-custom-script-extensions-on-my-virtual-machine-scale-set-can-i-continue-to-do-that-with-a-managed-service-fabric-resource"></a>Я запускаю пользовательские расширения сценариев в масштабируемом наборе виртуальных машин, можно ли продолжить это с управляемым ресурсом Service Fabric?

Да, вы по-прежнему можете указать расширения виртуальной машины для типа узла. Дополнительные сведения см. в разделе Образец расширения типа узла.

### <a name="i-want-to-have-an-internal-only-load-balancer-is-that-possible"></a>Я хочу использовать только внутреннюю подсистему балансировки нагрузки?

В настоящее время невозможно использовать только внутреннюю подсистему балансировки нагрузки. Мы рекомендуем блокировать правила группы безопасности сети, чтобы заблокировать любой нежелательный входящий и исходящий трафик.

### <a name="can-i-autoscale-my-cluster"></a>Можно ли автоматически масштабировать кластер? 
Автомасштабирование в настоящее время недоступно в предварительной версии.

### <a name="can-i-deploy-my-cluster-across-availability-zones"></a>Можно ли развернуть кластер в зонах доступности? 
Кластеры зон с перекрестной доступностью в настоящее время недоступны в предварительной версии.

### <a name="can-i-select-between-automatic-and-manual-upgrades-for-my-cluster-runtime"></a>Можно ли выбрать между автоматическим и ручным обновлением среду выполнения кластера? 
В предварительной версии все обновления среды выполнения будут выполняться автоматически.

## <a name="applications"></a>приложения;

### <a name="is-there-a-local-development-experience-for-service-fabric-managed-clusters"></a>Существует ли локальная среда разработки для Service Fabric управляемых кластеров?

Локальная среда разработки остается неизменной по отношению к существующим Service Fabric кластерам. Дополнительные сведения см. в статье [Создание приложения .NET](./service-fabric-quickstart-dotnet.md) для получения дополнительных сведений о локальном интерфейсе разработки.

### <a name="can-i-deploy-my-applications-as-an-azure-resource-manager-resource"></a>Можно ли развернуть приложения как ресурс Azure Resource Manager?

В предварительной версии нельзя развертывать приложения как ресурсы Azure Resource Manager. Приложения должны быть развернуты путем непосредственного подключения к кластеру с помощью PowerShell или CLI. Эта функция будет добавлена перед управляемыми Service Fabric кластерами введите общедоступную доступность.