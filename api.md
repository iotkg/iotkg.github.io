# API Reference

## run

Starts the data collection and BLE advertising process.

```js
const iotkg = require('iotkg');

iotkg.setSensors(...);
iotkg.run();
```

## start

Starts all processes and saves the code, so that everything
persists after the device is powered off.

```js
const iotkg = require('iotkg');

iotkg.setSensors(...);
iotkg.start();
```

## setSensors

Configures the sensors used for data collection.

- **sensors** `Object`:
    - **[sensor id]** `number`:
        - **name** `String`: The name of the sensor. Must be less than 20 characters.
        - **getValue** `function`: A function which returns the current value of the sensor.
        - **secondInterval** `number`: How frequently the sensor should be queried, in seconds. Defaults to 60.
        - **dataType** `'uint8|int8|uint16|int16|uint32|int32|float32|float64'`:
             The most suitable data type for the sensor. Smaller data types will allow for more
             data to be stored and faster transfer times. Defaults to `'float32'`.

```js
const iotkg = require('iotkg');

iotkg.setSensors({
    1: {
        name: 'Temparature',
        getValue: () => getTemperature(...),
        secondInterval: 45,
        dataType: 'float32',
    },
    2: ...
})
iotkg.start();
```

## setStorageByteLimit

Sets the maximum number of bytes which will be used for storing data. By default all available space is used.

```js
const iotkg = require('iotkg');
const Storage = require('Storage');

// use half of the available space
iotkg.setStorageByteLimit(Math.floor(Storage.getFree() / 2));

iotkg.setSensors(...);
iotkg.start();
```

## getMiFloraData

Gets the current value of all the sensors on the MiFlora.

- **macAddress** `string`: The MAC address of the MiFlora.
- **Returns** `Promise<Object>`: A promise which resolves to an object containing the keys `'temperature'`, `'sunlight'`, `'moisture'`, `'fertility'`.

```js
const iotkg = require('iotkg');

const macAddress = 'aa:bb:cc:dd:ee:ff';
const sensor = 'sunlight';

iotkg.setSensors({
    1: {
        name: sensor,
        getValue: iotkg.getMiFloraData(macAddress).then(data => data[sensor]),
    }
});
iotkg.start();
```

## getMiFlora...

The functions `getMiFloraTemperature`, `getMiFloraSunlight`, `getMiFloraMoisture`, `getMiFloraFertility` each get the current value of a single sensor on the MiFlora.

- **macAddress** `string`: The MAC address of the MiFlora.
- **Returns** `Promise<number>`: A promise which resolves the sensor value.


```js
const iotkg = require('iotkg');

const macAddress = 'aa:bb:cc:dd:ee:ff';
const secondInterval = 45;

iotkg.setSensors({
    1: { name: 'Temperature', secondInterval: secondInterval,
        getValue: iotkg.getMiFloraTemperature(macAddress) },
    2: { name: 'Moisture', secondInterval: secondInterval,
        getValue: iotkg.getMiFloraMoisture(macAddress) },
})
iotkg.start();
```

## getMiFloraSensorPreset

Returns a sensor configuration object which can be passed directly to [`setSensors`](#setSensors), and configures the device to collect data from a MiFlora.

- **macAddress** `string`: The MAC address of the MiFlora.
- **secondInterval** `number`: How frequently the sensors should be queried, in seconds. Defaults to 30.
- **sensorList** `['temperature|sunlight|moisture|fertility']`: The list of sensors which should be included. Defaults to all of them.
- **Returns** `Object`: A sensor configuration object.

```js
const iotkg = require('iotkg');

const macAddress = 'aa:bb:cc:dd:ee:ff';

iotkg.setSensors(iotkg.getMiFloraSensorPreset(macAddress, 45));
iotkg.start();
```

## findMiFloraDevices

Searches for MiFloras devices in Bluetooth range. The MAC address of every discovered device will be printed.

- **callback** `function(macAddress)`: A function which will be called each time a new MiFlora is discovered, with the MAC address as a parameter.
- **deviceLimit** `number`: The maximum number of devices to discover before the search ends. There is no limit by default.
- **secondLimit**: The time limit before the search ends, in seconds. Defaults to 10.

```js
const iotkg = require('iotkg');

iotkg.findMiFloraDevices(macAddress => {
    iotkg.setSensors(iotkg.getMiFloraSensorPreset(macAddress, 45));
    iotkg.run();
}, 1);
save();
```

## startMiFlora
Starts collecting data from a MiFlora sensor.

- **args** `object|number`: Either a single **secondInterval**, or an argument dictionary.
- **args.macAddress** `string`: The MAC address of the MiFlora. If not provided, the device will permanently use the first MiFlora it discovers.
- **args.secondInterval** `number`: How frequently the sensor should be queried, in seconds.
    If not provided, the device will search for a MiFlora in proximity.
- **sensorList** `['temperature|sunlight|moisture|fertility']`: The list of sensors which should be included. Defaults to all of them.

```js
const iotkg = require('iotkg');

iotkg.startMiFlora({
    macAddress: 'aa:bb:cc:dd:ee:ff',
    secondInterval: 45,
});
```
