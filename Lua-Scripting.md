# Basics

## Conventions

- The Lua interpreter will trigger an error if there is a mistake in the program, check the rusEFI console to see errors and script output.
- Unless otherwise mentioned, all `index` parameters start with the first element at index at 0.

## Writing Your Script

The entire Lua script is read at startup, then a function called `onTick` is called periodically by rusEFI.

Here is a simple script you can run to illustrate this behavior:

```
print('Hello Lua startup!')

function onTick()
    print('Hello onTick()')
end
```

### Controlling the Tick Rate

The function `setTickRate(hz)` can be used to configure how often rusEFI calls the `onTick` function.  If your script does a lot of work in the `onTick()` function it may run slower than the desired rate.  Since the Lua virtual machine runs at low priority compared to other functions of the ECU, it is impossible to swamp the ECU with too much Lua work, so set the tick rate to whatever is necessary.

```
n = 0
setTickRate(5) --set tick rate to 5hz
function onTick()
    print('Hello Lua: ' ..n)
    n = n + 1
end
```

# Function Reference

## Utility

### `print(msg)`

Print a line of text to the ECU's log.

- Parameters
  - `msg`: The message to print.  Pass a string or number and it will be printed to the log.
- Returns
  - none

#### Usage example

Program:
```
n = 5.5
print('Hello Lua, number is: ' ..n)
```
Output:
`Hello Lua, number is 5.5`

### `setTickRate(hz)`

Sets the rate at which rusEFI calls your `onTick` function, in hz.

- Parameters
  - `hz`: Desired tick rate, in hz.  Values passed will be clamped to a minimum of 1hz, and maximum of 100hz.
- Returns
  - none

### `table3d(tableIdx, x, y)`

Looks up a value from the specified FSIO table.

- Parameters
  - `tableIdx`: Index of the table to use.  Currently 4 tables are supported, so indices 0, 1, 2, and 3 are valid.
  - `x`: X-axis value to look up in the table (this is often RPM)
  - `y`: Y-axis value to look up in the table (this is often load)
- Returns
  - A number representing the value looked up from the table.

### `setDebug(index, value)`

Sets a debug channel to the specified value.  Note: this only works when the ECU debug mode is set to `Lua`.

- Parameters
  - `index`: the index of the debug channel to set, 1 thru 7 inclusive.
  - `value`: the value to set the channel to
- Returns
  - none

## Input

### `getSensor(index)`

Reads the specified sensor.

- Parameters
  - `index`: Index of the sensor to read.  [A list of sensor indices can be found here.](https://github.com/rusefi/rusefi/blob/master/firmware/controllers/sensors/sensor_type.h)
- Returns
  - A reading from the sensor, or `nil` if the sensor has a problem or isn't configured.

### `getSensorRaw(index)`

Reads the raw value from the specified sensor.  For most sensors, this means the analog voltage on the relevant input pin.

- Parameters
  - `index`: Index of the sensor to read.  [A list of sensor indices can be found here.](https://github.com/rusefi/rusefi/blob/master/firmware/controllers/sensors/sensor_type.h)
- Returns
  - The raw value that yielded the sensor reading, or 0 if the sensor doesn't support raw readings, isn't configured, or has failed.

### `hasSensor(index)`

Checks whether a particular sensor is configured (whether it is currently valid or not).

- Parameters
  - `index`: Index of the sensor to check.  [A list of sensor indices can be found here.](https://github.com/rusefi/rusefi/blob/master/firmware/controllers/sensors/sensor_type.h)
- Returns
  - A boolean value, `true` if the sensor is configured, and `false` if not.


### `getAnalog(index)`

Reads an analog voltage from one of the FSIO ADC inputs.

- Parameters
  - `index`: The index of the configured analog input to read. Currently 4 inputs are supported, so values of 0, 1, 2, 3 are valid for this parameter.
- Returns
  - The raw analog voltage from the input pin. Most rusEFI ECUs can measure between 0 and 5 volts, so this value will be a number between 0 and 5.

### `getDigital(index)`

Reads a digital input from the specified channel.

- Parameters
  - `index`: The index of the digital channel to read.  See table below for values.
- Returns
  - A boolean value representing the state of the input pin.  `true` = high voltage (above ~2 volts), `false` = low voltage (below ~3 volts)

Valid `index` parameter values:

| Index | Channel Name |
| --- | ---:|
| 0 | Clutch down switch |
| 1 | Clutch up switch |
| 2 | Brake switch |
| 3 | AC switch |


## Output

coming soon...