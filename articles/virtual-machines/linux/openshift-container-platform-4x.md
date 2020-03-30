---
title: Развертывание контейнерной платформы OpenShift 4.x в Azure
description: Развертывание контейнерной платформы OpenShift 4.x в Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74035434"
---
# <a name="deploy-openshift-container-platform-4x-in-azure"></a>Развертывание контейнерной платформы OpenShift 4.x в Azure

Развертывание контейнерной платформы OpenShift (OCP) 4.2 теперь поддерживается в Azure с помощью модели инфраструктуры установки (IPI).  Целевая страница для попытки OpenShift 4 [try.openshift.com](https://try.openshift.com/). Чтобы установить OCP 4.2 в Azure, посетите страницу [менеджера кластера Red Hat OpenShift.](https://cloud.redhat.com/openshift/install/azure/installer-provisioned)  Для доступа к этому сайту требуются учетные данные Red Hat.


## <a name="notes"></a>Примечания 

 - Для установки и запуска OCP 4.x в Azure требуется директор службы Active Directory (AAD) (SP)
     - СП должно быть предоставлено разрешение API **application.ReadWrite.OwnedBy** для графика активного каталога Azure
     - Администратор AAD-арендатордолженец должен предоставить admin Consent для встыковки этого разрешения API
     - SP должны быть предоставлены роли **администратора contributor** и **user Access** в подписке
 - Модель установки OCP 4.x отличается от 3.x, и для развертывания OCP 4.x в Azure нет шаблонов управления ресурсами Azure
 - Если в процессе установки возникают проблемы, обратитесь в соответствующую компанию (Microsoft или Red Hat)

| Описание проблемы | Контактная точка |
|-------------------|---------------|
| Специфические проблемы Azure (AAD, SP, подписка Azure и т.д.)                              | Microsoft |
| Проблемы, связанные с OpenShift (сбои в установке/ ошибки, подписка Red Hat и т.д.) |  Red Hat  |




## <a name="next-steps"></a>Дальнейшие действия

- [Overview](https://docs.openshift.com) (Обзор)
