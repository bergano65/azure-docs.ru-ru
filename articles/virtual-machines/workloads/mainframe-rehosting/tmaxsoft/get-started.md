---
title: Начало работы с TmaxSoft OpenFrame на виртуальных машинах Azure
description: Перехваивайте рабочие нагрузки IBM z/OS с помощью среды TmaxSoft OpenFrame на виртуальных машинах Azure (VM).
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: 408e0166e52af9efd3d4c64f1b29bddcfc1cca4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "61485543"
---
# <a name="get-started-with-tmaxsoft-openframe-on-azure"></a>Начало работы с TmaxSoft OpenFrame на Azure

Возьмите существующие ресурсы мэйнфрейма и переместите их в Microsoft Azure с помощью TmaxSoft OpenFrame. Это популярное решение для реhosting-хостинга создает среду эмуляции в Azure, что позволяет быстро мигрировать приложениям. Никакого переформатирования не требуется.

## <a name="openframe-rehosting-environment"></a>Среда реhosting-хостинга OpenFrame

Настройка среды OpenFrame в Azure для разработки, демонстраций, тестирования или производственных нагрузок. Как показано на следующем рисунке, OpenFrame включает в себя несколько компонентов, которые создают среду эмуляции мэйнфрейма в Azure. Например, онлайн-сервисы OpenFrame заменяют промежуточное программное обеспечение мэйнфрейма, такое как IBM Customer Information Control System (CICS). OpenFrame Batch с компонентом TJES заменяет подсистему IBM mainframe's Job Entry Entry (JES). 

![Процесс реhosting-хостинга OpenFrame](media/openframe-01.png)

> [!NOTE]
> Для запуска среды OpenFrame в Azure необходимо иметь действительную лицензию на продукт или пробную лицензию от TmaxSoft.

## <a name="openframe-components"></a>Компоненты OpenFrame

Следующие компоненты являются частью среды OpenFrame в Azure:

- **Инструменты миграции,** включая OFMiner, решение, которое анализирует ресурсы мэйнфреймов, а затем переносит их в Azure.
- **Компиляторы**, в том числе OFCOBOL, компилятор, который интерпретирует программы COBOL мейнфрейма; OFPLI, который интерпретирует программы PL/I мейнфрейма; и OFASM, компилятор, который интерпретирует программы сборки мейнфрейма.
- **Передние конечные** компоненты, включая Java Enterprise User Solution (JEUS), сервер веб-приложений, сертифицированный для Java Enterprise Edition 6.OFGW, и компонент шлюза OpenFrame, который обеспечивает 3270 слушателей.
- **Среда применения.** OpenFrame Base — это промежуточное программное обеспечение, которое управляет всей системой. OpenFrame Server Type C (OSC) заменяет промежуточное программное обеспечение мэйнфрейма и IBM CICS.
- **Реляционная база данных,** например, Tibero (показано), База данных Oracle, сервер Microsoft S'L, IBM Db2 или MyS'L. Приложения OpenFrame используют протокол Open Database Connectivity (ODBC) для связи с базой данных.
- **Безопасность** через TACF, модуль обслуживания, который контролирует доступ пользователей к системам и ресурсам. 
- **OFManager** — это решение, которое обеспечивает функции работы и управления OpenFrame в веб-среде.

![Архитектура OpenFrame](media/openframe-02.png)

## <a name="next-steps"></a>Дальнейшие действия

- [Установка TmaxSoft OpenFrame на Azure](./install-openframe-azure.md)
