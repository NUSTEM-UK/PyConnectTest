# PyConnectTest

Python testing tool for Connected devices.

## Spec

Presents a GUI window with a large representation of the most recent Connect network mood. Below, five buttons, each of which sends a specific mood to the Connect network. The main display gives some visual indication on receiving a new mood. It may be necessary to delay outgoing messages fractionally, to slow the system down enough to 'feel' right.

I'm assuming we build this as a [GUIzero](https://lawsie.github.io/guizero/) app, for speed and portablity. There's no particular reason it couldn't use PyQT or Tkinter or whatever, but GUIzero is likely to involve the smallest codebase and should be adequate for this. The app doesn't need to look great, though I guess eventually it might be useful if it's at least vaguely presentable.

Ideally, functions likely to be useful for other pieces of infrastructure or test tooling should be pulled into a separate `PyConnectLib` so we can reuse them. There may not be much in this case, I haven't really thought it through.

## MQTT details

```text
server: connect.nustem.uk
user:   connect
pass:   sent by separate method, I'm not that daft.
        (do not check into Github!)

Topic:              /MOOD   (both send and subscribe)
Message payloads:   HAPPY
                    SAD
                    HEART
                    SKULL
                    DUCK
                    (...possibly others, so build accordingly)
```

At the moment, the Kniwwelino code sends binary representations of the 5x5 matrix state for each mood, so we have:

```cpp
#define HAPPY "B0000001010000001000101110"
#define SAD "B0000001010000000111010001"
#define HEART "B0101011111111110111000100"
#define SKULL "B0111010101111110111001110" 
#define DUCK "B0110011100011110111000000"
```

...but that's a ridiculous way of doing things which I'll refactor at the Arduino library end. Nevertheless, it's worth bearing in mind that all parts of this might change.

(Placeholder) PNGs of the mood icons are included in `src/img/`.

## Notes

For the Arduino implementation, I don't bother with any logic to route messages internally from pressing a button to the display -- the button just sends the message, and the display reacts to messages received on the subscribed channel. That is: I bounce everything off the MQTT server. This offers some level of confirmation that the message has indeed been sent.

It occurs to me writing this, however, that I should be checking network status. If the Kniwwelino's network is down, message send is going to fail and in handling that we should probably trigger the behaviour we'd expect if the message had been received. Then at least you can play your message animations without any network at all. Huh. This piece of tooling has already been useful.