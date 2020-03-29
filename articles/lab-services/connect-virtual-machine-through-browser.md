---
title: Подключайтесь к виртуальным машинам через браузер - Azure Документы Майкрософт
description: Узнайте, как подключиться к вашим виртуальным машинам через браузер.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2019
ms.author: takamath
ms.openlocfilehash: 4549ee0f50c06040b70307c96b5ca96cd4162214
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974307"
---
# <a name="connect-to-your-virtual-machines-through-a-browser"></a>Подключение к виртуальным машинам через браузер 

DevTest Labs интегрируется с [Azure Bastion,](https://docs.microsoft.com/azure/bastion/)который позволяет подключаться к вашим виртуальным машинам через браузер. Для получения информации о том, как включить эту функцию в DevTest Labs, [см.](enable-browser-connection-lab-virtual-machines.md)

После включения *подключения браузера* пользователи лаборатории могут получить доступ к виртуальным машинам через браузер.  

## <a name="create-a-lab-virtual-machine"></a>Создание лабораторной виртуальной машины

Сначала необходимо создать виртуальную машину лаборатории в виртуальной сети, на которую настраивается Bastion. Выберите вторую **созданную подсеть,** а не AzureBastionSubnet. Вы можете выбрать виртуальную сеть во время создания виртуальной машины, перейдя на вкладку **Расширенные настройки.**

![Создание виртуальной машины](./media/connect-virtual-machine-through-browser/create-virtual-machine.png)

## <a name="launch-virtual-machine-in-a-browser"></a>Запуск виртуальной машины в браузере

После создания виртуальной машины вы можете запустить ее в браузере, нажав кнопку *подключения браузера* и введя имя пользователя и пароль для машины.  

![Запуск в браузере](./media/connect-virtual-machine-through-browser/browser-connect.png)

## <a name="next-steps"></a>Next Steps

[Добавление виртуальной машины в лабораторию в Azure DevTest Labs](devtest-lab-add-vm.md)
