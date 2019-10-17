---
title: Справочник по пакету SDK для iOS для чтения
titleSuffix: Azure Cognitive Services
description: Справочник по пакету SDK для иммерсивного чтения для iOS
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 08/01/2019
ms.author: metan
ms.openlocfilehash: 1f85740c358bd0949fed9c954537f0926538995d
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388122"
---
# <a name="immersive-reader-sdk-reference-for-ios"></a>Справочник по пакету SDK для иммерсивного чтения для iOS

Пакет SDK для iOS Reader — это SWIFT-Кокоапод, который позволяет интегрировать иммерсивное средство чтения в приложение iOS.

## <a name="functions"></a>Функции Azure

Пакет SDK предоставляет одну функцию, `launchImmersiveReader(navController, token, subdomain, content, options, onSuccess, onFailure)`.

### <a name="launchimmersivereader"></a>лаунчиммерсивереадер

Запускает иммерсивное средство чтения, запустив контроллер представления в приложении iOS.

```swift
public func launchImmersiveReader(navController: UINavigationController, token: String, subdomain: String, content: Content, options: Options?, onSuccess: @escaping () -> Void, onFailure: @escaping (_ error: Error) -> Void)
```

#### <a name="parameters"></a>Параметры

| Name | Тип | Описание |
| ---- | ---- |------------ |
| `navController` | уинавигатионконтроллер | Контроллер навигации для приложения iOS, из которого вызывается функция. |
| `token` | Строка | Маркер проверки подлинности Azure AD. См. [руководство по проверке подлинности Azure AD](./azure-active-directory-authentication.md). |
| `subdomain` | Строка | Пользовательский поддомен для иммерсивного ресурса чтения в Azure. См. [руководство по проверке подлинности Azure AD](./azure-active-directory-authentication.md). |
| `content` | [Содержимое](#content) | Объект, содержащий содержимое, которое должно отображаться в иммерсивное средство чтения. |
| `options` | [Варианты](#options) | Параметры для настройки определенного поведения иммерсивное средство чтения. Необязательный элемент. |
| `onSuccess` | () — > void | Замыкание, которое вызывается при успешном запуске иммерсивное средство чтения. |
| `onFailure` | (_ ошибка: [Ошибка](#error))-> void | Замыкание, которое вызывается, когда не удается загрузить иммерсивное средство чтения. Это замыкание возвращает объект [`Error`](#error) , представляющий код ошибки и сообщение об ошибке, связанное с ошибкой. Дополнительные сведения см. в [кодах ошибок](#error-codes). |

## <a name="types"></a>Типы

### <a name="content"></a>Контент

Содержит содержимое, отображаемое в иммерсивное средство чтения.

```swift
struct Content: Encodable {
    var title: String
    var chunks: [Chunk]
}
```

#### <a name="supported-mime-types"></a>Поддерживаемые типы MIME

| Тип MIME | Описание |
| --------- | ----------- |
| text/plain | Обычный текст. |
| Application/масмл + XML | Язык математической разметки (Масмл). [Узнайте больше](https://developer.mozilla.org/en-US/docs/Web/MathML).

### <a name="options"></a>Параметры

Содержит свойства, которые настраивают определенное поведение иммерсивное средство чтения.

```swift
struct Options {
    var uiLang: String?
    var timeout: TimeInterval?
}
```

### <a name="error"></a>Ошибка

Содержит сведения об ошибке.

```swift
struct Error {
    var code: String
    var message: String
}
```

#### <a name="error-codes"></a>Коды ошибок

| Код | Описание |
| ---- | ----------- |
| бадаргумент | Указан недопустимый аргумент. Дополнительные сведения см. в `message`. |
| Время ожидания | Не удалось загрузить иммерсивное средство чтения в течение указанного времени ожидания. |
| TokenExpired | Срок действия заданного маркера истек. |
| Регулирование | Превышено ограничение скорости вызовов. |
| интерналеррор | Произошла внутренняя ошибка в контроллере представления иммерсивного чтения. Дополнительные сведения см. в разделе `message`.|

## <a name="os-version-support"></a>Поддержка версий ОС

Пакет SDK для iOS для Reader поддерживается для iOS 9,0 или более поздней версии на iPad и iPhone.

## <a name="next-steps"></a>Дальнейшие действия

* Изучите [пакет SDK для iOS для чтения на GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS)
* [Краткое руководство. Создание приложения iOS, запускающего иммерсивное средство чтения (SWIFT)](./ios-quickstart.md)