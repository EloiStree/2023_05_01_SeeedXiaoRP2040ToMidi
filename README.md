#  Seeed Xiao RP2040 To Midi
This python script convert a SeedXiao RP2040 to a midi generator no all pin available.
Where to buy = https://www.seeedstudio.com/XIAO-RP2040-v1-0-p-5026.html

![image](https://user-images.githubusercontent.com/20149493/235472859-5cd6f63b-2e21-4ebc-8099-ffa58000662d.png)
![image](https://user-images.githubusercontent.com/20149493/235472911-d9be188a-5812-4262-aa1a-341cc186262d.png)


``` py

import time
import usb_midi
import adafruit_midi
import board
import digitalio

from adafruit_midi.note_on import NoteOn
from adafruit_midi.note_off import NoteOff


class BooleanObserver:
    def __init__(self):
        self.m_value = False
        self.m_firstSet = True

    def SetValueWithHasChangedValue(self, newValue):
        previous = self.m_value
        self.m_value = newValue

        if self.m_firstSet:
            self.m_firstSet = False
            return True
        else:
            return previous != newValue

    def GetPreviousValue(self):
        return not self.m_value

    def GetCurrentValue(self):
        return self.m_value

midi = adafruit_midi.MIDI(midi_out=usb_midi.ports[1], out_channel=0)


print("Default output MIDI channel:", midi.out_channel + 1)

button_pins = [digitalio.DigitalInOut(getattr(board, f'D{i}')) for i in range(11)]
button_state = [BooleanObserver() for i in range(11)]
for pin in button_pins:
    pin.direction = digitalio.Direction.INPUT
    pin.pull = digitalio.Pull.UP

while True:
    time.sleep(0.001)

    for i, pin in enumerate(button_pins):
       hasPinChanged = button_state[i].SetValueWithHasChangedValue(pin.value)
       if hasPinChanged:
#           print("Has pin Changed "+ str(i) +" "+ str(button_state[i].GetCurrentValue()))
           if not pin.value:
               midi.send(NoteOn(i+60, 120))
           else:
               midi.send(NoteOff(i+60, 120))

```

_I am not a python developer. If you have better proposition feel free to correct me_
