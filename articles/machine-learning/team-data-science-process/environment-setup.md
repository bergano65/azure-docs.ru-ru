---
title: Настройка сред обработки и анализа данных в Azure | Документация Майкрософт
description: Настройка сред обработки и анализа данных в Azure, используемых в процессе обработки и анализа данных группы.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: 481cfa6a-7ea3-46ac-b0f9-2e3982c37153
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: deguhath
ms.openlocfilehash: 996c7b4332326ddf1ef18ca732677a85b6df6046
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2018
ms.locfileid: "51345986"
---
# <a name="set-up-data-science-environments-for-use-in-the-team-data-science-process"></a>Настройка сред обработки и анализа данных для использования в процессе обработки и анализа данных группы
Процесс обработки и анализа данных группы использует различные специализированные среды для хранения, обработки и анализа данных. Сюда относятся хранилище BLOB-объектов Azure, несколько типов виртуальных машин Azure, кластеры HDInsight (Hadoop) и рабочие области Машинного обучения Azure. Выбор используемой среды зависит от типа и количества данных, которые нужно моделировать, и места хранения этих данных в облаке. 

* Сведения о том, какие факторы нужно учесть при выборе, см. в статье [Как определить сценарии и план для расширенной аналитической обработки данных](plan-your-environment.md). 
* Каталог, содержащий несколько сценариев, возможных при выполнении расширенной аналитики, доступен в статье [Scenarios for the Team Data Science Process](plan-sample-scenarios.md)

В следующих статьях описывается настройка различных сред обработки и анализа данных, используемых процессом обработки и анализа данных группы.

* [Учетная запись службы хранилища Azure](../../storage/common/storage-quickstart-create-account.md)
* [Кластер HDInsight Hadoop](customize-hadoop-cluster.md)
* [Рабочая область Студии машинного обучения](../studio/create-workspace.md)

**Виртуальная машина Майкрософт для обработки и анализа данных (DSVM)** также доступна как образ виртуальной машины Azure. В этой виртуальной машине Azure предварительно установлен и настроен ряд распространенных средств, которые обычно используются для анализа данных и машинного обучения. DSVM доступна в Windows и Linux. Дополнительные сведения см. в статье [Введение в облачные виртуальные машины для анализа и обработки данных для Linux и Windows](../data-science-virtual-machine/overview.md).

Узнайте, как создавать следующие компоненты:

- [Виртуальная машина для обработки и анализа данных под управлением Windows](../data-science-virtual-machine/provision-vm.md)
- [Виртуальная машина для обработки и анализа данных Ubuntu](../data-science-virtual-machine/dsvm-ubuntu-intro.md)
- [Виртуальная машина для обработки и анализа данных CentOS](../data-science-virtual-machine/linux-dsvm-intro.md)
- [Виртуальная машина для глубокого обучения](../data-science-virtual-machine/provision-deep-learning-dsvm.md)
