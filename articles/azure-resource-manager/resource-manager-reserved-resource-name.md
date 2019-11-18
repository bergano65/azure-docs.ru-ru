---
title: Ошибки имени зарезервированного ресурса
description: Описывается, как устранить ошибки при предоставлении имени ресурса, который включает зарезервированное слово.
ms.topic: troubleshooting
ms.date: 11/08/2017
ms.openlocfilehash: e76f4bf9bfee7de6e7523d69acf1388d2dd80e93
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150452"
---
# <a name="resolve-reserved-resource-name-errors"></a>Ошибки имен зарезервированных ресурсов Azure

В этой статье описаны ошибки, которые возникают при развертывании ресурса, в имени которого содержится зарезервированное слово.

## <a name="symptom"></a>Симптом

При развертывании ресурсов, которые доступны через другую общедоступную конечную точку, может появиться следующая ошибка:

```
Code=ReservedResourceName;
Message=The resource name <resource-name> or a part of the name is a trademarked or reserved word.
```

## <a name="cause"></a>Причина:

Ресурсы, которые имеют общедоступную конечную точку, не могут содержать зарезервированные слова или товарные знаки в имени.

Следующие слова являются зарезервированными:

* ACCESS;
* AZURE;
* BING;
* BIZSPARK;
* BIZTALK;
* CORTANA;
* DIRECTX;
* DOTNET;
* DYNAMICS;
* EXCEL;
* EXCHANGE;
* FOREFRONT;
* GROOVE;
* HOLOLENS;
* HYPERV;
* KINECT;
* LYNC;
* MSDN
* O365
* OFFICE;
* OFFICE365;
* ONEDRIVE;
* ONENOTE;
* OUTLOOK;
* POWERPOINT;
* SHAREPOINT;
* SKYPE;
* VISIO;
* VISUALSTUDIO.

Следующие слова нельзя использовать ни в качестве целого слова, ни как часть строки в имени:

* LOGIN;
* MICROSOFT;
* WINDOWS;
* XBOX.

## <a name="solution"></a>Решение

Предоставьте имя, которое не содержит ни одного из зарезервированных слов.