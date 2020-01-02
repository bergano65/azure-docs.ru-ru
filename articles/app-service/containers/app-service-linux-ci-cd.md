---
title: CI/CD для пользовательских контейнеров Linux
description: Узнайте, как настроить непрерывное развертывание в пользовательском контейнере Linux в службе приложений Azure. Непрерывное развертывание поддерживается для DOCKER HUB и записи контроля доступа.
keywords: azure app service, linux, docker, acr,oss
author: msangapu-msft
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.topic: article
ms.date: 11/08/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: d43491de7500204ed470757a1b744017a8180b57
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74687634"
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

## <a name="next-steps"></a>Дальнейшие действия

* [Вводные сведения о службе приложений Azure на платформе Linux](./app-service-linux-intro.md)
* [Реестр контейнеров Azure](https://azure.microsoft.com/services/container-registry/)
* [Создание веб-приложения .NET Core в службе приложений на платформе Linux](quickstart-dotnetcore.md)
* [Создание веб-приложения Ruby в службе приложений на платформе Linux](quickstart-ruby.md)
* [Развертывание веб-приложения Docker или Go в Веб-приложении для контейнеров](quickstart-docker-go.md)
* [Служба приложений Azure на платформе Linux: вопросы и ответы](./app-service-linux-faq.md)
* [Управление веб-приложением для контейнеров с помощью Azure CLI](./app-service-linux-cli.md)
