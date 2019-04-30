---
title: включение файла
description: включение файла
services: virtual-machines-windows, virtual-machines-linux
author: rockboyfor
ms.service: multiple
ms.topic: include
origin.date: 10/09/2018
ms.date: 11/26/2018
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 4d2235eaea457c89d01a632afa5dd5a862bec344
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60880563"
---
## <a name="deploy-an-image-with-marketplace-terms"></a>Развертывание образа на условиях Marketplace

Для некоторых образов виртуальных машин в Azure Marketplace применяются дополнительные условия лицензии и приобретения, которые нужно принять перед программным развертыванием.  

Чтобы развернуть виртуальную машину из такого образа, необходимо принять условия, на которых предоставляется образ, и включить программное развертывание. Это нужно сделать для подписки только один раз. Затем при каждом программном развертывании виртуальной машины из образа также необходимо указывать параметры *плана приобретения*.

В следующих разделах показано, как выполнить следующие задачи:

* проверить, есть ли дополнительные условия лицензии для образа Marketplace; 
* принять условия программными средствами;
* указать параметры плана приобретения при программном развертывании виртуальной машины.

<!-- Update_Description: update meta properties, wording update -->