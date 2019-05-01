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
ms.openlocfilehash: 59f5438bbedea2ff7793a5df95f1d3df58b9bba6
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/26/2019
ms.locfileid: "64576990"
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
  * Сброс виртуальной машины
* Удаление виртуальной Машины

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте, как [использование виртуальных машин VMware в Azure](quickstart-create-vmware-virtual-machine.md)
* Узнайте, как [сопоставить свою подписку Azure](https://docs.azure.cloudsimple.com/azuresubscriptionmapping/)