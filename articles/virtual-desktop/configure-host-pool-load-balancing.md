---
title: Настройка Windows Виртуальный настольный балансировки - Azure
description: Как настроить метод балансировки нагрузки для среды Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5d8670994791e360f5e3b30b90b4bea5d55464b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128311"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>Настройка метода балансировки нагрузки Виртуального рабочего стола Windows

Настройка метода балансировки нагрузки для пула хоста позволяет настроить среду Windows Virtual Desktop, чтобы лучше соответствовать вашим потребностям.

>[!NOTE]
> Это не относится к постоянному пулу узлов рабочего стола, поскольку пользователи всегда имеют отображение 1:1 для хоста сеанса в пуле узлов.

## <a name="configure-breadth-first-load-balancing"></a>Настройка балансировки нагрузки с первой шириной

Балансировка нагрузки в первую очередь — это конфигурация по умолчанию для новых непостоянных пулов хостов. Балансировка нагрузки в ширину распределяет новые пользовательские сеансы во всех доступных хостах сеансов в пуле узлов. При настройке балансировки нагрузки с точки ввоза с первой шириной можно установить максимальный лимит сеанса на узла сеанса в пуле хоста.

Сначала [скачайте и импортируйте модуль PowerShell для Виртуального рабочего стола Windows](/powershell/windows-virtual-desktop/overview/) для использования в сеансе PowerShell (если вы еще это не сделали). После этого выполните следующий командлет, чтобы войти в учетную запись:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Для настройки пула хоста для балансировки нагрузки с максимальной нагрузкой без корректировки максимального лимита сеанса выполните следующий cmdlet PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer
```

Чтобы настроить пул хоста для балансировки нагрузки с максимальной шириной и использовать новый максимальный лимит сеанса, выполните следующий cmdlet PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>Настройка балансировки нагрузки с первой глубиной

Балансировка нагрузки первой глубины распределяет новые сеансы пользователей среди доступного хостена с наибольшим числом соединений, но не достигла максимального порога лимита сеанса. При настройке балансировки нагрузки первой глубины **необходимо** установить максимальный лимит сеанса на унитаз сеанса в пуле хоста.

Для настройки пула хоста для балансировки нагрузки с глубиной первой нагрузки выполните следующий смдлет PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -DepthFirstLoadBalancer -MaxSessionLimit ###
```
