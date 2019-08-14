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
ms.openlocfilehash: c5a6f957cd3f799f9d7eff1df22df2200779ac30
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946038"
---
| Целевой объект вычисления | Использование | Поддержка GPU | Поддержка FPGA | Описание |
| ----- | ----- | ----- | ----- | ----- |
| [&nbsp;Локальная&nbsp;веб-служба](../articles/machine-learning/service/how-to-deploy-and-where.md#local) | Тестирование и отладка | Ну | &nbsp; | Хорошо подходит для ограниченного тестирования и устранения неполадок. Аппаратное ускорение зависит от использования библиотек в локальной системе.
| [Веб-&nbsp;&nbsp;служба виртуальной машины записных книжек](../articles/machine-learning/service/how-to-deploy-and-where.md#notebookvm) | Тестирование и отладка | Ну | &nbsp; | Хорошо подходит для ограниченного тестирования и устранения неполадок. 
| [Служба Azure Kubernetes (AKS)](../articles/machine-learning/service/how-to-deploy-and-where.md#aks) | Вывод в режиме реального времени |  [да](../articles/machine-learning/service/how-to-deploy-inferencing-gpus.md)  | [да](../articles/machine-learning/service/how-to-deploy-fpga-web-service.md)   |Подходит для крупномасштабных рабочих развертываний. Обеспечивает быстрое время отклика и автоматическое масштабирование развернутой службы. Автоматическое масштабирование кластера не поддерживается с помощью пакета SDK для Машинное обучение Azure. Чтобы изменить узлы в кластере AKS, используйте пользовательский интерфейс для кластера AKS в портал Azure. AKS — единственный параметр, доступный для визуального интерфейса. |
| [Экземпляры контейнеров Azure (ACI)](../articles/machine-learning/service/how-to-deploy-and-where.md#aci) | Тестирование или разработка | &nbsp;  | &nbsp; | Хорошо подходит для рабочих нагрузок с низким уровнем масштабирования и ЦП, для которых требуются < 48 ГБ ОЗУ |
| [Вычислительная среда Машинного обучения Azure](../articles/machine-learning/service/how-to-run-batch-predictions.md) | Образца Вывод&nbsp;пакета | да | &nbsp;  | Выполнение пакетной оценки на бессерверных вычислениях. Поддерживает виртуальные машины с обычным и низким приоритетными уровнями. |
| [Azure IoT Edge](../articles/machine-learning/service/how-to-deploy-and-where.md#iotedge) | Образца Модуль&nbsp;IOT |  &nbsp; | &nbsp; | Развертывание & обслуживание моделей машинного обучения на устройствах IoT. |
| [Что представляет собой Azure Data Box Edge (ознакомительная версия)?](../articles/databox-online/data-box-edge-overview.md)   | Via IoT Edge |  &nbsp; | да | Развертывание & обслуживание моделей машинного обучения на устройствах IoT. |
