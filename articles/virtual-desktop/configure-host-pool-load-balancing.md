---
title: Настройка балансировки нагрузки виртуальных рабочих столов Windows в Azure
description: Настройка метода балансировки нагрузки для среды виртуальных рабочих столов Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: 61c11e342f3b4f906b453e0962018a8f8c34acd4
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73605874"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>Настройка метода балансировки нагрузки Виртуального рабочего стола Windows

Настройка метода балансировки нагрузки для пула узлов позволяет настроить среду виртуальных рабочих столов Windows в соответствии с вашими потребностями.

>[!NOTE]
> Это не относится к пулу узлов постоянного рабочего стола, так как пользователи всегда имеют сопоставление 1:1 с узлом сеанса в пуле узлов.

## <a name="configure-breadth-first-load-balancing"></a>Настройка балансировки нагрузки по ширине в ширину

Балансировка нагрузки по ширине по умолчанию используется для новых непостоянных пулов узлов. Балансировка нагрузки по горизонтали распределяет новые пользовательские сеансы по всем доступным узлам сеансов в пуле узлов. При настройке балансировки нагрузки по ширине в ширину можно задать максимальное число сеансов для каждого узла сеанса в пуле узлов.

Сначала [скачайте и импортируйте модуль PowerShell для Виртуального рабочего стола Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) для использования в сеансе PowerShell (если вы еще это не сделали). После этого выполните следующий командлет, чтобы войти в учетную запись:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Чтобы настроить пул узлов для выполнения балансировки нагрузки с максимальной нагрузкой без настройки максимального ограничения сеанса, выполните следующий командлет PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer
```

Чтобы настроить пул узлов для выполнения балансировки нагрузки по ширине в ширину и использовать новое ограничение максимального числа сеансов, выполните следующий командлет PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>Настройка балансировки нагрузки по глубине

Балансировка нагрузки с глубиной первого уровня распространяет новые пользовательские сеансы на доступный узел сеансов с наибольшим числом подключений, но не достигло порога максимального числа сеансов. При настройке балансировки нагрузки с глубиной глубины **необходимо** установить максимальное ограничение сеанса для каждого узла сеанса в пуле узлов.

Чтобы настроить пул узлов для выполнения балансировки нагрузки с глубиной глубины, выполните следующий командлет PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -DepthFirstLoadBalancer -MaxSessionLimit ###
```
