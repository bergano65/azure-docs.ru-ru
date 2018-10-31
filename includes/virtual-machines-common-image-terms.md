---
title: включение файла
description: включение файла
services: virtual-machines-windows, virtual-machines-linux
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 10/09/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 6c7a9857f6d6d57dc9e314bcb1ef848a326b7ed2
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/18/2018
ms.locfileid: "49437029"
---
## <a name="terminology"></a>Терминология

Атрибуты образа Marketplace в Azure:

* **Издатель.** Организация, создавшая образ. Примеры: Canonical, MicrosoftWindowsServer.
* **Предложение.** Имя группы связанных образов, созданных издателем. Примеры: Ubuntu Server, WindowsServer.
* **Номер SKU.** Экземпляр предложения, например основной выпуск дистрибутива. Примеры: 16.04-LTS, 2016-Datacenter.
* **Версия.** Номер версии SKU образа. 

Чтобы определить образ Marketplace при программном развертывании виртуальной машины, укажите эти значения как отдельные параметры. Некоторые средства принимают *URN* образа, который объединяет эти значения, разделенные двоеточием (:): *Издатель*:*Предложение*:*Номер SKU*:*Версия*. В URN номер версии можно заменить ключевым словом "latest". В этом случае будет выбрана последняя версия образа. 

Если у издателя образа есть дополнительные условия лицензии и приобретения, необходимо принять их, чтобы включить программное развертывание. Также при программном развертывании виртуальной машины нужно ввести параметры *плана приобретения*. См. дополнительные сведения о [развертывании образа на условиях Marketplace](#deploy-an-image-with-marketplace-terms).