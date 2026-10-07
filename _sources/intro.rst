..  
   This document was developed primarily by a NIST employee. Pursuant
   to title 17 United States Code Section 105, works of NIST employees
   are not subject to copyright protection in the United States. Thus
   this repository may not be licensed under the same terms as Bluesky
   itself.

   See the LICENSE file for details.

.. _intro:

Introduction to BMM
===================

BMM is NIST's :red:`B`\ eamline for :red:`M`\ aterials :red:`M`\ easurement.

At the unix command line, do this to start the Bluesky user
interface, |bsui|:

.. code-block:: bash

   cd ~/.ipython/profile_goniometer
   pixi run start



|bsui| is simply an `IPython shell <https://ipython.org/>`_
with some customizations specific to Bluesky.  On top of that, there
are a number of customizations specific to BMM.


In this user manual, there are chapters covering most of the chores
one will need to do at the beamline, including:

#. moving motors
#. making motor scans
#. making reflectivity and pole figure scans
#. troubleshooting common problems

TL;DR
-----


**Open/close the shutter**
   ``shb.open()`` and ``shb.close()``, see :numref:`{name}, Section {number} <shutters>`

**Sample alignment scans**
   Use the ``RE(linescan())`` command, see :numref:`{name}, Section {number} <linescan>`

**Cheatsheet**
   :numref:`Command cheatsheet, Section {number} <cheatsheet>`


Essential links
---------------

:NSLS-II:  https://www.bnl.gov/nsls2/
:BMM's page:  https://www.bnl.gov/nsls2/beamlines/beamline.php?r=6-BM
:Operations schedule:  https://www.bnl.gov/nsls2/schedule/
:Beamtime schedule:  https://scheduler.nsls2.bnl.gov/
:NSLS Status:  https://www.bnl.gov/nsls2/operating-status.php
:BMM at Github:  https://github.com/orgs/NSLS2/teams/bmm/repositories



.. _slack:

Slack and Data Access
~~~~~~~~~~~~~~~~~~~~~

At the beginning of your experiment, you will be invited to the BMM
Slack workspace.  There you can follow along with the progress of the
experiment in the ``#pass-123456-bmm channel``, where ``123456`` will
be the proposal number of your experiment.

There is also a channel called ``#pass-123456`` which is for use as a
discussion channel.

Throughout the course of the experiment, messages and figures will be
automatically posted to that channel.  This allows someone to keep
track of progress and to keep an eye on data quality without being
physically at the beamline.

.. _fig-slack:
.. figure:: _images/software/slack.png
   :target: _images/slack.png
   :width: 50%
   :align: center

   An example of messages and a picture of measured data posted to the
   beamline Slack channel.


BMM and Building 743
--------------------


BMM is on the south side of the NSLS-II building: `what3words:
///width.corrugated.support
<https://what3words.com/width.corrugated.support>`__

You should park at building 743 and enter through the main entrance
of 743.

.. _fig-sitemap:
.. figure:: _images/location/map.png
   :target: _images/map.png
   :width: 60%
   :align: center

   Route from the Main Gate to Building 743


Walk though the lobby to the doors that lead out onto the experimental
floor.  BMM is just across the walk way from the doors to the 743 lobby.

.. subfigure::  ABC
   :layout-sm: ABC
   :gap: 8px
   :subcaptions: above
   :name: fig-BMMlocation
   :class-grid: outline

   .. image:: _images/location/743lobby.jpg

   .. image:: _images/location/corridor.jpg

   .. image:: _images/location/BMMcontrolstation.jpg

   (Left) Approaching the floor through the lobby of
   Building 743. (Center) BMM is just across the corridor from the
   door to the 743 lobby. (Right) Walk past the diagonal support beam
   and head into the BMM control station




BMM's staff have offices on the outer hallway of Building 743.


.. _fig-lob3:
.. figure:: _images/location/LOB-3.png
   :target: _images/LOB-3.png
   :width: 90%
   :align: center

   Bruce's, Jean's and Vesna's offices in Building 743




Acknowledgements
----------------

Pride of place goes to the late Jimmy Biancarosa and to Don Abel, the
excellent technicians who helped build BMM and outfit it with kit
that works.  Truly nothing would have ever happened at BMM without them.

BMM's `Bluesky <https://blueskyproject.io/>`__ profile was mostly
written by Bruce.  But this would not have happened without the help
of several members of NSLS-II's DSSI program.  In particular, I want
to thank Dan Allan, Tom Caswell, Josh Lynch, Jakub Wlodek, Max
Rakitin, Dmitri Gavrilov, Stuart Campbell, Abby Giles, Garrett Bishof,
Nate Maytan, Matt Snyder, Oksana Ivashkevych, Ryan Jaskiel, AJ Sliger,
John Sinsheimer, and Jun Ma.  

Thanks to every BMM user |nd| being a BMM user means being a beta tester
for the beamline software!

Aside from the Bluesky ecosystem, BMM makes use of `lots of great
python tools
<https://speakerdeck.com/jakevdp/the-unexpected-effectiveness-of-python-in-science?slide=52>`__.
Matt Newville's `Larch <http://xraypy.github.io/xraylarch/>`__ is used
to process every XAS scan that gets measured and Matt's `lmfit
<https://lmfit.github.io/lmfit-py/>`__ is used for many alignment
chores.

This documentation project uses `Sphinx
<https://www.sphinx-doc.org/en/master/index.html>`__ and the lovely
`{book}theme
<https://sphinx-book-theme.readthedocs.io/en/latest/index.html>`__
from the `The Executable Book Project
<https://ebp.jupyterbook.org/>`__.  Appendices are numbered properly
using the ``appendix.py`` extension from
https://github.com/heig-tin-info/handout.

This manual uses a GitHub action to build and deploy `(see details
here)
<https://github.com/marketplace/actions/sphinx-docs-to-github-pages>`__
this document whenever a ``git push`` happens.  We are grateful to the
`UIBCDF <https://github.com/uibcdf/action-sphinx-docs-to-gh-pages>`__
developers for this continuous deployment capability.




A note about copyright
----------------------

This document and `the BlueSky data collection profile
<https://github.com/NSLS2/bmm-profile-collection>`__ it covers was
developed primarily by a NIST employee. Pursuant to title 17 United
States Code Section 105, works of NIST employees are not subject to
copyright protection in the United States. Thus this repository may
not be licensed under the same terms as Bluesky itself or its
documentation.

See the `LICENSE file
<https://github.com/NSLS2/bmm-profile-collection/blob/main/LICENSE>`__
for details.
