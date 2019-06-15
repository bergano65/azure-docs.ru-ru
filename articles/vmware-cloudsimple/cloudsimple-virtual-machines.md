---
title: Решение VMware, CloudSimple - обзор виртуальных машин Azure
description: Дополнительные сведения о CloudSimple виртуальных машин и их преимущества.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 77a2901ae2d81f42780110b1576c1f32c7ff397b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65209488"
---
# <a name="cloudsimple-virtual-machines-overview"></a>Общие сведения о виртуальных машинах CloudSimple

CloudSimple позволяет управлять виртуальными машинами VMware с помощью портала Azure.  Кластер или пула ресурсов из кластера vSphere осуществляется через Azure, сопоставляя его к своей подписке.  CloudSimple виртуальной машины обеспечивает Самостоятельное управление виртуальных машин VMware на портале Azure.  

Чтобы создать виртуальную Машину CloudSimple из Azure, шаблона виртуальной Машины должен существовать на vCenter частного облака.  Этот шаблон используется для настройки операционной системы и приложений.  Шаблон виртуальной Машины могут быть защищены в соответствии с корпоративными политиками безопасности.  Шаблон можно использовать для создания виртуальных машин и использовать их из портала Azure, используя модель самообслуживания.

## <a name="benefits"></a>Преимущества

CloudSimple виртуальные машины на портале Azure предоставляют механизм самообслуживания для пользователей, для создания и управления виртуальными машинами VMware.

* Создание виртуальной Машины CloudSimple на vCenter частного облака
* Управление свойствами виртуальной Машины
  * Добавление и удаление дисков
  * Добавление или удаление сетевых адаптеров
* Операции Power CloudSimple виртуальной машины
  * Включение и выключение питания
  * Сброс виртуальной Машины
* Удаление виртуальной Машины

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте, как [использование виртуальных машин VMware в Azure](quickstart-create-vmware-virtual-machine.md)
* Узнайте, как [сопоставить свою подписку Azure](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)