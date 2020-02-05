---
title: Доступ к решениям Azure VMware (AVS) — портал
description: В этой статье описывается, как получить доступ к решениям Azure VMware (AVS) из портал Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c0b0601b9d2cf505aa5b25e10875338352cab771
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77015956"
---
# <a name="access-azure-vmware-solutions-avs-from-the-azure-portal"></a>Доступ к решениям Azure VMware (AVS) из портал Azure

Единый вход поддерживается для доступа к порталу AVS. После входа в портал Azure можно получить доступ к порталу AVS без повторного входа. При первом обращении к порталу AVS вам будет предложено авторизовать приложение [авторизации службы AVS](#consent-to-avs-service-authorization-application) . Авторизация является однократным действием.

## <a name="before-you-begin"></a>Перед началом работы

Пользователи со встроенными ролями **владельца** и **участника** могут получить доступ к порталу AVS. Роли должны быть настроены в группе ресурсов, в которой развернута служба AVS. Роли также можно настроить для объекта службы AVS. Дополнительные сведения о проверке роли см. в статье [Просмотр назначений ролей](https://docs.microsoft.com/azure/role-based-access-control/check-access) . Только пользователи со встроенными ролями **владельца** и **участника** могут получить доступ к порталу AVS. Роли должны быть настроены в подписке. Дополнительные сведения о проверке роли см. в статье [Просмотр назначений ролей](https://docs.microsoft.com/azure/role-based-access-control/check-access) .

При использовании пользовательских ролей роль должна иметь одну из следующих операций в разделе ```Actions```.  Дополнительные сведения о пользовательских ролях см. в статье [пользовательские роли для ресурсов Azure](https://docs.microsoft.com/azure/role-based-access-control/custom-roles). Если какая-либо из операций входит в состав ```NotActions```, пользователь не может получить доступ к порталу AVS. 

```
Microsoft.VMwareCloudSimple/*
Microsoft.VMwareCloudSimple/*/write
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*/write
```

## <a name="sign-in-to-azure"></a>Войдите в Azure

Войдите на портал Azure по адресу [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-avs-portal"></a>Доступ к порталу AVS

1. Выбор пункта **Все службы**.

2. Поиск **служб AVS**.

3. Выберите службу AVS, в которой вы хотите создать частное облако.

4. На странице **Обзор** щелкните **Переход на портал AVS**. Если вы получаете доступ к порталу AVS с портал Azure в первый раз, вам будет предложено авторизовать приложение [авторизации службы AVS](#consent-to-avs-service-authorization-application) . 

    ![Запустить портал AVS](media/launch-cloudsimple-portal.png)

> [!NOTE]
> Если выбрать операцию частного облака (например, создать или развернуть частное облако) непосредственно из портал Azure, портал AVS откроется на указанной странице.

На портале AVS выберите **Главная** в боковом меню, чтобы отобразить сводную информацию о частном облаке AVS. Здесь показаны ресурсы и емкость частного облака AVS, а также предупреждения и задачи, требующие внимания. Для общих задач щелкните именованные значки в верхней части страницы.

![Домашняя страница](media/cloudsimple-portal-home.png)

## <a name="consent-to-avs-service-authorization-application"></a>Согласие на приложение авторизации службы AVS

Запуск портала AVS из портал Azure в первый раз требует вашего согласия для приложения авторизации службы AVS. Выберите **принять** , чтобы предоставить запрошенные разрешения и получить доступ к порталу AVS.

![Согласие на авторизацию службы AVS — администраторы](media/cloudsimple-azure-consent.png)

Если у вас есть права глобального администратора, вы можете предоставить согласие для вашей организации. Выберите **согласие от имени вашей организации**.

![Согласие на авторизацию службы AVS — глобальный администратор](media/cloudsimple-azure-consent-global-admin.png)

Если ваши разрешения не разрешать доступ к порталу AVS, обратитесь к глобальному администратору своего клиента, чтобы предоставить необходимые разрешения. Глобальный администратор может согласиться от имени вашей организации.

![Согласие на авторизацию службы AVS — требуются администраторы](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте, как [создать частное облако](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Узнайте, как [настроить среду частного облака](quickstart-create-private-cloud.md)
