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

const secondInterval = 45;

iotkg.setSensors({
    1: {
        name: 'Temperature',
        getValue: E.getTemperature,
        secondInterval,
    },
    2: {
        name: 'Light',
        getValue: Puck.light,
        secondInterval,
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

const macAddress = 'aa:bb:cc:dd:ee:ff';
iotkg.startMiFlora({ macAddress, secondInterval: 30 });
```

## Multiple MiFlora sensors

You can collect temperature from two (or more) MiFloras:


```javascript
const iotkg = require('iotkg');

const secondInterval = 45;
const macAddress1 = 'a1:b1:c1:d1:e1:f1';
const macAddress2 = 'a2:b2:c2:d2:e2:f2';

iotkg.setSensors({
    1: {
        name: 'Temperature 1',
        getValue: () => getMiFloraTemperature(macAddress1)
        secondInterval,
    },
    2: {
        name: 'Temperature 2',
        getValue: () => getMiFloraTemperature(macAddress2)
        secondInterval,
    },
});
iotkg.start();
```

If you want to collect all of the sensor values, you can make this more general:

```javascript
const iotkg = require('iotkg');

const sensors1 = getMiFloraSensorPreset('a1:b1:c1:d1:e1:f1', 45);
const sensors2 = getMiFloraSensorPreset('a2:b2:c2:d2:e2:f2', 45);

const sensors = {};
for (let i = 1; i <= 4; i++) {
    sensors[i] = {...sensors1, name: sensors1[i] + ' 1' };
    sensors[i + 4] = {...sensors2, name: sensors2[i] + ' 2' };
}

iotkg.setSensors(sensors);
iotkg.start();



