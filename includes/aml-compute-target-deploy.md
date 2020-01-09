---
title: включить файл
description: включить файл
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/23/2019
ms.openlocfilehash: 59e545e788fd6173de70e6d1580cf2832f71b72b
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/28/2019
ms.locfileid: "75540468"
---
| Целевой объект вычисления | Используется для | Поддержка GPU | Поддержка FPGA | Description |
| ----- | ----- | ----- | ----- | ----- |
| [Локальная служба веб-&nbsp;&nbsp;](../articles/machine-learning/how-to-deploy-and-where.md#local) | Тестирование и отладка | &nbsp; | &nbsp; | Используется для ограниченного тестирования и устранения неполадок. Аппаратное ускорение зависит от использования библиотек в локальной системе.
| [Машинное обучение Azure служб вычислительных экземпляров&nbsp;службы веб-&nbsp;](../articles/machine-learning/how-to-deploy-and-where.md#notebookvm) | Тестирование и отладка | &nbsp; | &nbsp; | Используется для ограниченного тестирования и устранения неполадок.
| [Служба Azure Kubernetes (AKS)](../articles/machine-learning/how-to-deploy-and-where.md#aks) | Вывод в режиме реального времени |  [Да](../articles/machine-learning/how-to-deploy-inferencing-gpus.md) (развертывание веб-службы) | [Да](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |Используется для крупномасштабных развертываний в реальном масштабе. Обеспечивает быстрое время отклика и автоматическое масштабирование развернутой службы. Автоматическое масштабирование кластера не поддерживается с помощью пакета SDK для Машинное обучение Azure. Чтобы изменить узлы в кластере AKS, используйте пользовательский интерфейс для кластера AKS в портал Azure. AKS — единственный доступный для конструктора параметр. |
| [Экземпляры контейнеров Azure](../articles/machine-learning/how-to-deploy-and-where.md#aci); | Тестирование или разработка | &nbsp;  | &nbsp; | Используйте для небольших рабочих нагрузок на основе ЦП, требующих менее 48 ГБ ОЗУ. |
| [Машинное обучение Azureные кластеры](../articles/machine-learning/how-to-run-batch-predictions.md) | Образца Вывод пакетной&nbsp; | [Да](../articles/machine-learning/how-to-run-batch-predictions.md) (конвейер машинного обучения) | &nbsp;  | Выполнение пакетной оценки на бессерверных вычислениях. Поддерживает виртуальные машины с обычным и низким приоритетными уровнями. |
| [Функции Azure](../articles/machine-learning/how-to-deploy-functions.md) | Образца Вывод в режиме реального времени | &nbsp; | &nbsp; | &nbsp; |
| [Azure IoT Edge](../articles/machine-learning/how-to-deploy-and-where.md#iotedge) | Образца Модуль&nbsp;IoT |  &nbsp; | &nbsp; | Развертывание и обслуживание моделей машинного обучения на устройствах IoT. |
| [Что представляет собой Azure Data Box Edge (ознакомительная версия)?](../articles/databox-online/data-box-edge-overview.md)   | Via IoT Edge |  &nbsp; | Да | Развертывание и обслуживание моделей машинного обучения на устройствах IoT. |

> [!NOTE]
> Хотя целевые объекты вычислений, например локальные, Машинное обучение Azure вычислительных кластеров и Машинное обучение Azure вычислительных кластерах, поддерживают GPU для обучения и экспериментирования, использование GPU для вывода __при развертывании в качестве веб-службы__ поддерживается только в службе Azure Kubernetes.
>
> Использование GPU для определения, __Если оценка с помощью конвейера машинного обучения__ поддерживается только в машинное обучение Azureных вычислениях.