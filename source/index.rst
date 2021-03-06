.. Cosmic documentation master file, created by
   sphinx-quickstart on Wed Feb 27 00:51:11 2013.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Cosmic Spec
===========

.. warning::

    This document is unstable and incomplete. Use the Python documentation for now.

This document will describe the architecture of Cosmic and provide a strict specification that every implementation of Cosmic must follow. The canonical implementation is written in `Python <http://www.python.org/>`_. If you would like to get started with Cosmic, dive into the `Python documentation </docs/cosmic/python/latest/>`_ and use this document as a reference. If you would like to hack on Cosmic, this document is for you.

An integral part of Cosmic is our simple yet powerful serialization library, `Teleport </docs/teleport/spec/latest/>`_.

.. toctree::
   :maxdepth: 2
   :numbered:

   api
   models
   actions
   registry
