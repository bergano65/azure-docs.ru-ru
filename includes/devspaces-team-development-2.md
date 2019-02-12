---
title: включение файла
description: включение файла
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: include
manager: yuvalm
ms.openlocfilehash: 749120446619bf682d02be0f9290a6d47540c16a
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/02/2019
ms.locfileid: "55664399"
---
### <a name="run-the-service"></a>Запуск службы

1. Чтобы запустить службу, нажмите клавишу F5 (или введите `azds up` в окне терминала). Служба автоматически запустится в выбранной среде _dev/scott_. 
1. Убедитесь, что служба запущена в своей среде, еще раз выполнив команду `azds list-up`. Вы увидите, что экземпляр *mywebapi* теперь выполняется в среде _dev/scott_ (версия, запущенная в _dev_, по-прежнему выполняется, но не отображается).

    ```
    Name                      DevSpace  Type     Updated  Status
    mywebapi                  scott     Service  3m ago   Running
    mywebapi-bb4f4ddd8-sbfcs  scott     Pod      3m ago   Running
    webfrontend               dev       Service  26m ago  Running
    ```

1. Запустите команду `azds list-uris` и обратите внимание на URL-адрес точки доступа для *webfrontend*.

    ```
    Uri                                                                        Status
    -------------------------------------------------------------------------  ---------
    http://localhost:53831 => mywebapi.scott:80                                Tunneled
    http://dev.s.dev.webfrontend.6364744826e042319629.canadaeast.aksapp.io/  Available
    ```

1. Вместо "dev.s." в качестве префикса URL-адреса введите "scott.s." в веб-браузере. Обратите внимание, что этот обновленный URL-адрес по-прежнему разрешается. Этот URL-адрес является уникальным для среды _dev/scott_. Он указывает на то, что запросы, отправленные на URL-адрес Scott, сначала будут направляться к службам в среде _dev/scott_, а при сбое возвратятся к службам в среде _dev_.

<!--
TODO: replace 2 & 3 with below once bug#753164 and PR#158827 get pushed to production.

You can confirm that your service is running in its own space by running `azds list-up` again. First, you'll notice an instance of *mywebapi* is now running in the _dev/scott_ space (the version running in _dev_ is still running but it is not listed). If you run `azds list-uris`, you will notice that the access point URL for *webfrontend* is prefixed with the text "scott.s.". This URL is unique to the _dev/scott_ space. The special URL signifies that requests sent to the "Scott URL" will try to first route to services in the _dev/scott_ space, but if that fails, they will fall back to services in the _dev_ space.

```
Name                      DevSpace  Type     Updated  Status
mywebapi                  scott     Service  3m ago   Running
mywebapi-bb4f4ddd8-sbfcs  scott     Pod      3m ago   Running
webfrontend               dev       Service  26m ago  Running
```

```
Uri                                                                        Status
-------------------------------------------------------------------------  ---------
http://localhost:53831 => mywebapi.scott:80                                Tunneled
http://scott.s.dev.webfrontend.6364744826e042319629.canadaeast.aksapp.io/  Available
```
-->

![](../articles/dev-spaces/media/common/space-routing.png)

Эта встроенная возможность Azure Dev Spaces позволяет выполнять тестирование кода в общей среде, не требуя от каждого разработчика повторно создавать множество служб в своем пространстве. Для такой маршрутизации требуется, чтобы код приложения отправлял заголовки распространения, как показано на предыдущем шаге этого руководства.

### <a name="test-code-in-a-space"></a>Проверка кода в среде
Чтобы проверить свою новую версию *mywebapi* в сочетании с *webfrontend*, откройте в браузере URL-адрес общедоступной точки доступа для *webfrontend* и перейдите на страницу About (Сведения). Отобразится новое сообщение.

Теперь удалите часть "scott.s" из URL-адреса и обновите браузер. Снова будет наблюдаться старое поведение (версия *mywebapi*, запущенная в _dev_).

При наличии среды _dev_, которая всегда содержит последние изменения, и при условии, что приложение настроено для использования маршрутизации на основе среды DevSpace, как описано в разделе этого руководства, станет понятнее, как Dev Spaces может быть очень полезной при тестировании новых функций в контексте более крупного приложения. Вместо того чтобы развернуть _все_ службы в частной среде, вы можете создать частную среду, которая является производной от среды _dev_ и будет включать только те службы, с которыми вы фактически работаете. Инфраструктура маршрутизации Dev Spaces будет обрабатывать все остальное за счет использования всех доступных служб частной среды и по умолчанию применять последнюю версию, выполняемую в среде _dev_. Тем не менее _несколько_ разработчиков могут одновременно активно создавать разные службы в собственной среде, не нарушая работу друг друга.

### <a name="well-done"></a>Все готово!
Вы выполнили руководство по началу работы. Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * Настройка Azure Dev Spaces с помощью управляемого кластера Kubernetes в Azure.
> * итеративная разработка кода в контейнерах;
> * независимая разработка двух отдельных служб и вызов другой службы с помощью обнаружения службы DNS Kubernetes;
> * эффективная разработка и тестирование кода в среде командной работы.
> * определение базовых функций с помощью Dev Spaces для простой проверки отдельных изменений в контексте более крупного приложения для микрослужб.

Теперь, после ознакомления с Azure Dev Spaces, [предоставьте доступ к пространству разработки своему сотруднику](../articles/dev-spaces/how-to/share-dev-spaces.md) и покажите ему, как просто работать вместе.

## <a name="clean-up"></a>Очистка
Чтобы полностью удалить экземпляр Azure Dev Spaces из кластера, включая все среды разработки и работающие службы, используйте команду `az aks remove-dev-spaces`. Не забывайте, что это действие необратимо. Позднее вы сможете снова добавить в кластер поддержку Azure Dev Spaces, но в этом случае вам придется начать работу с нуля. Прежние службы и среды не будут восстановлены.

С помощью приведенного ниже примера кода можно вывести список контроллеров Azure Dev Spaces в активной подписке, а затем удалить контроллер Azure Dev Spaces, связанный с кластером AKS myaks в группе ресурсов myaks-rg.

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```