---
title: Включить имя файла
description: включить файл
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/23/2019
ms.openlocfilehash: ba1e0eee5de65364d8e5e762195f7de98384c2a7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76122595"
---
| Целевой объект вычисления | Назначение | Поддержка GPU | Поддержка FPGA | Описание |
| ----- | ----- | ----- | ----- | ----- |
| [&nbsp;Локальная&nbsp;веб-служба](../articles/machine-learning/how-to-deploy-and-where.md#local) | Тестирование и отладка | &nbsp; | &nbsp; | Используется для ограниченного тестирования и устранения неполадок. Аппаратное ускорение зависит от использования библиотек в локальной системе.
| [Веб-&nbsp;служба&nbsp;машинное обучение Azureного вычислительного экземпляра](../articles/machine-learning/how-to-deploy-and-where.md#notebookvm) | Тестирование и отладка | &nbsp; | &nbsp; | Используется для ограниченного тестирования и устранения неполадок.
| [Служба Azure Kubernetes (AKS)](../articles/machine-learning/how-to-deploy-and-where.md#aks) | Вывод в режиме реального времени |  [Да](../articles/machine-learning/how-to-deploy-inferencing-gpus.md) (развертывание веб-службы) | [Да](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |Используется для крупномасштабных развертываний в реальном масштабе. Обеспечивает быстрое время отклика и автоматическое масштабирование развернутой службы. Автоматическое масштабирование кластера не поддерживается с помощью пакета SDK для Машинное обучение Azure. Чтобы изменить узлы в кластере AKS, используйте пользовательский интерфейс для кластера AKS в портал Azure. AKS — единственный доступный для конструктора параметр. |
| [Экземпляры контейнеров Azure](../articles/machine-learning/how-to-deploy-and-where.md#aci); | Тестирование или разработка | &nbsp;  | &nbsp; | Используйте для небольших рабочих нагрузок на основе ЦП, требующих менее 48 ГБ ОЗУ. |
| [вычислительные кластеры Машинного обучения Azure;](../articles/machine-learning/how-to-use-parallel-run-step.md) | Образца Вывод&nbsp;пакета | [Да](../articles/machine-learning/how-to-use-parallel-run-step.md) (конвейер машинного обучения) | &nbsp;  | Выполнение пакетной оценки на бессерверных вычислениях. Поддерживает виртуальные машины с обычным и низким приоритетными уровнями. |
| [Функции Azure](../articles/machine-learning/how-to-deploy-functions.md) | Образца Вывод в режиме реального времени | &nbsp; | &nbsp; | &nbsp; |
| [Azure IoT Edge](../articles/machine-learning/how-to-deploy-and-where.md#iotedge) | Образца Модуль&nbsp;IOT |  &nbsp; | &nbsp; | Развертывание и обслуживание моделей машинного обучения на устройствах IoT. |
| [Что представляет собой Azure Data Box Edge (ознакомительная версия)?](../articles/databox-online/data-box-edge-overview.md)   | Via IoT Edge |  &nbsp; | Да | Развертывание и обслуживание моделей машинного обучения на устройствах IoT. |

> [!NOTE]
> Хотя целевые объекты вычислений, например локальные, Машинное обучение Azure вычислительных кластеров и Машинное обучение Azure вычислительных кластерах, поддерживают GPU для обучения и экспериментирования, использование GPU для вывода __при развертывании в качестве веб-службы__ поддерживается только в службе Azure Kubernetes.
>
> Использование GPU для определения, __Если оценка с помощью конвейера машинного обучения__ поддерживается только в машинное обучение Azureных вычислениях.