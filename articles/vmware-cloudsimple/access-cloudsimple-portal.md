---
title: Решение Azure VMware для доступа по CloudSimple - портала
description: Описание способов доступа к VMware решение порталом CloudSimple с портала Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 30882899e5be4101ae3d77f9840d8bdef567e53f
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66676988"
---
# <a name="accessing-the-vmware-solution-by-cloudsimple-portal-from-azure-portal"></a>Доступ к VMware решения с портала CloudSimple с портала Azure

Единый вход поддерживается для доступа к порталу CloudSimple. После входа на портал Azure можно получить доступ к порталу CloudSimple без повторного входа систему. При первом доступе к порталу CloudSimple, вам будет предложено авторизовать [авторизации службы CloudSimple](#consent-to-cloudsimple-service-authorization-application) приложения.  Авторизация — это однократное действие.

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите на портал Azure по адресу [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Доступ к порталу CloudSimple

1. Выбор пункта **Все службы**.

2. Поиск **CloudSimple служб**.

3. Выберите службу CloudSimple, на котором вы хотите создать частным облаком.

4. На **Обзор** щелкните **перейдите на портал CloudSimple**.  Если вы обращаетесь CloudSimple портала с помощью портала Azure впервые, вам будет предложено авторизовать [авторизации службы CloudSimple](#consent-to-cloudsimple-service-authorization-application) приложения. 

    ![Запуск портала CloudSimple](media/launch-cloudsimple-portal.png)

> [!TIP]
> Если выбрать операцию частного облака (например, создание или развертывание частного облака) непосредственно с портала Azure, портал CloudSimple откроется указанной страницы.

На портале CloudSimple выберите **Главная** в боковом меню, чтобы отобразить сводные данные о вашей частных облаков. Ресурсы и емкость частного облаков отображаются вместе с оповещениями и задачи, требующие внимания. Для стандартных задач щелкните именованный значков в верхней части страницы.

![Домашняя страница](media/cloudsimple-portal-home.png)

## <a name="consent-to-cloudsimple-service-authorization-application"></a>Согласие на CloudSimple авторизации службы приложений

Запуск портала CloudSimple на портале Azure в первый раз требует вашего согласия для приложения CloudSimple авторизации в службе.  Выберите **Accept** для предоставления запрошенных разрешений и доступа к порталу CloudSimple. 

![Согласие на авторизации в службе CloudSimple - администраторов](media/cloudsimple-azure-consent.png)

Если у вас есть права глобального администратора, вы даете согласие для вашей организации.  Выберите **предоставлять согласие от лица организации**.

![Согласие на авторизации в службе CloudSimple - глобального администратора](media/cloudsimple-azure-consent-global-admin.png)

Если разрешение не разрешают доступ к порталу CloudSimple, обратитесь в службу глобального администратора вашего клиента, чтобы предоставить необходимые разрешения.  Глобальный администратор может предоставлять согласие от имени вашей организации.

![Согласие на авторизации службы CloudSimple — требует от администраторов](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте, как [Создание частного облака](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Узнайте, как [Настройка среды с частным облаком](quickstart-create-private-cloud.md)