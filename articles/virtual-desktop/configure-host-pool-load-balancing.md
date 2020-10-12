---
title: Настройка балансировки нагрузки виртуальных рабочих столов Windows в Azure
description: Настройка метода балансировки нагрузки для среды виртуальных рабочих столов Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 07eae73a36bf4051925547fa375f46963a162881
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88010112"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>Настройка метода балансировки нагрузки Виртуального рабочего стола Windows

Настройка метода балансировки нагрузки для пула узлов позволяет настроить среду виртуальных рабочих столов Windows в соответствии с вашими потребностями.

>[!NOTE]
> Это не относится к пулу узлов постоянного рабочего стола, так как пользователи всегда имеют сопоставление 1:1 с узлом сеанса в пуле узлов.

## <a name="prerequisites"></a>Предварительные требования

В этой статье предполагается, что вы использовали инструкции из раздела [Настройка модуля PowerShell для виртуальных рабочих столов Windows](powershell-module.md) для загрузки и установки модуля PowerShell и входа в учетную запись Azure.

## <a name="configure-breadth-first-load-balancing"></a>Настройка балансировки нагрузки по ширине в ширину

Балансировка нагрузки по ширине по умолчанию используется для новых непостоянных пулов узлов. Балансировка нагрузки по горизонтали распределяет новые пользовательские сеансы по всем доступным узлам сеансов в пуле узлов. При настройке балансировки нагрузки по ширине в ширину можно задать максимальное число сеансов для каждого узла сеанса в пуле узлов.

Чтобы настроить пул узлов для выполнения балансировки нагрузки с максимальной нагрузкой без настройки максимального ограничения сеанса, выполните следующий командлет PowerShell:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'BreadthFirst'
```

После этого, чтобы убедиться, что вы установили метод балансировки нагрузки по ширине, выполните следующий командлет:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, LoadBalancerType

Name             : hostpoolname
LoadBalancerType : BreadthFirst
```

Чтобы настроить пул узлов для выполнения балансировки нагрузки по ширине в ширину и использовать новое ограничение максимального числа сеансов, выполните следующий командлет PowerShell:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'BreadthFirst' -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>Настройка балансировки нагрузки по глубине

Балансировка нагрузки с глубиной первого уровня распространяет новые пользовательские сеансы на доступный узел сеансов с наибольшим числом подключений, но не достигло порога максимального числа сеансов. При настройке балансировки нагрузки с глубиной глубины необходимо установить максимальное ограничение сеанса для каждого узла сеанса в пуле узлов.

Чтобы настроить пул узлов для выполнения балансировки нагрузки с глубиной глубины, выполните следующий командлет PowerShell:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'DepthFirst' -MaxSessionLimit ###
```

Чтобы убедиться, что параметр обновлен, выполните следующий командлет:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, LoadBalancerType, MaxSessionLimit

Name             : hostpoolname
LoadBalancerType : DepthFirst
MaxSessionLimit  : 6
```

## <a name="configure-load-balancing-with-the-azure-portal"></a>Настройка балансировки нагрузки с помощью портал Azure

Балансировку нагрузки можно также настроить с помощью портал Azure.

Чтобы настроить балансировку нагрузки, выполните следующие действия.

1. Войдите на портал Azure по адресу https://portal.azure.com.
2. Найдите и выберите пункт **виртуальный рабочий стол Windows** в разделе службы.
3. На странице виртуальных рабочих столов Windows выберите **Пулы узлов**.
4. Выберите имя пула узлов, который нужно изменить.
5. Выберите **Свойства**.
6. Введите **Максимальное число сеансов** в поле и выберите в раскрывающемся меню необходимый **алгоритм балансировки нагрузки** для этого пула узлов.
7. Щелкните **Сохранить**. При этом применяются новые параметры балансировки нагрузки.