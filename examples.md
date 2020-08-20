# Examples

## Counter
Using a counter as a sensor is helpful for testing and debugging:

```javascript
const iotkg = require('iotkg');

let counter = 0;
function getCounter() {
    counter += 1;
    return counter;
}

iotkg.setSensors({
    1: {
        name: 'Counter',
        getValue: getCounter,
        secondInterval: 45,
        dataType: 'uint16', // for efficiency
    }
});
iotkg.start();
```

## Onboard sensors

If you are using a Puck.js, you can collect light and temperature values from the device's onboard sensors:

```javascript
const iotkg = require('iotkg');
const Puck = require('Puck');

iotkg.setSensors({
    1: {
        name: 'Temperature',
        getValue: E.getTemperature,
        secondInterval: 45,
    },
    2: {
        name: 'Light',
        getValue: Puck.light,
        secondInterval: 45,
    },
});
iotkg.start();
```

## MiFlora sensors

If you have a MiFlora, you can quickly get started using:

```javascript
const iotkg = require('iotkg');
iotkg.startMiFlora(45);
```

This will connect to the first MiFlora in range, and collect data every 45 seconds. If you want to get an explicit MAC address, you can run:

```javascript
const iotkg = require('iotkg');
iotkg.findMiFloraDevices();
```

Which will output, for example:

```
Found MiFlora Device: aa:bb:cc:dd:ee:ff
```

Then, you can hardcode this address:

```javascript
const iotkg = require('iotkg');

iotkg.startMiFlora('aa:bb:cc:dd:ee:ff', 45);
```

## Multiple MiFlora sensors

Let's say you want to collect temperature and humidity from two (or more) MiFloras:

```javascript
const iotkg = require('iotkg');

iotkg.setSensors({
    1: {
        name: 'Temperature 1',
        getValue: () => iotkg.getMiFloraData('a1:b1:c1:d1:e1:f1'),
        pickValue: 'temperature',
        secondInterval: 35,
    },
    2: {
        name: 'Temperature 2',
        getValue: () => iotkg.getMiFloraTemperature('a2:b2:c2:d2:e2:f2'),
        pickValue: 'temperature',
        secondInterval: 45,
    },
    3: {
        name: 'Humidity 1',
        combineWith: 1,
        pickValue: 'humidity',
    },
    4: {
        name: 'Humidity 2',
        combineWith: 2,
        pickValue: 'humidity',
    },
});
iotkg.start();
```

You can also simplify this for all sensor values:

```javascript
const iotkg = require('iotkg');

iotkg.setSensors({
    ...getMiFloraSensorPreset(35, 'a1:b1:c1:d1:e1:f1', 1),
    ...getMiFloraSensorPreset(45, 'a2:b2:c2:d2:e2:f2', 5),
});
iotkg.start();
```
