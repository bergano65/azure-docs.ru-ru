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
ms.openlocfilehash: 1bb5d979ff7c9fe6e2afec4c7ebd81649b4488e3
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73581236"
---
# <a name="connect-to-your-virtual-machines-through-a-browser"></a>Подключение к виртуальным машинам через браузер 

DevTest Labs интегрируется с [Azure бастиона](https://docs.microsoft.com/azure/bastion/), что позволяет подключаться к виртуальным машинам через браузер. Сведения о том, как включить эту функцию в DevTest Labs, см. в статье [Включение подключения браузера на виртуальных машинах лаборатории](enable-browser-connection-lab-virtual-machines.md).

После включения *подключения к браузеру* пользователи лаборатории смогут получать доступ к виртуальным машинам через браузер.  


## <a name="create-a-lab-virtual-machine"></a>Создание виртуальной машины лаборатории

Сначала необходимо создать виртуальную машину лаборатории в виртуальной сети, на которой настроен бастиона. Вы можете выбрать виртуальную сеть во время создания виртуальной машины, перейдя на вкладку **Дополнительные параметры** .

![Создание виртуальной машины](./media/connect-virtual-machine-through-browser/create-virtual-machine.png)

## <a name="launch-virtual-machine-in-a-browser"></a>Запуск виртуальной машины в браузере

После создания виртуальной машины ее можно запустить в браузере, нажав кнопку *подключить к браузеру* и введя имя пользователя и пароль для компьютера.  

![Запуск в браузере](./media/connect-virtual-machine-through-browser/browser-connect.png)

## <a name="next-steps"></a>Дальнейшие действия

[Добавление виртуальной машины в лабораторию в Azure DevTest Labs](devtest-lab-add-vm.md)
