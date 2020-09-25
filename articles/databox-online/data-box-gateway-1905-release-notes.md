---
title: Заметки о выпуске Шлюз Azure Data Box 1905 | Документация Майкрософт
description: Описание критических открытых проблем и способов их устранения для Шлюз Azure Data Box 1905, в котором используется общедоступная версия.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 06/12/2019
ms.author: alkohli
ms.openlocfilehash: b97165f19ac07e5a783203ece1a28420f731b649
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91320682"
---
# <a name="azure-data-box-edge-and-azure-data-box-gateway-1905-release-notes"></a>Заметки о выпуске Azure Data Box Edge и Шлюз Azure Data Box 1905

## <a name="overview"></a>Обзор

В следующих заметках о выпуске указываются критические открытые проблемы и разрешенные проблемы для выпуска 1905 для Azure Data Box Edge и Шлюз Azure Data Box.

Заметки о выпуске постоянно обновляются: обнаруживаются и добавляются критические проблемы, требующие временного решения. Перед развертыванием Data Box Edge или Шлюз Data Box внимательно проверьте сведения, содержащиеся в заметках о выпуске. 

Этот выпуск соответствует версиям программного обеспечения:

- **Шлюз Data Box 1905 (1.6.887.626)**
- **Data Box Edge 1905 (1.6.887.626)**

> [!NOTE]
> Обновление 1905 можно применять только к устройствам Data Box Edge, на которых установлена общедоступная версия программного обеспечения.

## <a name="whats-new"></a>Новые возможности

- **Улучшения ведения журнала для программируемых массивов шлюзов (FPGA)** . в этом выпуске мы внесли улучшения в журналы и оповещения, связанные с FPGA. Это обязательное обновление для Data Box Edge, если в FPGA используется функция COMPUTE для граничных вычислений. Дополнительные сведения см. в разделе [Преобразование данных с помощью граничных вычислений на Data Box Edge](azure-stack-edge-deploy-configure-compute-advanced.md).

## <a name="known-issues-in-ga-release"></a>Известные проблемы в общедоступном выпуске

В этом выпуске не указаны новые проблемы. Все указанные в выпуске проблемы были перенесены из предыдущих выпусков. Чтобы просмотреть список известных проблем, перейдите к разделу [Известные проблемы в общедоступном выпуске](data-box-gateway-release-notes.md#known-issues-in-ga-release).


## <a name="next-steps"></a>Дальнейшие действия

- [Подготовка к развертыванию Шлюз Azure Data Box](data-box-gateway-deploy-prep.md)
- [Подготовка к развертыванию Azure Data Box Edge](azure-stack-edge-deploy-prep.md)
