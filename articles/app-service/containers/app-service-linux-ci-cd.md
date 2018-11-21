---
title: Непрерывное развертывание из реестра контейнеров Docker при помощи платформы "Веб-приложения для контейнеров" в Azur | Документация Майкрософт
description: Как настроить непрерывное развертывание из реестра контейнеров Docker на платформе "Веб-приложения для контейнеров".
keywords: azure app service, linux, docker, acr,oss
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2018
ms.author: msangapu;yili
ms.openlocfilehash: b26366edddc223b842cc5d38473bda42422f1840
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2018
ms.locfileid: "51298547"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Непрерывное развертывание с использованием платформы Azure "Веб-приложения для контейнеров"

В этом руководстве описывается настройка непрерывного развертывания для настраиваемого образа контейнера из управляемых репозиториев [реестра контейнеров Azure](https://azure.microsoft.com/services/container-registry/) или [Docker Hub](https://hub.docker.com).

## <a name="enable-continuous-deployment-with-acr"></a>Включение непрерывного развертывания с помощью ACR

![Снимок экрана с веб-перехватчиком ACR](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-02.png)

1. Войдите на [портале Azure](https://portal.azure.com).
2. Выберите **Служба приложений** в левой части страницы.
3. Выберите имя приложения, для которого следует настроить непрерывное развертывание.
4. На странице **Параметры контейнера** выберите **Один контейнер**.
5. Выберите **Реестр контейнеров Azure**.
6. Выберите **Непрерывное развертывание > Вкл.**
7. Нажмите кнопку **Сохранить**, чтобы включить непрерывное развертывание.

## <a name="use-the-acr-webhook"></a>Использование веб-перехватчика ACR

После включения непрерывного развертывания можно просмотреть созданный веб-перехватчик на странице веб-перехватчиков Реестра контейнеров Azure.

![Снимок экрана с веб-перехватчиком ACR](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-03.png)

В Реестре контейнеров щелкните "Веб-перехватчики", чтобы просмотреть текущие веб-перехватчики.

## <a name="enable-continuous-deployment-with-docker-hub-optional"></a>Включение непрерывного развертывания с помощью Docker Hub (необязательно)

1. Войдите на [портале Azure](https://portal.azure.com).
2. Выберите **Служба приложений** в левой части страницы.
3. Выберите имя приложения, для которого следует настроить непрерывное развертывание.
4. На странице **Параметры контейнера** выберите **Один контейнер**.
5. Выберите **Docker Hub**.
6. Выберите **Непрерывное развертывание > Вкл.**
7. Нажмите кнопку **Сохранить**, чтобы включить непрерывное развертывание.

![Снимок экрана с параметрами приложения](./media/app-service-webapp-service-linux-ci-cd/ci-cd-docker-02.png)

Скопируйте URL-адрес веб-перехватчика. Чтобы добавить веб-перехватчик для Docker Hub, ознакомьтесь со статьей о <a href="https://docs.docker.com/docker-hub/webhooks/" target="_blank">веб-перехватчиках для Docker Hub</a>.

## <a name="next-steps"></a>Дополнительная информация

* [Вводные сведения о службе приложений Azure на платформе Linux](./app-service-linux-intro.md)
* [Реестр контейнеров Azure](https://azure.microsoft.com/services/container-registry/)
* [Создание веб-приложения .NET Core в службе приложений на платформе Linux](quickstart-dotnetcore.md)
* [Создание веб-приложения Ruby в службе приложений на платформе Linux](quickstart-ruby.md)
* [Развертывание веб-приложения Docker или Go в Веб-приложении для контейнеров](quickstart-docker-go.md)
* [Служба приложений Azure на платформе Linux: вопросы и ответы](./app-service-linux-faq.md)
* [Управление веб-приложением для контейнеров с помощью Azure CLI](./app-service-linux-cli.md)
