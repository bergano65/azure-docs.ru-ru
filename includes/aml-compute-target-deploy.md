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
ms.openlocfilehash: ba1e0eee5de65364d8e5e762195f7de98384c2a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76122595"
---
| Целевой объект вычисления | Используется для | Поддержка GPU | Поддержка FPGA | Описание |
| ----- | ----- | ----- | ----- | ----- |
| [&nbsp;Местный&nbsp;веб-сервис](../articles/machine-learning/how-to-deploy-and-where.md#local) | Тестирование/отладка | &nbsp; | &nbsp; | Используется для ограниченного тестирования и устранения неполадок. Аппаратное ускорение зависит от использования библиотек в локальной системе.
| [Веб-служба Azure&nbsp;Machine&nbsp;Learning вычисляет экземпляры](../articles/machine-learning/how-to-deploy-and-where.md#notebookvm) | Тестирование/отладка | &nbsp; | &nbsp; | Используется для ограниченного тестирования и устранения неполадок.
| [Служба Лазурного Кубернета (AKS)](../articles/machine-learning/how-to-deploy-and-where.md#aks) | Вывод в реальном времени |  [Да](../articles/machine-learning/how-to-deploy-inferencing-gpus.md) (развертывание веб-службы) | [Да](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |Используется для крупномасштабных производственных развертываний. Обеспечивает быстрое время отклика и автоматическое развертывание службы. Автоматизация кластеров не поддерживается через SDK машинного обучения Azure. Чтобы изменить узлы в кластере AKS, используйте uI для кластера AKS на портале Azure. AKS является единственным вариантом, доступным для дизайнера. |
| [Инстанции контейнеров Azure](../articles/machine-learning/how-to-deploy-and-where.md#aci) | Тестирование или разработка | &nbsp;  | &nbsp; | Используется для низкомасштабных рабочих нагрузок на основе процессора, которые требуют менее 48 ГБ оперативной памяти. |
| [вычислительные кластеры Машинного обучения Azure;](../articles/machine-learning/how-to-use-parallel-run-step.md) | (Предварительный просмотр) Вывод&nbsp;о пакете | [Да](../articles/machine-learning/how-to-use-parallel-run-step.md) (конвейер машинного обучения) | &nbsp;  | Выполнить пакетный скоринг на безсерверных вычислений. Поддерживает нормальные и низкоприоритетные VM. |
| [Проверка](../articles/machine-learning/how-to-deploy-functions.md) | (Предварительный просмотр) Вывод в реальном времени | &nbsp; | &nbsp; | &nbsp; |
| [Azure IoT Edge](../articles/machine-learning/how-to-deploy-and-where.md#iotedge) | (Предварительный просмотр) Модуль&nbsp;IoT |  &nbsp; | &nbsp; | Развертывайте и обслуживайте модели ML на устройствах IoT. |
| [Что представляет собой Azure Data Box Edge (ознакомительная версия)?](../articles/databox-online/data-box-edge-overview.md)   | Через IoT Края |  &nbsp; | Да | Развертывайте и обслуживайте модели ML на устройствах IoT. |

> [!NOTE]
> Хотя вычисление таких целей, как локальный, Azure Machine Learning compute instance и Azure Machine Learning compute, поддерживает графический процессор для обучения и экспериментов, использование графического процессора для выводов __при развертывании в качестве веб-сервиса__ поддерживается только в службе Azure Kubernetes.
>
> Использование графического процессора для выводов __при использовании с помощью конвейера машинного обучения__ поддерживается только на Azure Machine Learning Compute.