..
   This document was developed primarily by a NIST employee. Pursuant
   to title 17 United States Code Section 105, works of NIST employees
   are not subject to copyright protection in the United States. Thus
   this repository may not be licensed under the same terms as Bluesky
   itself.

   See the LICENSE file for details.

.. role:: key
    :class: key


.. _cheatsheet:


Command cheatsheet
==================

This section provides a quick summary of the data acquisition commands
discussed in the previous sections.

.. admonition:: Stopping a measurement
   :class: attention

   To stop any command do :key:`Ctrl`-:key:`C` :key:`Ctrl`-:key:`C`

   That is, hit :key:`Ctrl`-:key:`C` twice!

   This will **pause** the running command and return you to the command line.

   Do ``RE.resume()`` to continue the command from where it stopped.

   Do ``RE.stop()`` to fully stop the command.


.. note:: Parentheses |nd| ``()`` |nd| are an essential part of the
          syntax.  Same for ``RE()`` and ``%`` when specified.  

Helper commands
---------------

These commands bypass the run engine and are intended for command line use only.  

``shb.open()`` / ``shb.close()``
  Open / close the photon shutter |harr| :numref:`Section %s <shutters>`

``%ct`` / ``%ct N``
  Count the monitor for the current dwell time, or count the monitor for the specified time

``%ctm`` / ``%ctm N``
  Count the monitor nd the Mythen for the current dwell time, or count
  the monitor for the specified time

``%att N``
  Set the attenuator level between 0 and 15 |harr| :numref:`Section %s <attenuator>`

``%dt N``
  Set the dwell time to a value in seconds. If the value cannot be
  interpreted as a number, it will be set to 0.5 seconds.

General commands
----------------

``show_symbols()``
  Print device and plan names to the screen

``RE(mv(delta, <value>))``
  Move any named motor TO a position (``delta`` is an example) |harr| :numref:`Section %s <goniometer_axes>`

``RE(mvr(delta, <value>))``
  Move any named motor BY an amount (``delta`` is an example) |harr| :numref:`Section %s <goniometer_axes>`

``RE(mv(attenuator, N))``
  Set attenuator, N is an integer between 0 and 15 |harr| :numref:`Section %s <attenuator>`

``RE(mv(slits.vsize, 0.15))``
  Set the goniometer slit size |harr| :numref:`Section %s <goniometer_slits>`

``RE(mv(dwell_time, 0.5))``
  Set measurememt time  |harr| :numref:`Section %s <dwelltime>`

``RE(count([detector], N))`` / ``RE(count([detector1, detector2, ...], N))``
  Count on a detector N times.  Detector can be ``struck``,
  ``mythen``, etc.  Can also be a list of detectors.

``RE(linescan(<motor>, <detector>, <start>, <stop>, <N>))``
  move a motor, plot a signal |harr| :numref:`Section %s <linescan>`

``RE(pluck())``
  Select a point from a plot on screen and move that motor to that position |harr| :numref:`Figure %s <fig-pluckprompt>`



Refine beam shape and position
------------------------------
``RE(mv(m2.yaw, 0.01))``
  Adjust M2 yaw  |harr| :numref:`Section %s <focusing_mirror>`

``RE(mv(m2.lateral, 0.01))``
  Adjust M2 lateral position  |harr| :numref:`Section %s <focusing_mirror>`


Alignment scans
---------------

``RE(align_slits())``
  Align slits around beam and make beam position at slit position = 0 |harr| :numref:`Section %s <slit_align>`

``RE(linescan(dethor, 'mythen', -3, 3, 61)``
  Align the dethor motor to center detector on beam |harr| :numref:`Section %s <dethor_align>`

``RE(mythen_calibration(-4, 1, 1001))``
  Mythen calibration routine |harr| :numref:`Section %s <mythen_cal>`

..
   linescan
   mythen_calibration
   align_delta
   sample_vertical
   sample_pitch
   verify_delta_eta
   eta_calibration
   slitscan
   align_slits
   xrr
   

Measurement scans
-----------------

``RE(xrr())``
  Make an XRR scan
