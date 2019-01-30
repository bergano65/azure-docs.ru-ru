---
title: Включение CI/CD с помощью подключаемого модуля Jenkins в Azure IoT Edge | Документация Майкрософт
description: Расширение Azure IoT Edge для Jenkins позволяет интегрировать задачи разработки и развертывания в имеющееся решение DevOps.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 016d1c5d389cf1b9e82194e9d273863da1138d2b
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54830040"
---
# <a name="integrate-azure-iot-edge-with-jenkins-pipelines"></a>Интеграция Azure IoT Edge с помощью конвейеров Jenkins

Azure IoT Edge имеет встроенную поддержку Azure DevOps и Azure DevOps Projects, а также предоставляет подключаемый модуль для расширения функциональных возможностей DevOps для Jenkins. [Jenkins](https://jenkins.io/) — это сервер автоматизации с открытым кодом, который использует подключаемые модули для поддержки многих типов проектов разработки и развертывания, включая IoT Edge. 

Подключаемый модуль Azure IoT Edge для Jenkins ориентирован на непрерывную интеграцию и непрерывное развертывание. Вы можете создать конвейер сборки и отправки, который создает модули и отправляет их образы контейнеров в реестр контейнеров. Затем создайте конвейер выпуска, который развертывает модули на устройствах IoT Edge. 

Прежде чем начать использовать подключаемый модуль IoT Edge для Jenkins, вам потребуется центр Интернета вещей в Azure и реестр контейнеров для хранения образов контейнера. Используйте субъект-службу Azure для предоставления участнику Jenkins разрешения для центра Интернета вещей, чтобы подключаемый модуль мог создавать развертывания для устройств IoT Edge. 

Если вы готовы начать работу, найдите сведения об установке и использовании для [подключаемого модуля Azure IoT Edge для Jenkins](https://plugins.jenkins.io/azure-iot-edge).