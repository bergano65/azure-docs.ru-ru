---
title: Сбросить пароли для vMs-компьютеров в лаборатории Класса в Лабораторных службах Azure (ru) Документы Майкрософт
description: Узнайте, как сбросить пароли для виртуальных машин (VM) в классных лабораториях Службы лаборатории Azure.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: 7c757ef8508f9364a46116e6ddf19207f23a4b6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73583701"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs-students"></a>Установка или сбросить пароль для виртуальных машин в классных лабораториях (студенты)
В этой статье показано, как студенты могут установить/сбросить пароль для своих vMs. 

## <a name="enable-resetting-of-passwords"></a>Включить сброс паролей
На момент создания лаборатории владелец лаборатории может включить или отключить **один и тот же пароль для всех виртуальных машин.** Если эта опция была включена, студенты не могут сбросить пароль. Все вдыхать в лабораториях будут иметь тот же пароль, который установлен инструктором. 

Если эта опция отключена, пользователи должны будут установить пароль при попытке подключения к VM в первый раз. Студенты также могут сбросить пароль позже в любое время, как показано в последнем разделе этой статьи. 

## <a name="reset-password-for-the-first-time"></a>Сбросить пароль впервый раз
Если один и **тот же пароль для всех виртуальных машин** был отключен, когда пользователи (студенты) выбирают кнопку **Connect** на лабораторной плитке на странице My **Virtual Machines,** пользователь видит следующее поле диалога, чтобы установить пароль для VM: 

![Сбросить пароль для студента](../media/how-to-set-virtual-machine-passwords/student-set-password.png)

## <a name="reset-password-later"></a>Сбросить пароль позже
Студент также может установить пароль, нажав меню переполнения **(вертикальные три точки)** на лабораторной плитке, и выбрав **пароль сна.** 

![Сбросить пароль позже](../media/how-to-set-virtual-machine-passwords/student-set-password-2.png)


## <a name="next-steps"></a>Дальнейшие действия
Чтобы узнать о других вариантах использования учащимися, которые [Configure student usage](how-to-configure-student-usage.md)владелец лаборатории может настроить, см.
