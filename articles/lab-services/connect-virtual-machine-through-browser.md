---
title: Подключение к виртуальным машинам через браузер — Azure | Документация Майкрософт
description: Узнайте, как подключиться к виртуальным машинам через браузер.
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
ms.date: 08/19/2019
ms.author: takamath
ms.openlocfilehash: 22d7afa889994068c3f906d0b9adbc1a74bc4839
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642595"
---
# <a name="connect-to-your-virtual-machines-through-a-browser"></a>Подключение к виртуальным машинам через браузер 

DevTest Labs интегрируется с [Azure бастиона](https://docs.microsoft.com/azure/bastion/), что позволяет подключаться к виртуальным машинам через браузер. Сведения о том, как включить эту функцию в DevTest Labs, см. в статье [Включение подключения браузера на виртуальных машинах лаборатории](enable-browser-connection-lab-virtual-machines.md).

После включения *подключения* к браузеру пользователи лаборатории смогут получать доступ к виртуальным машинам через браузер.  

> [!NOTE]
> Включение подключения к браузеру на виртуальных машинах лаборатории выполняется в предварительной версии.

## <a name="create-a-lab-virtual-machine"></a>Создание виртуальной машины лаборатории

Сначала необходимо создать виртуальную машину лаборатории в виртуальной сети, на которой настроен бастиона. Вы можете выбрать виртуальную сеть во время создания виртуальной машины, перейдя на вкладку **Дополнительные параметры** .

![Создание виртуальной машины](./media/connect-virtual-machine-through-browser/create-virtual-machine.png)

## <a name="launch-virtual-machine-in-a-browser"></a>Запуск виртуальной машины в браузере

После создания виртуальной машины ее можно запустить в браузере, нажав кнопку *подключить к браузеру* и введя имя пользователя и пароль для компьютера.  

![Запуск в браузере](./media/connect-virtual-machine-through-browser/browser-connect.png)

## <a name="next-steps"></a>Следующие шаги

[Добавление виртуальной машины в лабораторию в Azure DevTest Labs](devtest-lab-add-vm.md)