---
title: включение файла
description: включение файла
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 05/30/2019
ms.openlocfilehash: 59e5adb5d1ee35b983c9552a2076a373561c0b44
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67168297"
---
| Целевой объект вычисления | Использование | Поддержка GPU | Поддержка FPGA | Описание |
| ----- | ----- | ----- | ----- | ----- |
| [Локальный&nbsp;web&nbsp;службы](../articles/machine-learning/service/how-to-deploy-and-where.md#local) | Тестирование и отладка | Возможно | &nbsp; | Такие ограниченные возможности для тестирования и устранения неполадок. Аппаратное ускорение зависит от того, с помощью библиотек в локальной системе.
| [Служба Azure Kubernetes (AKS)](../articles/machine-learning/service/how-to-deploy-and-where.md#aks) | Вывод в режиме реального времени |  [Да](../articles/machine-learning/service/how-to-deploy-inferencing-gpus.md)  | [Да](../articles/machine-learning/service/how-to-deploy-fpga-web-service.md)   |Подходит для крупномасштабных рабочих развертываний. Обеспечивает автоматическое масштабирование и малое время отклика.  AKS является единственным доступным для визуального интерфейса. |
| [Экземпляры контейнеров Azure (ACI)](../articles/machine-learning/service/how-to-deploy-and-where.md#aci) | Тестирования или разработки | &nbsp;  | &nbsp; | Хорошо подходит для низкой масштаб рабочих нагрузок на основе использования ресурсов ЦП требующих < 48 ГБ ОЗУ |
| [Вычислительная среда Машинного обучения Azure](../articles/machine-learning/service/how-to-run-batch-predictions.md) | (Предварительная версия) Пакет&nbsp;вывод | Да | &nbsp;  | Запустите пакетной оценки на бессерверных вычислений. Поддерживает нормальные и низкоприоритетные виртуальные машины. |
| [Edge Интернета вещей Azure](../articles/machine-learning/service/how-to-deploy-and-where.md#iotedge) | (Предварительная версия) IoT&nbsp;модуля |  &nbsp; | &nbsp; | Развертывание и обслуживать моделей машинного Обучения на устройствах Интернета вещей. |
| [Что представляет собой Azure Data Box Edge (ознакомительная версия)?](../articles/databox-online/data-box-edge-overview.md)   | с помощью Edge Интернета вещей |  &nbsp; | Да | Развертывание и обслуживать моделей машинного Обучения на устройствах Интернета вещей. |
