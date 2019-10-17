---
title: Развертывание OpenShift контейнера платформы 4. x в Azure | Документация Майкрософт
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
ms.openlocfilehash: 9f807823b1d0d8079c43b2ba0e074a1c8a91b458
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72392687"
---
# <a name="deploy-openshift-container-platform-4x-in-azure"></a>Развертывание OpenShift контейнера платформы 4. x в Azure

Развертывание платформы контейнеров OpenShift (OCP) 4,2 теперь поддерживается в Azure с помощью модели подготовки к установке инфраструктуры (IPI) для установщика.  Целевая страница для попытки OpenShift 4 — [try.openshift.com](https://try.openshift.com/). Чтобы установить OCP 4,2 в Azure, посетите страницу [диспетчера кластеров Red Hat OpenShift](https://cloud.redhat.com/openshift/install/azure/installer-provisioned) .  Для доступа к этому сайту требуются учетные данные Red Hat.


## <a name="notes"></a>Заметки 

 - Для установки и запуска OCP 4. x в Azure требуется субъект-служба Azure Active Directory (AAD).
     - SP должно быть предоставлено разрешение API **Application. ReadWrite. овнедби** для Azure Active Directory Graph
     - Чтобы это разрешение API вступило в силу, администратор клиента AAD необходимо предоставить согласие администратора.
     - Пакету обновления должны быть предоставлены роли " **участник** " и " **администратор доступа пользователей** " к подписке
 - Модель установки для OCP 4. x отличается от версии 3. x, и отсутствуют шаблоны Azure Resource Manager, доступные для развертывания OCP 4. x в Azure.
 - Если во время установки возникают проблемы, обратитесь в соответствующую компанию (Майкрософт или Red Hat)

| Описание проблемы | Точка контакта |
|-------------------|---------------|
| Проблемы, связанные с Azure (AAD, SP, подписка Azure и т. д.)                              | Майкрософт |
| Проблемы, связанные с OpenShift (сбои при установке и ошибки, подписка Red Hat и т. д.) |  Red Hat  |




## <a name="next-steps"></a>Дальнейшие действия

- [Overview](https://docs.openshift.com) (Обзор)
