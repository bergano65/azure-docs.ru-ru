---
title: Обзор виртуальных машин
titleSuffix: Azure VMware Solution by CloudSimple
description: Узнайте о виртуальных машинах CloudSimple и их преимуществах.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 436a5ab6fb286ce206f981487b6a29d774ecd1a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024914"
---
# <a name="cloudsimple-virtual-machines-overview"></a>Обзор виртуальных машин CloudSimple

CloudSimple позволяет управлять виртуальными машинами VMware (VM) с портала Azure.  Кластер или пул ресурсов из кластера vSphere управляются через Azure, отображая его в подписке.

Для создания Облачного VM из Azure в вашем private Cloud vCenter должен существовать vM-шаблон VM.  Шаблон используется для настройки операционной системы и приложений.  Шаблон VM можно затвердеть, чтобы соответствовать политикам корпоративной безопасности.  Шаблон можно использовать для создания вс-проектов, а затем использовать их с портала Azure с помощью модели самообслуживания.

## <a name="benefits"></a>Преимущества

Виртуальные машины CloudSimple с портала Azure обеспечивают пользователям механизм самообслуживания для создания и управления виртуальными машинами VMware.

* Создание CloudSimple VM на вашем частном облачном vCenter
* Управление свойствами VM
  * Добавление/удаление дисков
  * Добавить/удалить NICs
* Силовые работы вашего CloudSimple VM
  * Включаните электричество и отключать электричество
  * Сброс виртуальной машины
* Удаление виртуальной машины

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте, как [использовать VMware VMs на Azure](quickstart-create-vmware-virtual-machine.md)
* Узнайте, как [сопоставить подписку на Azure](azure-subscription-mapping.md)
