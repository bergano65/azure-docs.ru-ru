---
title: Как установить IoT Edge на Kubernetes (ru) Документы Майкрософт
description: Узнайте о том, как установить IoT Edge на Kubernetes с помощью локальной кластерной среды разработки
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4b2068c3944f9e7616b0666c7bafcafc68ee0cd9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471291"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>Как установить IoT Edge на Kubernetes (Предварительный просмотр)

IoT Edge может интегрироваться с Kubernetes, используя его в качестве устойчивого, высокодоступного слоя инфраструктуры. Вот где эта поддержка вписывается в решение Высокого уровня IoT Edge:

![k8s интро](./media/how-to-install-iot-edge-kubernetes/kubernetes-model.png)

>[!TIP]
>Хорошая умственная модель для этой интеграции заключается в том, чтобы думать о Kubernetes как о другой рабочей среде, на которой могут работать приложения IoT Edge в дополнение к Linux и Windows.

## <a name="architecture"></a>Architecture 
На Kubernetes IoT Edge обеспечивает *определение пользовательских ресурсов* (CRD) для развертывания рабочей нагрузки края. Агент IoT Edge берет на себя роль *контроллера CRD,* который согласовывает желаемое состояние, управляемое облаком, с локальным состоянием кластера.

Срок службы модуля управляется планировщиком Kubernetes, который поддерживает доступность модуля и выбирает их размещение. IoT Edge управляет платформой приложения edge, работающей сверху, непрерывно согласовывая желаемое состояние, указанное в концентраторе IoT, с состоянием на кластере края. Модель приложения по-прежнему является знакомой моделью, основанной на модулях и маршрутах IoT Edge. Контроллер IoT Edge Agent выполняет *модель* приложения автоматического перевода IoT Edge для нативных конструкций Kubernetes, таких как стручки, развертывания, службы и т.д.

Вот диаграмма архитектуры высокого уровня:

![kubernetes арка](./media/how-to-install-iot-edge-kubernetes/publicpreview-refresh-kubernetes.png)

Каждый компонент развертывания края присваивается пространством имен Kubernetes, специфичным для устройства, что позволяет обмениваться одними и теми же кластерными ресурсами между несколькими устройствами края и их развертываниями.

>[!NOTE]
>IoT Edge на Kubernetes находится в [публичном предварительном просмотре](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="tutorials-and-references"></a>Учебники и рекомендации 

Пожалуйста, ознакомьтесь [с IoT Edge на Kubernetes предварительный просмотр документов мини-сайт](https://aka.ms/edgek8sdoc) для получения дополнительной информации, в том числе углубленные учебники и ссылки.
