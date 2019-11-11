---
title: Справочник по пакету SDK для iOS для чтения
titleSuffix: Azure Cognitive Services
description: Пакет SDK для iOS Reader — это SWIFT-Кокоапод, который позволяет интегрировать иммерсивное средство чтения в приложение iOS.
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 08/01/2019
ms.author: metan
ms.openlocfilehash: 67d6b8c22c5635bd789078a7f91b02f8b07e5e70
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903123"
---
# <a name="immersive-reader-sdk-reference-for-ios"></a>Справочник по пакету SDK для иммерсивного чтения для iOS

Пакет SDK для iOS Reader — это SWIFT-Кокоапод, который позволяет интегрировать иммерсивное средство чтения в приложение iOS.

## <a name="functions"></a>Функции

Пакет SDK предоставляет одну функцию `launchImmersiveReader(navController, token, subdomain, content, options, onSuccess, onFailure)`.

### <a name="launchimmersivereader"></a>лаунчиммерсивереадер

Запускает иммерсивное средство чтения, запустив контроллер представления в приложении iOS.

```swift
public func launchImmersiveReader(navController: UINavigationController, token: String, subdomain: String, content: Content, options: Options?, onSuccess: @escaping () -> Void, onFailure: @escaping (_ error: Error) -> Void)
```

#### <a name="parameters"></a>parameters

| имя | введите | ОПИСАНИЕ |
| ---- | ---- |------------ |
| `navController` | уинавигатионконтроллер | Контроллер навигации для приложения iOS, из которого вызывается функция. |
| `token` | Строка, | Маркер проверки подлинности Azure AD. См. [руководство по проверке подлинности Azure AD](./azure-active-directory-authentication.md). |
| `subdomain` | Строка, | Пользовательский поддомен для иммерсивного ресурса чтения в Azure. См. [руководство по проверке подлинности Azure AD](./azure-active-directory-authentication.md). |
| `content` | [Содержимое](#content) | Объект, содержащий содержимое, которое должно отображаться в иммерсивное средство чтения. |
| `options` | [Варианты](#options) | Параметры для настройки определенного поведения иммерсивное средство чтения. необязательный параметр. |
| `onSuccess` | () — > void | Замыкание, которое вызывается при успешном запуске иммерсивное средство чтения. |
| `onFailure` | (_ ошибка: [Ошибка](#error))-> void | Замыкание, которое вызывается, когда не удается загрузить иммерсивное средство чтения. Это замыкание возвращает объект [`Error`](#error) , представляющий код ошибки и сообщение об ошибке, связанное с ошибкой. Дополнительные сведения см. в [кодах ошибок](#error-codes). |

## <a name="types"></a>Типы

### <a name="content"></a>Содержимое

Содержит содержимое, отображаемое в иммерсивное средство чтения.

```swift
struct Content: Encodable {
    var title: String
    var chunks: [Chunk]
}
```

#### <a name="supported-mime-types"></a>Поддерживаемые типы MIME

| Тип MIME | ОПИСАНИЕ |
| --------- | ----------- |
| text/plain | Обычный текст. |
| Application/масмл + XML | Язык математической разметки (Масмл). [Дополнительные сведения](https://developer.mozilla.org/en-US/docs/Web/MathML)

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

| Код | ОПИСАНИЕ |
| ---- | ----------- |
| бадаргумент | Указан недопустимый аргумент. Дополнительные сведения см. в `message`. |
| Время ожидания | Не удалось загрузить иммерсивное средство чтения в течение указанного времени ожидания. |
| TokenExpired | Срок действия заданного маркера истек. |
| Регулирование | Превышено ограничение скорости вызовов. |
| интерналеррор | Произошла внутренняя ошибка в контроллере представления иммерсивного чтения. Дополнительные сведения см. в разделе `message`.|

## <a name="os-version-support"></a>Поддержка версий ОС

Пакет SDK для iOS для Reader поддерживается для iOS 9,0 или более поздней версии на iPad и iPhone.

## <a name="next-steps"></a>Дополнительная информация

* Изучите [пакет SDK для iOS для чтения на GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS)
* [Краткое руководство. Создание приложения iOS, запускающего иммерсивное средство чтения (SWIFT)](./ios-quickstart.md)