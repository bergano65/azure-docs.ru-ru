---
title: Добавление пользовательского сертификата ЦС — служба управления API Azure | Документация Майкрософт
description: Узнайте, как добавить пользовательский сертификат ЦС в службе управления API Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/20/2018
ms.author: apimpm
ms.openlocfilehash: b05713fd8acb49b25dba04781c1049c9f05115fb
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52444701"
---
# <a name="how-to-add-a-custom-ca-certificate-in-azure-api-management"></a>Добавление пользовательского сертификата ЦС в службе управления API Azure

Служба управления API Azure позволяет выполнять установку сертификатов ЦС на компьютере внутри доверенных корневых и промежуточных хранилищ сертификатов. Эту функциональность следует использовать, если вашим службам требуется пользовательский сертификат ЦС.

В этом руководстве описывается, как на портале Azure управлять сертификатами в экземпляре службы управления API Azure.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="step1"> </a>Передача сертификата ЦС

![Добавление сертификатов ЦС](media/api-management-howto-ca-certificates/00.png)

Чтобы передать новый сертификат ЦС, выполните действия ниже. Если экземпляр службы управления API еще не создан, выполните инструкции из руководства [Создание экземпляра службы управления API Azure](get-started-create-service-instance.md).

1. На портале Azure перейдите к экземпляру службы управления API Azure.

2. В меню выберите **Сертификаты ЦС**.

3. Щелкните **+ Добавить**.  

    ![Добавление сертификатов ЦС](media/api-management-howto-ca-certificates/01.png)  

4. Перейдите к сертификату и выберите хранилище сертификатов. Нужен только открытый ключ, поэтому пароль не требуется.

    ![Добавление сертификатов ЦС](media/api-management-howto-ca-certificates/02.png)  

5. Выберите команду **Сохранить**. Это может занять несколько минут.

    ![Добавление сертификатов ЦС](media/api-management-howto-ca-certificates/03.png)  

> [!NOTE]
> Вы можете отправить сертификат ЦС с помощью команды PowerShell `New-AzureRmApiManagementSystemCertificate`.

## <a name="step1a"> </a>Удаление сертификата клиента

Чтобы удалить сертификат, щелкните контекстное меню **...** и выберите **Удалить** рядом с именем сертификата.

![Удаление сертификатов ЦС](media/api-management-howto-ca-certificates/04.png)  

[Upload a CA certificate]: #step1
[Delete a CA certificate]: #step1a