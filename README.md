This is a MicroPython driver for the FT6x06 touch chip used by many touch displays. The chip uses i2c to communicate with the host, with an interrupt to signal when touch data is available. This driver can be used both via a callback triggered by an interrupt and using just data polling, requesting data when needed:

This is an example using the callback function:

```
def data_available(data):
    print(data)

i2c = SoftI2C(scl=40,sda=39)
ft = FT6206(i2c,interrupt_pin=Pin(16,Pin.IN),callback=data_available)
while True: time.sleep(1)
```

While this is just polling:

```
i2c = SoftI2C(scl=40,sda=39)
ft = FT6206(i2c)
while True:
    print(ft.get_touch_coords())
    time.sleep(1)
```

When `get_touch_coords()` is used and no data is available, None is returned.
When data is available (both in teh callback and polling) data is returned as an array of touches dictionaries:

```
# Just a single touch.
[{'area': 0, 'y': 212, 'x': 174, 'weight': 0, 'type': 'up'}]

# Two fingers at the same time in my test device.
[{'area': 0, 'y': 212, 'x': 174, 'weight': 0, 'type': 'up'}, {'area': 15, 'y': 103, 'x': 135, 'weight': 0, 'type': 'down'}]
```

The area/weight are probably available just on selected devices. In my device they are apparently meaningless.

When callback is used, the device reports `down` as event type when we touch the screen with the finger. As we move without detatching the finger from the dispaly, `up` is returned.

When polling is used, the behavior of the event type is the same, but in practical terms the `down` event is reported for something like a few hundred milliseconds from the moment of the touch, and then `up` events are reported.

## License

MIT license.
