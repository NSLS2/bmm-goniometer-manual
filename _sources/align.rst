..  
   This document was developed primarily by a NIST employee. Pursuant
   to title 17 United States Code Section 105, works of NIST employees
   are not subject to copyright protection in the United States. Thus
   this repository may not be licensed under the same terms as Bluesky
   itself.

   See the LICENSE file for details.

.. role:: key
    :class: key



.. _align:

Preparing for XRR
=================

To start bsui using the goniometer profile:

.. code-block:: bash

   cd ~/git/bmm-profile-goniometer
   pixi run start

.. _fig-bsui:
.. figure:: _images/software/bsui_startup.png
   :target: _images/bsui_startup.png
   :width: 100%
   :align: center

   bsui startup



XRD mode of the photon delivery system
--------------------------------------

The very first step for setting up the goniometer for XRR is to move
the photo delivery system to the correct position for photo delivery
to the goniometer position.

This involves moving
 
+ the monochromator to the specified energy
+ the focusing mirror to the correct pitch and bend
+ the harmonic rejection mirror out of the way
+ the hutch slit assembly (:numref:`Section %s <blslits>`) to
  the correct height
+ the XAFS table to the correct height for supporting the
  flight path.

To set up the photon delivery system for scattering at 8600 eV:

.. code-block::

   RE(xrdmode())

or specify an energy:

.. code-block::

   RE(xrdmode(12000))

The single argument is the target energy in eV units.  The default is
8600 eV, the normal operating energy for experiments on the
goniometer.

This plan will look up the correct positions of all motors in the
`beamline lookup table
<https://github.com/NSLS2/bmm_tools/blob/main/src/bmm_tools/optics/mode_data.py>`__
and set all those axes moving to their correct positions.

Once all axes have arrived in position, a scan of the rocking curve of
the monochromator will be performed and ``dcm_pitch`` will be moved to
the peak of that scan.

Finally, the hutch slits will be opened wide, 7 mm wide by 1 mm tall,
allowing the beam size to be determined by the :numref:`gomiometer
slits (see Section %s) <goniometer_slits>`.

Once finished, engage the :numref:`DCM feedback system (see Section
%s) <feedback>`:  

.. code-block::

   feedback.engage()

.. admonition:: Future tech!

   This plan will eventually be used to perform scattering
   measurements at any energy above 8000 eV without having to do a
   time-consuming realignment of the goniometer.

   To obtain consistency in lateral position of the focused beam,
   Bruce is working with BLOP team in DSSI to optimize ``dcm_roll``
   and the orientation of the focusing mirror to provide stable beam
   position over the energy range from 8 keV to 20 keV.  

   To obtain consistency in vertical position, a scan of the pitch of
   the focusing mirror into the goniometer slits will deliver
   consistent beam height.




Goniometer alignment strategy
-----------------------------

.. note:: A few things that are explicit steps in SPEC are handled
	  differently in Bluesky.  For example, the Mythen
	  ``full_mca``, ROI1, is set at Bluesky startup and does not
	  need to be explicitly set.  All alignment steps and
	  associated data processing are discussed in detail in
	  :numref:`Section %s <plans>`.

#. Move the ``delta`` arm to 30 degrees to allow room for the YAG
   camera and its mount.  Secure the mount for the YAG camera to the
   mounting fixture on the floor, as shown in :numref:`Figure %s
   <fig-yag_camera>`.


   .. _fig-yag_camera:
   .. figure:: _images/align/yag_mount.jpg
      :target: _images/yag_mount.jpg
      :width: 30%
      :align: center

      The YAG camera mounted on its holder.


#. Place the Mythen in the most downstream position on the
   :olive:`(what is the arm called?)`. Measure and record the gap value
   |nd| typically around 90 mm.  See :numref:`Figure %s <fig-gap>` for a
   photo identifying what the gap is.  To record the gap in a way that
   the data acquisition software can use, do: 

   .. code-block:: python
   
      xrduser.gap = 90.0

#. Using the YAG camera, center the pin under the beam.

   a. Open the slits wide

      .. code-block:: python

	 RE(mv(slits.vsize, 4))
	 RE(mv(slits.hsize, 4))

      and set the attenuators to 0 

      .. code-block:: python

	 RE(mv(attenuator, 0))

   b. Adjust ``samplez`` to put the pin in the beam by seeing its shadow
      on the YAG.  

      .. code-block:: python

	 RE(mvr(samplez, <amount>))

   c. Mark the position of the pin in the beam

   d. Rotate ``phi`` stage by 180 degrees.  This is most easily done
      by loosening the lock (circled in :numref:`Figure %s
      <fig-phi_axis>`) and rotating the stage by hand.  Be sure to
      tighten the lock once moved.

      .. _fig-phi_axis:
      .. figure:: _images/align/phi_axis.jpg
	 :target: _images/yag_phi_axis.jpg
	 :width: 30%
	 :align: center

	 The lock for the ``phi`` axis.

   e. Mark pin again, then mark the geometric center of those two
      markings
   f. Move ``table.lateral`` so that the center of the two markings is in
      the center of the beam
   g. Rotate ``phi`` by -180 degrees to verfiy this alignment
   h. Rotate ``chi`` by -90 degrees: 

      .. code-block:: python

	 RE(mvr(chi, -90))

   i. Repeat steps (c) to (g) for this orientation
   j. Rotate ``chi`` back to 0 degrees: 

      .. code-block:: python

	 RE(mvr(chi, 90))

   .. admonition:: Future Tech!

      Automate the pin centering procedure using a camera that is
      supported by AreaDetector.  Automate the angle motions and
      determination of pin shadow positions.  Compute and move to
      target position in each direction.

#. Align the slits to be centered around the beam and define the 0 of
   each slit to be in the position that cuts the beam in half.  This is
   done by: 

   .. code-block:: python

      RE(align_slits())

   :numref:`See Section %s <slit_align>` for more details.

   After this step, reduce the amount of attenuation going into the
   Bicron.  These are the little inserts at the bottom of the box
   holding the Bicron, but above the beam path.  This is done because
   small slit height for XRR reduces the signal going into the Bicron.

#. Set slit sizes: 

   .. code-block:: python

      RE(mv(slits.vsize, 0.12, slits.hsize, 1.0))

   This vertical size |nd| 120 |mu|\ m |nd| is considerably smaller
   than the focused beam, but appropriate for an XRR measurement.

#. Align the table in the beam:
  
   .. code-block:: python

      RE(linescan(table.vertical, 'monitor', -1, 1, 51))
      RE(linescan(table.lateral, 'monitor', -2, 2, 51))

#. Do a linescan (:numref:`Section %s <linescan>`) of the ``dethor``
   motor to center the Mythen around the beam in the horizontal
   direction. 
  
   .. code-block:: python

      RE(linescan(dethor, 'mythen', -3, 3, 61))

   :numref:`See Section %s <dethor_align>` for more details.

#. Perform the Mythen calibration scan:
  
   .. code-block:: python

      RE(mythen_calibration(-4, 1, 1001))

   This will set the bounds of the ``dir`` and ``refl`` ROIs and write
   a calibration report to the proposal folder.  It will also record
   the calibration parameters.  :numref:`See Section %s <mythen_cal>`
   for more details.

   .. admonition:: Question
      :class: attention

      What is the CHESS calibration?  This needs to be written.

#. Verify the alignment of beam, goniometer, and detector are
   acceptable by scanning the ``delta`` arm and plotting the signal
   from both ``dir`` and ``refl``.  The ``dir`` plot should be narrower
   than **and** well centered in the ``refl`` plot.

   .. code-block:: python

      RE(linescan(delta, 'mythen', -0.15, 0.15, 61))

You are now ready for sample alignment.

.. _align_sample:

Sample alignment strategy
-------------------------

A sample for XRR is usually a large, flat wafer.  The correct
alignment has the sample surface parallel to the beam path and at a
height such that it blocks half the beam.  With that alignment, the
center of the beam will be on the center of the sample as the incident
angle changes and the beam will spread symmetrically over the length
of the sample as the angle changes.

Normally, sample alignment is fully automated using this plan:

.. code-block:: python

   RE(align_sample())

This performs 3 iterations of the following steps:

1. Align the sample vertically.

   .. code-block:: python

      RE(sample_vertical())

   This is a linescan (:numref:`Section %s <linescan>`) of the
   ``samplez`` motor against the signal in direct beam ROI.  Once
   finished an error function is fit to the measurement to find the
   position where the sample blocks half the beam.  

   .. _fig-vertical_align:
   .. figure:: _images/align/vertical.png
      :target: _images/vertical.png
      :width: 40%
      :align: center

   An example of a step of optimizing the vertical position along with
   the fitted step-like function used to find the optimal position.


2. Align the sample in eta (i.e. pitch relative to the incident beam).

   .. code-block:: python

      RE(sample_eta())

   This will run a linescan (:numref:`Section %s <linescan>`) of
   ``eta`` against the signal in direct beam ROI then do an
   appropriate analysis (more discussion below) to find the zero of
   ``eta``.  It then moves to the peak position of the ``dir`` signal
   and defines it as 0 by setting the EPICS offset accordingly.

   .. _fig-pitch_align:
   .. figure:: _images/align/pitch.png
      :target: _images/picth.png
      :width: 40%
      :align: center

   An example of a step of optimizing the ``eta`` position along with
   the peak analysis to find the optimal position.


The peak position of the direct beam signal |nd| ``dir`` |nd| is the
default choice in this automation.  The effect of total external
reflection at shallow angles can be seen in the ``refl`` signal, also
plotted on screen.

The use of this automation is `strongly` encouraged.  Doing so
attaches the results of the alignment as metadata to subsequent XRR
scans.  This allows a trail of experimental provenance throughout the
experiment, allowing the generation of detailed, useful reports on the
XRR measurements.  See :numref:`Section %s <reports>`.

If more than 3 iterations are needed, the number of iterations can be
specified as an argument to the plan:

.. code-block:: python

   RE(align_sample(iterations=3))

The final step to sample alignment is to refine the position of the
eta motor by performing the so-called "spine" refinement.

This, too, is automated, although it requires a bit more interaction.

The concept is to move to several |nd| typically 4 |nd| values of
``delta`` and ``eta``, then perform a scan in ``eta`` to find the peak
intensity in the ``dir`` ROI.  After all the previous goniometer and
sample alignment steps, ``eta`` might still be a few millidegrees out
of alignment.  By checking for the ``dir`` peak position at a series
of ``delta``/\ ``eta`` positions, this small misalignment is corrected.

Start by doing a refinement at ``eta`` = 1 and ``delta`` = 2:

.. code-block:: python

   RE(refine_eta.measure(1))

This will move ``delta`` to 2 and ``eta`` to 1.  It will then
determine the correct attenuator setting by starting at 6 and stepping
down until a strong signal is observed in ``dir``, or until the
attenuator is at setting 0.

It will then do a scan in eta around the current position.  That will
look something like :numref:`Figure %s <fig-eta_measure>`.

.. _fig-eta_measure:
.. figure:: _images/align/eta_measure.png
   :target: _images/eta_measure.png
   :width: 50%
   :align: center

   The ``eta`` refinemant scan, shown with the the ``refl`` channel
   along with the analysis of the ``dir`` peak.

If this result looks reasonable, then store its result by doing

.. code-block:: python

   refine_eta.push()

This will append to the ``refine_eta.points`` variable with
information from that step of the refinement process.

This procedure is repeated three more time.  Good additional choices
of positions of ``delta`` and ``eta`` are something like (3,1.5),
(4,2), and (6,3).  That is, do something like this for the full
``eta`` refinement:

.. code-block:: python

   RE(refine_eta.measure(1))
   refine_eta.push()
   RE(refine_eta.measure(1.5))
   refine_eta.push()
   RE(refine_eta.measure(2))
   refine_eta.push()
   RE(refine_eta.measure(3))
   refine_eta.push()

Depending on the details of the sample and its reflectivity, you may
need to choose different values of ``delta``/\ ``eta`` positions.

Once you are happy with the refinement steps, do:

.. code-block:: python

   refine_eta.compute_offset()

For each point, a difference between the nominal value of ``eta`` and
the peak of the refinement scan is measured.  This function will
compute the compute the mean of those differences.  A table
summarizing the refinement sequence is printed to screen along with
the average offset in ``eta``.  If the sample was well aligned in
earlier steps, the correction will be on the order of a millidegree or
less.

If this all seems correct, do:

.. code-block:: python

   RE(refine_eta.correct_eta())

This will move to the negative of the average ``eta`` offset computed
above, then reset the offset parameter in EPICS to define the new
``eta=0`` position.

You are now ready to measure XRR!

