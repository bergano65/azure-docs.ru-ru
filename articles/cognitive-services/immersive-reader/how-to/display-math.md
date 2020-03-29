---
title: Отображение математики в погружение Читателя
titleSuffix: Azure Cognitive Services
description: Эта статья покажет вам, как отображать математику в Immersive Reader.
author: pasta
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: pasta
ms.openlocfilehash: e01cc9e8cedb6c38da0b56e04419c706d5d0566e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946125"
---
# <a name="how-to-display-math-in-the-immersive-reader"></a>Как отобразить математику в захватывающем reader

Погружение Reader может отображать математику, когда это предусмотрено в виде математического языка разметки ([MathML](https://developer.mozilla.org/docs/Web/MathML)).
Тип MIME может быть установлен через [кусок](../reference.md#chunk)Immersive Reader. Для получения дополнительной информации смотрите [поддерживаемые типы MIME.](../reference.md#supported-mime-types)

## <a name="send-math-to-the-immersive-reader"></a>Отправить математику к захватывающему читателю
Для того, чтобы отправить математику в Immersive Reader, поставить кусок, содержащий MathML, и установить тип MIME; ```application/mathml+xml```

Например, если содержание содержимого было следующим:

```html
<div id='ir-content'>
    <math xmlns='http://www.w3.org/1998/Math/MathML'>
        <mfrac>
            <mrow>
                <msup>
                    <mi>x</mi>
                    <mn>2</mn>
                </msup>
                <mo>+</mo>
                <mn>3</mn>
                <mi>x</mi>
                <mo>+</mo>
                <mn>2</mn>
            </mrow>
            <mrow>
                <mi>x</mi>
                <mo>−</mo>
                <mn>3</mn>
            </mrow>
        </mfrac>
        <mo>=</mo>
        <mn>4</mn>
    </math>
</div>
```

Затем вы можете отобразить содержимое, используя следующий JavaScript.

```javascript
const data = {
    title: 'My Math',
    chunks: [{
        content: document.getElementById('ir-content').innerHTML.trim(),
        mimeType: 'application/mathml+xml'
    }]
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, data, YOUR_OPTIONS);
```

При запуске Immersive Reader, вы должны увидеть:

![Математика в погружение Читателя](../media/how-tos/1-math.png)

## <a name="next-steps"></a>Дальнейшие действия

* Ознакомьтесь с разделом о [пакете SDK для иммерсивного средства чтения](https://github.com/microsoft/immersive-reader-sdk) и [справочнике по этому пакету](../reference.md).