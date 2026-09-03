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


XRD mode of the photon delivery system
--------------------------------------

.. code-block::

   RE(xrdmode(8600))

Discuss other energies.

Discuss what is happening.


Goniometer alignment strategy
-----------------------------

.. note:: A few things that are explicit steps in SPEC are handled
	  differently in Bluesky.  For example, the Mythen
	  ``full_mca``, ROI1, is set at Bluesky startup and does not
	  need to be explicitly set.

1. Place the Mythen in the most downstream position on the
   :olive:`(what is the arm called?)`. Measure and record the gap value
   |nd| typically around 90 mm.  

2. Measure and record the gap.  See :numref:`Figure %s <fig-gap>` for a
   photo identifying what the gap is.  To record the gap in a way that
   the software can use, do: ``xrduser.gap = <value>``.

3. Using the YAG camera, center the pin under the beam.

   a. open slits wide

      .. code-block:: python

	 RE(mv(slits.vsize, 4))
	 RE(mv(slits.hsize, 4))


   b. adjust ``samplez`` to put the pin in the beam by seeing its shadow
      on the YAG.  

      .. code-block:: python

	 RE(mvr(samplez, <amount>))

   c. mark the position of the pin in the beam
   d. rotate ``phi`` stage by 180 degrees
   e. mark pin again, then mark the geometric center of those two
      markings
   f. move ``table.lateral`` so that center of the two markings is in
      the center of the beam
   g. rotate ``phi`` by -180 degrees to verfiy this alignment
   h. rotate ``chi`` by -90 degrees: 

      .. code-block:: python

	 RE(mvr(chi, -90))

   i. repeat steps c to g
   j. rotate ``chi`` back to 0 degrees: 

      .. code-block:: python

	 RE(mvr(chi, 90))

      .. admonition:: Future Tech!

	 Automate all of this using a camera that is supported by
	 AreaDetector.  Automate the angle motions and determination
	 of pin shadow positions.  Compute and move to target position
	 in each direction.


4. Align the slits to be centered around the beam and define the 0 of
   each slit to be in the position that cuts the beam in half.  This is
   done by: 

   .. code-block:: python

      RE(align_slits())

   :numref:`See Section %s <slit_align>`.

5. Set slit sizes: 

   .. code-block:: python

      RE(mv(slits.vsize, 0.15, slits.hsize, 1.0))

6. Align the table in the beam:
  
   .. code-block:: python

      RE(linescan(table.vertical, 'monitor', -1, 1, 51))
      RE(linescan(table.lateral, 'monitor', -2, 2, 51))

7. Do a linescan (:numref:`Section %s <linescan>`) of the ``dethor``
   motor to center the Mythen around the beam in the horizontal
   direction. 
  
   .. code-block:: python

      RE(linescan(dethor, 'mythen', -3, 3, 61))

   :numref:`See Section %s <dethor_align>`.

8. Perform the Mythen calibration scan:
  
   .. code-block:: python

      RE(mythen_calibration(-4, 1, 1001))

   This will set the bounds of the ``dir`` and ``refl`` ROIs and write
   a calibration report to the proposal folder.  It will also record
   the calibration parameters.

   :numref:`See Section %s <mythen_cal>`.

   .. admonition:: Question
      :class: attention

      What is the CHESS calibration?  This needs to be written.

9. Verify the alignment of beam, goniometer, and detector are
   acceptable by scanning the ``delta`` are and plotting the signal
   from both ``dir`` and ``refl``.  The ``dir`` plot should be narrower
   than **and** well centered in the ``refl`` plot.

   .. code-block:: python

      RE(linescan(delta, 'mythen', -0.15, 0.15, 61))

You are now ready for sample alignment.

Sample alignment strategy
-------------------------

A sample for XRR is usually a large, flat wafer.  The correct
alignment has the sample surface parallel to the beam path and at a
height such that it blocks half the beam.  With that alignment, the
center of the beam will be on the center of the sample as the incident
angle changes and the beam will spread symmetrically over the length
of the sample.

.. todo:: Need example screenshots of both sample alignment scans.

1. Start by aligning the sample vertically.

   .. code-block:: python

      RE(sample_vertical())

   This will run a linescan (:numref:`Section %s <linescan>`) of
   ``samplez`` against the signal in direct beam ROI then fit an error
   function to the measurement to find the position where the sample
   blocks half the beam.  That position will be defined as 0 of
   ``samplez`` by setting the EPICS offset accordingly.

2. Then align the pitch of the sample by a linescan (:numref:`Section
   %s <linescan>`) of ``eta`` against the signal in direct beam
   ROI.  


   .. code-block:: python

      RE(sample_pitch())

   Do an appropriate analysis (more discussion below) to find the zero
   of ``eta``.  Move to that position and define it as 0 by setting
   the EPICS offset accordingly.

3. Iterate those two steps as needed.

The interpretation of the pitch scan is a bit subtle.  In the case of
a very rough surface, the correct choice for ``eta`` will be very close
to the peak of the measured scan.

However, in the case of a very smooth sample, the total external
reflection will be intense enough that the structure near the peak
will be such that the maximum intensity is not necessarily the proper
0 of ``eta``.  In that case, a more elaborate analysis is required.

.. todo:: Fully explain the smooth sample algorithm once it is
          implemented in code.


