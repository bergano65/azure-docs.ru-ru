---
title: Доступ к решению Azure VMware от CloudSimple - Портал
description: Сведения о том, как получить доступ к решению VMware с помощью портала CloudSimple на портале Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 95d27bab243f6805436465f5027e519d33e20f6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244736"
---
# <a name="access-the-vmware-solution-by-cloudsimple-portal-from-the-azure-portal"></a>Доступ к решению VMware с помощью портала CloudSimple с портала Azure

Единый вход поддерживается для доступа к порталу CloudSimple. После входиный вход на портал Azure можно получить доступ к порталу CloudSimple без повторного вххастыва. При первом доступе к порталу CloudSimple, который вы получаете, авторизуем приложение [для авторизации облачных служб.](#consent-to-cloudsimple-service-authorization-application)  Авторизация является одноразовой акцией.

## <a name="before-you-begin"></a>Перед началом

Пользователи со встроенными ролями **владельца** и **вкладчика** могут получить доступ к порталу CloudSimple.  Роли должны быть настроены на группу ресурсов, в которой развернут сервис CloudSimple.  Роли также могут быть настроены на объект службы CloudSimple.  Для получения дополнительной информации [View role assignments](https://docs.microsoft.com/azure/role-based-access-control/check-access) о проверке роли см. Доступ к порталу CloudSimple могут получить только пользователи со встроенными ролями **владельца** и **автора.**  Роли должны быть настроены по подписке.  Для получения дополнительной информации [View role assignments](https://docs.microsoft.com/azure/role-based-access-control/check-access) о проверке роли см.

Если вы используете пользовательские роли, роль должна ```Actions```иметь любую из следующих операций под .  Для получения дополнительной информации [Custom roles for Azure resources](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)о пользовательских ролях см.  Если какая-либо из операций является частью, ```NotActions```пользователь не может получить доступ к порталу CloudSimple.

```
Microsoft.VMwareCloudSimple/*
Microsoft.VMwareCloudSimple/*/write
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*/write
```

## <a name="sign-in-to-azure"></a>Вход в Azure

Войти на портал Azure по адресу [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Доступ к порталу CloudSimple

1. Выберите **все услуги**.

2. Поиск **облачных сервисов CloudSimple**.

3. Выберите сервис CloudSimple, на котором вы хотите создать частное облако.

4. На странице **Обзор** нажмите **Перейдите на портал CloudSimple**.  Если вы впервые получаете доступ к порталу CloudSimple с портала Azure, вам будет предложено авторизовать приложение [авторизации службы CloudSimple.](#consent-to-cloudsimple-service-authorization-application) 

    ![Запуск портала CloudSimple](media/launch-cloudsimple-portal.png)

> [!NOTE]
> Если вы выберете операцию Private Cloud (например, создание или расширение частного облака) непосредственно с портала Azure, портал CloudSimple открывается на указанную страницу.

На портале CloudSimple выберите **меню «Дом** в стороннем меню», чтобы отображать краткую информацию о ваших частных облаках. Отображаются ресурсы и емкость ваших частных облаков, а также оповещения и задачи, требующие внимания. Для выполнения общих задач нажмите на именованные значки в верхней части страницы.

![Домашняя страница](media/cloudsimple-portal-home.png)

## <a name="consent-to-cloudsimple-service-authorization-application"></a>Согласие на приложение авторизации облачных услуг

Для впервые для запуска портала CloudSimple с портала Azure требуется ваше согласие на создание приложения для авторизации сервисов CloudSimple.  Выберите **Принять** для предоставления запрошенных разрешений и получить доступ к порталу CloudSimple.

![Согласие на авторизацию облачных сервисов - администраторы](media/cloudsimple-azure-consent.png)

Если у вас есть глобальная привилегия администратора, вы можете дать согласие на организацию.  Выберите **Согласие от имени вашей организации**.

![Согласие на авторизацию облачных сервисов - глобальный админ](media/cloudsimple-azure-consent-global-admin.png)

Если ваши разрешения не разрешают доступ к порталу CloudSimple, свяжитесь с глобальным администратором арендатора, чтобы предоставить необходимые разрешения.  Глобальный администратор может дать согласие от имени вашей организации.

![Согласие на авторизацию облачных сервисов - требует администраторов](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте, как [создать частное облако](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Узнайте, как [настроить среду частного облака](quickstart-create-private-cloud.md)
