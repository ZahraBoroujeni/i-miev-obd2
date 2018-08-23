::

           ___  __        __     __                          ___
    |\/| |  |  /__` |  | |__) | /__` |__| |    | __  |\/| | |__  \  /
    |  | |  |  .__/ \__/ |__) | .__/ |  | |    |     |  | | |___  \/
                    __                __        __ 
                   /  `  /\  |\ |    |__) |  | /__`
                   \__, /~~\ | \|    |__) \__/ .__/


This project aims to document the OBD-II codes for Mitsubishi I-Miev
electric vehicle.

Conventions used in this document:

- Message ID is always 3 hex characters
- Data bytes are zero-indexed: D0, D1, etc

OBD-II Traffic analysis
~~~~~~~~~~~~~~~~~~~~~~~

Periodically occurring PIDs:

- 1000ms (1 fps):
  01C [#note_testmode]_
- 200ms (5 fps):
  568 [#note_testmode]_
- 100ms (10 fps):
  101, 286, 298, 29A, 2F2, 374, 375, 384, 385, 389 [#note_testmode]_,
  38A [#note_testmode]_, 3A4, 408, 412_, 695, 696, 697, 6FA, 75A, 75B
- 50ms (20 fps):
  38D, 564, 565, 5A1, 6D0, 6D1, 6D2, 6D3, 6D4, 6D5, 6D6, 6DA
- 40ms (25 fps):
  424, 6E1, 6E2, 6E3, 6E4
- 20ms (50 fps):
  119, 149, 156, 200, 208_, 210, 212, 215, 231_, 300, 308, 325, 346, 418
- 10ms (100fps):
  236_, 285, 288, 373

.. [#note_testmode] Possibly only sent in debug mode.

PID descriptions
~~~~~~~~~~~~~~~~

.. _208:

208 - Brake pedal
-----------------

Break pedal sensor data is transmitted every 20ms. Data bits seem
to be following:

- D0: ``0x00`` (const?)
- D1: ``0x20`` (const?)
- D2: ``0x60`` (const?)
- D2-D3: pedal position  ``0x02`` is zero position
- D4-D5: speed, ``0xc000`` is zero speed
- D6-D7: speed, ``0xc000`` is zero speed

.. _210:

210 - Gas pedal
-----------------

Gas pedal sensor data is transmitted every 20ms. Data bits seem
to be following:

- D0-D1: ``0x0000`` (const?)
- D2: gas pedal position,  (const?)
- D3: just first bit changes
- D4: 0x80 or 0x00 
- D5-D6:  ``0x00`` (const?)


.. _231:

231 - Brake pedal switch sensor
-----------------

- D0-D3: ``0x00`` (const?)
- D4: ``0x02`` if brake is free, ``0x00`` if brake pedal is pressed
- D5-D7: ``0x00`` (const?)

.. _236:

236 - Steering wheel sensor
-----------------

Steering wheel sensor data is transmitted every 10ms. Data bits:

- D0-D1: Steering wheel position with 0.5 degree accuracy, center point ``(0.0 degrees) = D0:0x10, D1:0x00``. ``(((D0 * 256) + D1) - 4096) / 2 = steering wheel position in degrees)``. Negative angle - right, positive angle left.
- D2-D3: possibly represents rate of change, defaults to ``D2:0x10, D3:0x00`` when steering wheel is at rest.
- D4: counter, but only high-nibble bits (4-7) are used, ``D4[0:3]=0``
- D5: ``0x00`` (const?)
- D6: ``0x00`` (const?)
- D7: TODO

.. _412:

412 - Speed + Odometer value
-----------------

Transmitted every 100ms. Data bits:

- D0: ``0x7f`` (const?)
- D1: speed in km/h
- D2-D4: Odometer display in km. ``((((D2 * 256) + D3) * 256) + D4) = km/h``
- D5: ``0x00`` (const?)
- D6: ``0x21`` (const?)
- D7: ``0x06`` (const?)

.. _373:

373 - Battery
-----------------

Transmitted every 10ms. Data bits:

- D0: ``0x00`` (const?)
- D1: ``0x00`` (const?)
- D2-D3: Battery pack amps (A)
- D4-D5: Battery pack amps (A)
- D6: ``0x00`` (const?)
- D7: ``0x00`` (const?)

.. _374:

374 - Battery 
----------------------------

Transmitted every 100ms. Data bits:

- D0: ``0x00`` (const?)
- D1: state of charge : ([byte 1] - 10) / 2 , (209 == 99.5%, 38 == 14%)

.. _6E:

6E1/6E2/6E3/6E4 -  Battery 
-----------------

Transmitted every 40ms. Data bits:

- D0: counter
- D1: 0x00 (const?)
- D2: almost 0x4E (temperature?)
- D3: almost 0x4E (temperature?)
- D4:  0x01 (const?)
- D5: Amper
- D6: 0x01 (const?)
- D7: Amper

.. _424:

424 -  Light
-----------------

           | Bits  |       D0        |       D1         |        D2          |         D6          |
           | ----- | --------------  | ---------------- | ----------------   |  ----------------   | 
           | 0:    |                 | hazard lights    | Unlock doors       |                     |
           | 1:    |                 | Right Blinker    | Unlock driver door |                     |
           | 2:    | Automatic light | Upper Beam       |                    |                     |
           | 3:    | Fog lights      | Wiper rear       |                    | Rear window heating |
           | 4:    | Rear fog light  | Night headlights |                    |                     |
           | 5:    |                 | Sidelights       |                    |                     |
           | 6:    |                 |                  |                    |                     |
           | 7:    |                 |                  |                    |                     |

- D3: 
- D4:  
- D5: 
- D6:
- D7:
