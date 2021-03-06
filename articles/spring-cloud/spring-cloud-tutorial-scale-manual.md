---
title: Руководство по масштабированию приложения в Azure Spring Cloud | Документация Майкрософт
description: В этом руководстве описано, как масштабировать приложение в Azure Spring Cloud на портал Azure.
ms.service: spring-cloud
ms.topic: tutorial
ms.author: jeconnoc
author: jpconnock
ms.date: 10/06/2019
ms.openlocfilehash: cce7562c74577f6fd545bcaed3ee3e0968fd40b4
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132885"
---
# <a name="tutorial-scale-an-application-in-azure-spring-cloud"></a>Руководство по масштабированию приложения в Azure Spring Cloud

В этом руководстве описано, как масштабировать любое приложение для микрослужб с помощью панели мониторинга Azure Spring Cloud на портале Azure. Вы можете масштабировать приложение, изменяя число виртуальных ЦП и объем памяти или изменения число экземпляров приложения. По завершении вы узнаете, как выполнять быстро настроить каждое приложение в службе вручную. Масштабирование вступает в силу мгновенно и не требует никакого изменения кода или повторного развертывания.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим учебником необходимы указанные ниже компоненты.
* Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу. 
* Развернутый экземпляр службы Azure Spring Cloud.  Чтобы приступить к работе, ознакомьтесь с [этим руководством](spring-cloud-quickstart-launch-app-cli.md).
* По крайней мере одно приложение, ранее созданное в этом экземпляре службы.


## <a name="navigate-to-the-scale-page-in-the-azure-portal"></a>Переход на страницу масштабирования на портале Azure

1. Войдите на [портал Azure](https://portal.azure.com).

1. Перейдите на страницу **обзора** Azure Spring Cloud.

1. Выберите группу ресурсов, которая содержит службу.

1. Перейдите на вкладку **Приложения** под заголовком **Параметры** в меню с левой стороны.

1. Выберите приложение, которое требуется масштабировать. В этом примере мы будем масштабировать приложение с именем account-service. После этого вы перейдете на страницу **обзора** приложения.

1. Перейдите на вкладку **Масштаб** под заголовком **Параметры** в меню с левой стороны. Вы увидите параметры для атрибутов масштабирования, показанные в следующем разделе.

## <a name="scale-your-application"></a>Масштабирование приложения

Вы можете изменять атрибуты масштабирования. Учитывайте приведенные ниже замечания.

* **ЦП.** Максимально допустимое количество ЦП на экземпляр приложения составляет 4. Общее число ЦП приложения будет равно заданному здесь значению, умноженному на число экземпляров приложения.

* **Память (ГБ).** Максимально допустимый объем памяти на экземпляр приложения составляет 8 ГБ.  Общий объем памяти приложения будет равен заданному здесь значению, умноженному на число экземпляров приложения.

* **Число экземпляров приложения**. На уровне "Стандартный" масштабировать можно до 20 экземпляров. Это значение изменяет количество отдельных работающих экземпляров приложений для микрослужб.

Не забудьте нажать кнопку **Сохранить**, чтобы применить параметры масштабирования.

![Масштабирование службы на портале Azure](media/spring-cloud-tutorial-scale-manual/scale-up-out.png)

Через несколько секунд внесенные изменения будут отображаться на странице **Обзор** с дополнительными сведениями на вкладке **Экземпляры приложения**. В процессе масштабирования не требуется вносить изменения в код или выполнять повторное развертывание.

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как вручную масштабировать приложения Azure Spring Cloud.  Чтобы узнать, как выполнять мониторинг приложения, перейдите к следующему руководству.

> [!div class="nextstepaction"]
> [Мониторинг приложения](spring-cloud-tutorial-distributed-tracing.md)
