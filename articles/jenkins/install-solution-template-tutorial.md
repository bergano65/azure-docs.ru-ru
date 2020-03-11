---
title: Учебник. Создание сервера Jenkins в Azure
description: В этом учебнике вы установите Jenkins на виртуальной машине Azure под управлением Linux на основе шаблона решения Jenkins и создадите образец приложения Java.
keywords: jenkins, azure, devops, portal, virtual machine, solution template
ms.topic: tutorial
ms.date: 03/03/2020
ms.openlocfilehash: 3fa8dcbcc0edcc987fa9c4e76fbdb9d7a2f3396c
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274533"
---
# <a name="tutorial-create-a-jenkins-server-on-an-azure-linux-vm"></a>Руководство по созданию сервера Jenkins на виртуальной машине Azure под управлением Linux. 

В этом учебнике описано, как установить [Jenkins](https://jenkins.io) со средствами и подключаемыми модулями, настроенными для работы с Azure, на виртуальной машине под управлением Ubuntu Linux. Мы создадим в Azure сервер Jenkins для сборки образца приложения Java из [GitHub](https://github.com).

> [!div class="checklist"]
> * Установить и настроить сервер Jenkins в Azure.
> * Вход в консоль Jenkins через туннель SSH
> * Создание универсального проекта
> * Компиляция кода и создание пакета для примера приложения

[!INCLUDE [jenkins-install-solution-template-include](../../includes/jenkins-install-solution-template-steps.md)]