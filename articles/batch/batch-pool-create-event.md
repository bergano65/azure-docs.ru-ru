---
title: Событие создания пула пакетной службы Azure | Документы Майкрософт
description: Справочник по событию создания пула пакетной службы.
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: 3f7b20cb915e0a969c6a400c07a094943f7f3a1b
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70258444"
---
# <a name="pool-create-event"></a>Событие создания пула

 Это событие возникает при создании пула. Содержимое журнала предоставляет общие сведения о пуле. Обратите внимание, что если целевой размер пула больше 0 вычислительных узлов, сразу после этого события начинается событие начала изменения размера пула.

 В следующем примере показан текст события создания пула для пула, созданного с помощью `CloudServiceConfiguration` свойства.

```
{
    "id": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Standard_F1s",
    "imageType": "VirtualMachineConfiguration",
    "cloudServiceConfiguration": {
        "osFamily": "3",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "virtualMachineConfiguration": {
          "imageReference": {
            "publisher": " ",
            "offer": " ",
            "sku": " ",
            "version": " "
          },
          "nodeAgentId": " "
        },
    "resizeTimeout": "300000",
    "targetDedicatedNodes": 2,
    "targetLowPriorityNodes": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoScale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

|Элемент|Тип|Примечания|
|-------------|----------|-----------|
|`id`|Строковое|Идентификатор пула.|
|`displayName`|Строковое|Отображаемое имя пула.|
|`vmSize`|Строковое|Размер виртуальных машин в пуле. Все виртуальные машины в пуле имеют одинаковый размер. <br/><br/> Сведения о доступных размерах виртуальных машин для пулов облачных служб (пулы, созданные с помощью cloudServiceConfiguration), см. в статье [Размеры для облачных служб](https://azure.microsoft.com/documentation/articles/cloud-services-sizes-specs/). Пакетная служба поддерживает все размеры виртуальных машин для облачных служб, кроме `ExtraSmall`.<br/><br/> Сведения о доступных размерах виртуальных машин для пулов при использовании образов из магазина виртуальных машин (пулы, созданные с помощью virtualMachineConfiguration) см. в статье [Размеры виртуальных машин](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-sizes/) (Linux) или [Размеры виртуальных машин](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/) (Windows). Пакетная служба поддерживает все размеры виртуальных машин Azure, кроме `STANDARD_A0`. Для хранилища класса Premium также не поддерживаются размеры таких серий: `STANDARD_GS`, `STANDARD_DS` и `STANDARD_DSV2`.|
|`imageType`|Строковое|Метод развертывания для образа. Поддерживаемые значения — `virtualMachineConfiguration` или.`cloudServiceConfiguration`|
|[`cloudServiceConfiguration`](#bk_csconf)|Комплексный тип|Конфигурация облачной службы для пула.|
|[`virtualMachineConfiguration`](#bk_vmconf)|Комплексный тип|Конфигурация виртуальной машины для пула.|
|[`networkConfiguration`](#bk_netconf)|Комплексный тип|Конфигурация сети для пула.|
|`resizeTimeout`|Time|Время ожидания при выделении вычислительных узлов для пула, указанное для последней операции изменения размера в этом пуле.  (Исходная регулировка размера при создании пула считается изменением размера.)|
|`targetDedicatedNodes`|Int32|Число выделенных единиц вычислений, запрашиваемых для пула.|
|`targetLowPriorityNodes`|Int32|Количество расчетных узлов с низким приоритетом, запрошенных для пула.|
|`enableAutoScale`|Bool|Указывает, корректируется ли размер пула автоматически с течением времени.|
|`enableInterNodeCommunication`|Bool|Указывает, настроен ли пул для прямой связи между узлами.|
|`isAutoPool`|Bool|Определяет, создан ли пул с помощью механизма AutoPool задания.|
|`maxTasksPerNode`|Int32|Максимальное число задач, которые могут быть запущены одновременно на одном вычислительном узле в пуле.|
|`vmFillType`|Строковое|Определяет, каким образом пакетная служба распределяет задачи между вычислительными узлами в пуле. Допустимые значения: "Spread" (Распределение) или "Pack" (Упаковка).|

###  <a name="bk_csconf"></a> cloudServiceConfiguration

|Имя элемента|Тип|Примечания|
|------------------|----------|-----------|
|`osFamily`|Строковое|Семейство гостевой ОС Azure для установки на виртуальных машинах в пуле.<br /><br /> Возможные значения:<br /><br /> **2** — семейство ОС 2, эквивалентное Windows Server 2008 R2 с пакетом обновления 1 (SP1).<br /><br /> **3** — семейство ОС 3, эквивалентное Windows Server 2012.<br /><br /> **4** — семейство ОС 4, эквивалентное Windows Server 2012 R2.<br /><br /> Дополнительные сведения см. в статье [Выпуски гостевой ОС Azure](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases).|
|`targetOSVersion`|Строковое|Версия гостевой ОС Azure для установки на виртуальных машинах в пуле.<br /><br /> Значение по умолчанию — **\*** , что означает последнюю версию операционной системы для заданного семейства.<br /><br /> Другие допустимые значения см. в статье [Выпуски гостевой ОС Azure](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases).|

###  <a name="bk_vmconf"></a> virtualMachineConfiguration

|Имя элемента|Тип|Примечания|
|------------------|----------|-----------|
|[`imageReference`](#bk_imgref)|Комплексный тип|Указывает информацию об используемой платформе или образе Marketplace.|
|`nodeAgentId`|Строковое|Номер SKU для агента узла пакетной службы, подготовленного на вычислительном узле.|
|[`windowsConfiguration`](#bk_winconf)|Комплексный тип|Указывает параметры операционной системы Windows на виртуальной машине. Это свойство не нужно задавать, если imageReference ссылается на образ операционной системы Linux.|

###  <a name="bk_imgref"></a> imageReference

|Имя элемента|Тип|Примечания|
|------------------|----------|-----------|
|`publisher`|Строковое|Издатель образа.|
|`offer`|Строковое|Предложение образа.|
|`sku`|Строковое|Номер SKU образа.|
|`version`|Строковое|Версия образа.|

###  <a name="bk_winconf"></a> windowsConfiguration

|Имя элемента|Тип|Примечания|
|------------------|----------|-----------|
|`enableAutomaticUpdates`|логический|Указывает, включено ли для виртуальной машины автоматическое обновление. Если это свойство не задано, используется значение по умолчанию true.|

###  <a name="bk_netconf"></a> networkConfiguration

|Имя элемента|Type|Примечания|
|------------------|--------------|----------|
|`subnetId`|Строковое|Указывает идентификатор ресурса для подсети, в которой создаются вычислительные узлы пула.|
