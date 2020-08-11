---
title: Диагностика загрузки Azure
description: Общие сведения о диагностике загрузки Azure и управляемой диагностике загрузки
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: e2ba5d909a3aa43921f52295d2f7216aac76bc32
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88067092"
---
# <a name="azure-boot-diagnostics"></a>Диагностика загрузки Azure

Диагностика загрузки — это функция отладки для виртуальных машин Azure, которая позволяет выполнять диагностику сбоев при загрузке ВИРТУАЛЬНЫХ машин. Диагностика загрузки позволяет пользователю наблюдать за состоянием виртуальной машины при загрузке, собирая данные последовательного журнала и снимки экрана.

## <a name="boot-diagnostics-view"></a>Представление диагностики загрузки
В колонке виртуальной машины параметр Диагностика загрузки находится в разделе *Поддержка и устранение неполадок* в портал Azure. При выборе диагностики загрузки будут отображаться снимок экрана и сведения о последовательных журналах. Последовательный журнал содержит сообщения ядра, и снимок экрана является моментальным снимком текущего состояния виртуальных машин. В зависимости от того, что виртуальная машина работает под управлением Windows или Linux, определяет, как будет выглядеть ожидаемый снимок экрана. Для Windows пользователи увидят фон рабочего стола и Linux, пользователи увидят запрос на вход.

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="Снимок экрана диагностики загрузки Linux":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="Снимок экрана системы диагностики загрузки Windows":::


## <a name="limitations"></a>Ограничения
- Диагностика загрузки доступна только для Azure Resource Manager виртуальных машин. 
- Диагностика загрузки не поддерживает учетные записи хранения класса Premium. Если для диагностики загрузки используется учетная запись хранения класса Premium, `StorageAccountTypeNotSupported` при запуске виртуальной машины будут появляться сообщения об ошибке. 

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о [последовательной консоли Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-overview) и об использовании диагностики загрузки для [устранения неполадок виртуальных машин в Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics).
