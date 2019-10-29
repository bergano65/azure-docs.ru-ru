---
title: Настройка проверки подлинности подписчика веб-перехватчика IoT Edge службы "Сетка событий Azure" | Документация Майкрософт
description: Настройка проверки подлинности подписчика веб-перехватчика
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 97ed1e2ad84d895e9da0d96cd070e14acb46385d
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992487"
---
# <a name="configure-webhook-subscriber-authentication"></a>Настройка проверки подлинности подписчика веб-перехватчика

В этом разделе приведены примеры возможных конфигураций подписчиков веб-перехватчиков для модуля сетки событий. По умолчанию для подписчиков веб-перехватчика принимаются только конечные точки HTTPS. Модуль сетки событий будет отклонен, если подписчик представляет самозаверяющий сертификат.

## <a name="allow-only-https-subscriber"></a>Разрешить только подписчик HTTPS

```json
 {
  "Env": [
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=false"
  ]
}
 ```

## <a name="allow-https-subscriber-with-self-signed-certificate"></a>Разрешить подписчику HTTPS с самозаверяющим сертификатом

```json
 {
  "Env": [
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Задайте для свойства `outbound:webhook:allowUnknownCA` значение `true` только в тестовых средах, так как обычно используются самозаверяющие сертификаты. Для рабочих нагрузок рекомендуется установить для них значение **false**.

## <a name="allow-https-subscriber-but-skip-certificate-validation"></a>Разрешить подписчику HTTPS, но пропустить проверку сертификата

```json
 {
  "Env": [
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=true",
    "outbound:webhook:allowUnknownCA=false"
  ]
}
 ```

>[!NOTE]
>Задайте для свойства `outbound:webhook:skipServerCertValidation` значение `true` только в тестовых средах, так как вы можете не представлять сертификат, который должен пройти проверку подлинности. Для рабочих нагрузок рекомендуется установить для них значение **false** .

## <a name="allow-both-http-and-https-with-self-signed-certificates"></a>Разрешить как HTTP, так и HTTPS с самозаверяющими сертификатами

```json
 {
  "Env": [
    "outbound:webhook:httpsOnly=false",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Задайте для свойства `outbound:webhook:httpsOnly` значение `false` только в тестовых средах, так как может потребоваться сначала открыть подписчик HTTP. Для рабочих нагрузок рекомендуется установить для них значение **true** .
