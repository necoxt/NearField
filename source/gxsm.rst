GXSM
^^^^^^^^

The `GXSM <http://gxsm.sourceforge.net/>`_ is the Gnome X Scanning Microscopy project - a general-purpose, open-source, hardware-independent SPM control software proposed by Percy Zahl et al. in 2003. It applies to a wide range of SPM instruments, such as STM, AFM, SNOM, etc. In addition to SPM, GXSM is suitable for any 2D scanning measurement technique and complex spectroscopic tasks such as scanning tunneling spectroscopy. The GXSM software is distributed under the GNU General Public License and can be downloaded via the Internet and recompiled by modifying the source program as needed. It provides a suitable software environment for home-built scanning probe microscopes.

The latest documentation on GXSM can be found at `GXSM 3.0 <https://sourceforge.net/p/gxsm/svn/HEAD/tree/trunk/Gxsm-3.0-Manual/Gxsm-3.0-Manual.pdf>`_.

.. note:: 
    Currently (September 2021) GXSM 3.0 is only supported for installation on Ubuntu systems (not tested on other Linux distributions). There is a third-party version of `GXSM 2.0 <https://sourceforge.net/projects/gxsm4w/>`_ on windows, but MK3-PLL hardware is not supported.

Get started with GXSM 3.0
---------

If you are only considering using GXSM and not involved in development or customizing new features, please use APT to install, otherwise please clone the latest `repository <https://sourceforge.net/p/gxsm/svn/HEAD/tree/trunk/>`_ via SVN and compile locally.

Install GXSM via APT
>>>>>>>>>

First you have to add the PPA (Personal Package Archive) hosted on launchpad:

>>> sudo apt-add-repositry ppa:totto/gxsm

On Ubuntu 16.04 LTS you will need to add also the PPA from the Gnome3-team to obtain recent version of the GTK+3.0 libaries.

>>> sudo apt-add-repository ppa:gnome3-team/gnome3
>>> sudo apt-add-repository ppa:gnome3-team/gnome3-staging

Refresh you local database:

>>> sudo apt update

and install the GXSM-3.0 package via

>>> sudo apt install gxsm3 sranger-modules-std-dkms sranger-modulesmk23-dkms

which will not only install GXSM-3.0 but also the sranger packages.

Install GXSM via SVN
>>>>>>>>>

If you have previously installed GXSM via apt, please make sure to remove it first.

>>> sudo apt purge gxsm3

For Ubuntu 18.04 LTS and previous versions, please refer to Chapter 3.2 of the `manual <https://sourceforge.net/p/gxsm/svn/HEAD/tree/trunk/Gxsm-3.0-Manual/Gxsm-3.0-Manual.pdf>`_. The following procedure is tested on Ubuntu 20.04 LTS, refered to this `discussion <https://sourceforge.net/p/gxsm/discussion/297458/thread/cb1e30a6/>`_:

1) Install all these packages:
   
   >>> sudo apt install subversion intltool yelp-tools gtk-doc-tools gnome-common libgail-3-dev libnetcdf-dev libnetcdf-cxx-legacy-dev libfftw3-dev libgtk-3-0 libgtksourceview-3.0-dev gsettings-desktop-schemas-dev python-gobject-2-dev libgtksourceviewmm-3.0-dev libquicktime-dev libglew-dev freeglut3-dev libgl1-mesa-dev libopencv-core-dev libopencv-features2d-dev libopencv-highgui-dev libopencv-objdetect-dev libnlopt-dev libglm-dev fonts-freefont-ttf libsoup2.4-dev libgsl-dev libpython3.8-dev gcc perl make g++ libpopt-dev python-gobject python-gi-dev python-gobject-2-dev

2) Modify "pystate.h":
   
   Related to "libpython3.8-dev" there is a bug in the python-3.8 causing compile problems. To fix this 1st, edit (as root) this file to fix a wrong include path in this file "pystate.h":

   >>> cd /usr/include/python3.8/cpython
   >>> sudo vi pystate.h
   ...
   // #include "cpython/initconfig.h" // does not exist, but this works:
   #include "initconfig.h" // this works
   ...

3) Download the latest verion of the gxsm3 source code:
   
   >>> svn checkout https://svn.code.sf.net/p/gxsm/svn/trunk/Gxsm-3.0 gxsm-svn

4) Try to compile gxsm3:
   
   >>> cd gxsm-svn
   >>> ./autogen.sh
   >>> make -j8

5) If there is no error try:
   
   >>> sudo make install


Run GXSM 3.0
---------

To start from a command line

>>> sudo gxsm3

If the computer is not connected to the MK3-PLL controller, the GXSM panel will not pop up. If you want to run without hardware, enter

>>> sudo gxsm3 -h no

Please refer to the GXSM 3.0 manual for details on the panels and features.

Auto-approach
---------

The main idea of the auto approach is to apply a delta wave to the z-axis of the piezoelectric tube before each round of stepping and let the tip search the sample surface. If searched, then turn on the PID to lock to the set tunneling current level; if not searched within the elongation range, then return and proceed to the next step. The voltage applied to (a) the Z-axis of the piezoelectric tube during the search and (b) the signal output to the translation stage controller is shown below

.. image:: ../images/Auto-approach.png
    :width: 400 px
    :alt: autoapproach

The auto-approach is established on the premise that the distance per roundstep is shorter than the distance explored; otherwise, there is a risk of hitting theprobe.