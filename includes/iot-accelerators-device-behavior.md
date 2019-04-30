---
title: включение файла
description: включение файла
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 07/26/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 0b2b6814ad0b55d8f56998f6d4c9b6bb88663e04
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61446372"
---
## <a name="state-behavior"></a>Поведение состояния

Раздел [Simulation](../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#simulation) схемы модели устройства определяет внутреннее состояние имитированного устройства:

- `InitialState` определяет начальные значения для всех свойств объекта состояния устройства.
- `Script` указывает файл JavaScript, который выполняется по расписанию, чтобы обновить состояние устройства.

В следующем примере приводится определение объекта состояния устройства для имитированного устройства Chiller:

```json
"Simulation": {
  "InitialState": {
    "online": true,
    "temperature": 75.0,
    "temperature_unit": "F",
    "humidity": 70.0,
    "humidity_unit": "%",
    "pressure": 150.0,
    "pressure_unit": "psig",
    "simulation_state": "normal_pressure"
  },
  "Interval": "00:00:05",
  "Scripts": {
    "Type": "javascript",
    "Path": "chiller-01-state.js"
  }
}
```

Состояние имитированного устройства, как определено в разделе `InitialState`, служба моделирования хранит в памяти. Сведения о состоянии передаются как входные данные в функцию `main`, определенную в файле **chiller-01-state.js**. В этом примере служба моделирования запускает файл **chiller-01-state.js** каждые 5 секунд. Сценарий может изменить состояние имитированного устройства.

Ниже показана структура стандартной функции `main`:

```javascript
function main(context, previousState, previousProperties) {

  // Use the previous device state to
  // generate the new device state
  // returned by the function.

  return state;
}
```

Параметр `context` содержит следующие свойства:

- `currentTime` как строку в формате `yyyy-MM-dd'T'HH:mm:sszzz`;
- `deviceId`, например `Simulated.Chiller.123`;
- `deviceModel`, например `Chiller`.

В параметре `state` содержится состояние устройства, поддерживаемое службой моделирования устройств. Это значение представляет собой объект `state`, возвращенный предыдущим вызовом функции `main`.

В примере ниже показана стандартная реализация метода `main` для обработки состояния устройства, которое хранится в службе моделирования:

```javascript
// Default state
var state = {
  online: true,
  temperature: 75.0,
  temperature_unit: "F",
  humidity: 70.0,
  humidity_unit: "%",
  pressure: 150.0,
  pressure_unit: "psig",
  simulation_state: "normal_pressure"
};

function restoreState(previousState) {
  // If the previous state is null, force a default state
  if (previousState !== undefined && previousState !== null) {
      state = previousState;
  } else {
      log("Using default state");
  }
}

function main(context, previousState, previousProperties) {

  restoreState(previousState);

  // Update state

  return state;
}
```

В примере ниже показано, каким образом метод `main` может смоделировать значения телеметрии, изменяющиеся с течением времени:

```javascript
/**
 * Simple formula generating a random value
 * around the average and between min and max
 */
function vary(avg, percentage, min, max) {
  var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
  value = Math.max(value, min);
  value = Math.min(value, max);
  return value;
}


function main(context, previousState, previousProperties) {

    restoreState(previousState);

    // 75F +/- 5%,  Min 25F, Max 100F
    state.temperature = vary(75, 5, 25, 100);

    // 70% +/- 5%,  Min 2%, Max 99%
    state.humidity = vary(70, 5, 2, 99);

    log("Simulation state: " + state.simulation_state);
    if (state.simulation_state === "high_pressure") {
        // 250 psig +/- 25%,  Min 50 psig, Max 300 psig
        state.pressure = vary(250, 25, 50, 300);
    } else {
        // 150 psig +/- 10%,  Min 50 psig, Max 300 psig
        state.pressure = vary(150, 10, 50, 300);
    }

    return state;
}
```

Вы можете просмотреть полный файл [chiller-01-state.js](https://github.com/Azure/device-simulation-dotnet/blob/master/Services/data/devicemodels/scripts/chiller-01-state.js) на GitHub.

## <a name="method-behavior"></a>Поведение метода

Раздел [CloudToDeviceMethods](../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#cloudtodevicemethods) схемы модели устройства определяет методы, на которые отвечает имитированное устройство.

В примере ниже приводится список методов, поддерживаемых имитированным устройством Chiller:

```json
"CloudToDeviceMethods": {
  "Reboot": {
    "Type": "javascript",
    "Path": "Reboot-method.js"
  },
  "FirmwareUpdate": {
    "Type": "javascript",
    "Path": "FirmwareUpdate-method.js"
  },
  "EmergencyValveRelease": {
    "Type": "javascript",
    "Path": "EmergencyValveRelease-method.js"
  },
  "IncreasePressure": {
    "Type": "javascript",
    "Path": "IncreasePressure-method.js"
  }
}
```

У каждого метода есть связанный файл JavaScript, который реализует поведение метода.

Состояние имитированного устройства, как определено в разделе `InitialState` схемы, служба моделирования хранит в памяти. При вызове метода сведения о состоянии передаются как входные данные в функцию `main`, определенную в файле JavaScript. Сценарий может изменить состояние имитированного устройства.

Ниже показана структура стандартной функции `main`:

```javascript
function main(context, previousState, previousProperties) {

}
```

Параметр `context` содержит следующие свойства:

- `currentTime` как строку в формате `yyyy-MM-dd'T'HH:mm:sszzz`;
- `deviceId`, например `Simulated.Chiller.123`;
- `deviceModel`, например `Chiller`.

В параметре `state` содержится состояние устройства, поддерживаемое службой моделирования устройств.

Параметр `properties` содержит свойства устройства, записанные как сообщаемые свойства в двойнике устройства Центра Интернета вещей.

Есть три глобальные функции, которые можно использовать для реализации поведения метода:

- `updateState`, чтобы обновить состояние, которое служба моделирования хранит в памяти.
- `updateProperty`, чтобы обновить отдельное свойство устройства.
- `sleep`, чтобы приостановить выполнение для моделирования длительной задачи.

В примере ниже приводится сокращенная версия сценария **IncreasePressure-method.js**, используемая имитированными устройствами Chiller:

```javascript
function main(context, previousState, previousProperties) {

    log("Starting 'Increase Pressure' method simulation (5 seconds)");

    // Pause the simulation and change the simulation mode so that the
    // temperature will fluctuate at ~250 when it resumes
    var state = {
        simulation_state: "high_pressure",
        CalculateRandomizedTelemetry: false
    };
    updateState(state);

    // Increase
    state.pressure = 210;
    updateState(state);
    log("Pressure increased to " + state.pressure);
    sleep(1000);

    // Increase
    state.pressure = 250;
    updateState(state);
    log("Pressure increased to " + state.pressure);
    sleep(1000);

    // Resume the simulation
    state.CalculateRandomizedTelemetry = true;
    updateState(state);

    log("'Increase Pressure' method simulation completed");
}
```

## <a name="debugging-script-files"></a>Отладка файлов сценария

Подключить отладчик к интерпретатору Javascript, который служба моделирования устройств использует для запуска сценариев состояния и метода, невозможно. Однако сведения можно записать в журнале службы. Встроенная функция `log()` позволяет сохранить сведения для трассировки и отладки выполнения функции.

При наличии синтаксической ошибки работа интерпретатора завершится сбоем и в журнал службы будет записано исключение `Jint.Runtime.JavaScriptException`.

Сведения о локальном запуске службы имитирования устройств содержатся в статье [Running the service locally](https://github.com/Azure/device-simulation-dotnet#running-the-service-locally-eg-for-development-tasks) (Локальный запуск службы) на сайте GitHub. Локальный запуск службы упрощает отладку имитированного устройства перед его развертыванием в облако.