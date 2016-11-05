---
layout: post
title: Conceptual Models
---

‘Conceptual models’ is one of the core ideas discussed in Donald Norman’s book, [*The Design of Everyday Things*][DET]. A conceptual model is the mental model a person forms that relates their interaction with an interface to the underlying thing the interface controls. It is the explanatory framework a person has to explain how a control operates something.

One example Norman gives is that of thermostat controls. A thermostat interface presents the user with a range of temperatures over which to set the control. However, the interface itself does not explain how the thermostat controls the heating device.

The problem is illustrated well by a common misconception about thermostats. If a room is very cold and I want to heat it quickly, does setting the temperature very high, much higher than I want it to be, cause the heating device to work harder and heat the room faster or not? For most thermostats, almost certainly not. While it might be a popular enough conceptual model, most thermostats are simply a on-off switch. When the temperature gets too low, the thermostat turns the heater fully on. When the temperature gets too high, the thermostat turns the heater fully off. For anyone with a bit of engineering knowledge, the conceptual model may well extend to include an understanding of how an electrical control system uses an hysteresis loop to keep the temperature revolving within a certain range.

As this very simple example shows, an interface is not the same thing as that which it controls. More troubling, even a very simple interface, which one may initially consider to be virtually self-evident in what it does, is open to interpretation - and misinterpretation.

What ever the outcome of a person’s interaction with a control, there is one thing in common between the person who correctly interprets and the person who incorrectly interprets what the control does: both are seeking an explanation for their interaction with the device. The device, however simple is not self-evident and requires interpretation. Human beings, by their very nature, seek explanations that order the world around them. People will form explanations, rightly or wrongly, given any amount of information provided to them. Whether that information is sufficient or insufficient, helpful or misleading, people will create conceptual models in their interactions with a device.

If this is so for the humble thermostat, one can imagine how much more complicated things get when dealing with even a relatively simple computer program’s user interface, let alone a complex one.

As software developers, it is important to recognise that people will develop conceptual models of what is happening in the software, whether we like it or not. The only question is, does the software operate in such a way as to guide people’s conceptual models correctly. Does it reinforce their correct conceptions, and effectively bring into question their incorrect conceptions? How to go about that forms the bulk of the content in Norman’s book. To appreciate the significance of that content, however, it is important to recognise the existence and significance of things like the conceptual model. It is, after all, incredibly easy to fail to recognise the interpretive effort going on in the mind of a user, even for the very simplest of user interfaces.

[DET]: http://www.amazon.com/Design-Everyday-Things-Donald-Norman/dp/0465067107
