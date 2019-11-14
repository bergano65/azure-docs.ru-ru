---
title: Развертывание OpenShift контейнера платформы 4. x в Azure
description: Разверните OpenShift Container Platform 4. x в Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 213c02b76f822d134729ebc4c0e6bff40f62089f
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035434"
---
# <a name="deploy-openshift-container-platform-4x-in-azure"></a>Развертывание OpenShift контейнера платформы 4. x в Azure

Развертывание платформы контейнеров OpenShift (OCP) 4,2 теперь поддерживается в Azure с помощью модели подготовки к установке инфраструктуры (IPI) для установщика.  Целевая страница для попытки OpenShift 4 — [try.openshift.com](https://try.openshift.com/). Чтобы установить OCP 4,2 в Azure, посетите страницу [диспетчера кластеров Red Hat OpenShift](https://cloud.redhat.com/openshift/install/azure/installer-provisioned) .  Для доступа к этому сайту требуются учетные данные Red Hat.


## <a name="notes"></a>Примечания 

 - Для установки и запуска OCP 4. x в Azure требуется субъект-служба Azure Active Directory (AAD).
     - SP должно быть предоставлено разрешение API **Application. ReadWrite. овнедби** для Azure Active Directory Graph
     - Чтобы это разрешение API вступило в силу, администратор клиента AAD необходимо предоставить согласие администратора.
     - Пакету обновления должны быть предоставлены роли " **участник** " и " **администратор доступа пользователей** " к подписке
 - Модель установки для OCP 4. x отличается от версии 3. x, и отсутствуют шаблоны Azure Resource Manager, доступные для развертывания OCP 4. x в Azure.
 - Если во время установки возникают проблемы, обратитесь в соответствующую компанию (Майкрософт или Red Hat)

| Описание проблемы | Точка контакта |
|-------------------|---------------|
| Проблемы, связанные с Azure (AAD, SP, подписка Azure и т. д.)                              | Microsoft |
| Проблемы, связанные с OpenShift (сбои при установке и ошибки, подписка Red Hat и т. д.) |  Red Hat  |




## <a name="next-steps"></a>Дополнительная информация

- [Overview](https://docs.openshift.com) (Обзор)
