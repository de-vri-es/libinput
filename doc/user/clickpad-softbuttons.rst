.. _clickpad_softbuttons:

==============================================================================
Clickpad software button behavior
==============================================================================

"Clickpads" are touchpads without separate physical buttons. Instead, the
whole touchpad acts as a button and left or right button clicks are
distinguished by :ref:`the location of the fingers <software_buttons>` or
the :ref:`number of fingers on the touchpad <clickfinger>`.
"ClickPad" is a trademark by `Synaptics Inc. <http://www.synaptics.com/en/clickpad.php>`_
but for simplicity we refer to any touchpad with the above feature as Clickpad,
regardless of the manufacturer.

The kernel marks clickpads with the
`INPUT_PROP_BUTTONPAD <https://www.kernel.org/doc/Documentation/input/event-codes.txt>`_
property. Without this property, libinput would not know whether a touchpad
is a clickpad or not. To perform a right-click on a Clickpad, libinput
provides :ref:`software_buttons` and :ref:`clickfinger`.

.. note:: The term "click" refers refer to a physical button press
	  and/or release of the touchpad, the term "button event" refers to
	  the events generated by libinput in response to a click.

.. _software_buttons:

------------------------------------------------------------------------------
Software button areas
------------------------------------------------------------------------------

The bottom of the touchpad is split into three distinct areas generate left,
middle or right button events on click. The height of the button area
depends on the hardware but is usually around 10mm.

.. figure :: software-buttons-visualized.svg
     :align: center

     The locations of the virtual button areas.


Left, right and middle button events can be triggered as follows:

- if a finger is in the main area or the left button area, a click generates
  left button events.
- if a finger is in the right area, a click generates right button events.
- if a finger is in the middle area, a click generates middle button events.

.. figure:: software-buttons.svg
    :align: center

    Left, right and middle-button click with software button areas

The middle button is always centered on the touchpad and smaller in size
than the left or right button. The actual size is device-dependent. Many
touchpads do not have visible markings so the exact location of the button
is unfortunately not visibly obvious.

.. note:: If :ref:`middle button emulation <middle_button_emulation>` is
	  enabled on a clickpad, only left and right button areas are
	  available.

If fingers are down in the main area in addition to fingers in the
left or right button area, those fingers are are ignored.
A release event always releases the buttons logically down, regardless of
the current finger position

.. figure:: software-buttons-thumbpress.svg
    :align: center

    Only the location of the thumb determines whether it is a left, right or
    middle click.

The movement of a finger can alter the button area behavior:

- if a finger starts in the main area and moves into the software button
  area, the software buttons do not apply to that finger
- once a finger has moved out of the button area, it cannot move back in and
  trigger a right or middle button event
- a finger moving within the software button area does not move the pointer
- once a finger moves out out of the button area it will control the
  pointer (this only applies if there is no other finger down on the
  touchpad)

.. figure:: software-buttons-conditions.svg
    :align: center

    **Left:** moving a finger into the right button area does not trigger a
    right-button click.
    **Right:** moving within the button areas does not generate pointer
    motion.

On some touchpads, notably the 2015 Lenovo X1 Carbon 3rd series, the very
bottom end of the touchpad is outside of the sensor range but it is possible
to trigger a physical click there. To libinput, the click merely shows up as
a left button click without any positional finger data and it is
impossible to determine whether it is a left or a right click. libinput
ignores such button clicks, this behavior is intentional.

.. _clickfinger:

------------------------------------------------------------------------------
Clickfinger behavior
------------------------------------------------------------------------------

This is the default behavior on Apple touchpads. Here, a left, right, middle
button event is generated when one, two, or three fingers are held down on the
touchpad when a physical click is generated, given the default mapping. The
location of the fingers does not matter and there are no software-defined
button areas. It is possible to swap right and middle buttons, the same way as
with :ref:`tapping <tapping>`.

.. figure:: clickfinger.svg
    :align: center

    One, two and three-finger click with Clickfinger behavior

On some touchpads, libinput imposes a limit on how the fingers may be placed
on the touchpad. In the most common use-case this allows for a user to
trigger a click with the thumb while leaving the pointer-moving finger on
the touchpad.

.. figure:: clickfinger-distance.svg
    :align: center

    Illustration of the distance detection algorithm

In the illustration above the red area marks the proximity area around the
first finger. Since the thumb is outside of that area libinput considers the
click a single-finger click rather than a two-finger click.

.. _special_clickpads:

------------------------------------------------------------------------------
Special Clickpads
------------------------------------------------------------------------------

The Lenovo \*40 series laptops have a clickpad that provides two software button sections, one at
the top and one at the bottom. See :ref:`Lenovo \*40 series touchpad support <t440_support>`
for details on the top software button.

Some Clickpads, notably some Cypress ones, perform right button detection in
firmware and appear to userspace as if the touchpad had physical buttons.
While physically clickpads, these are not handled by the software and
treated like traditional touchpads.
