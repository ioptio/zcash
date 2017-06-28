.. Zcash documentation master file, created by
   sphinx-quickstart on Tue Jun 20 17:48:24 2017.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.


Zcash 1.0.9
=================================
    

What is Zcash?
--------------

`Zcash <https://z.cash/>`_ is an implementation of the "Zerocash" protocol.
Based on Bitcoin's code, it intends to offer a far higher standard of privacy
through a sophisticated zero-knowledge proving scheme that preserves
confidentiality of transaction metadata. Technical details are available
in our `Protocol Specification <https://github.com/zcash/zips/raw/master/protocol/protocol.pdf>`_.

This software is the Zcash client. It downloads and stores the entire history
of Zcash transactions; depending on the speed of your computer and network
connection, the synchronization process could take a day or more once the
blockchain has reached a significant size.

Security Warnings
-----------------

Before using, be sure to read the `privacy and security recommendations <privacy-security-recommendations.rst>`_.

Advanced users may read the `Advanced security warnings <security-warnings.rst>`_.

**Zcash is unfinished and highly experimental.** Use at your own risk.

Deprecation Policy
------------------

This release is considered deprecated 16 weeks after the release day. There
is an automatic deprecation shutdown feature which will halt the node some
time after this 16 week time period. The automatic feature is based on block
height and can be explicitly disabled.

Where do I begin?
-----------------

You can install the official Zcash client or find third-party wallets at https://z.cash/download.html

Need Help
---------

* Start with the `FAQ <https://z.cash/support/faq.html>`_
* Ask for help:
    
  * `Zcash forum <https://forum.z.cash/>`_
  * `Zcash Community chat <https://chat.zcashcommunity.com/>`_

Participation in the Zcash project is subject to a
`Code of Conduct <code_of_conduct.md>`_. Note that the Zcash forum and Zcash Community chat have adopted a related set of `Guidelines <https://forum.z.cash/faq>`_.

License
-------

For license information see the file `COPYING <COPYING>`_.




Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`

  
.. toctree::
    :maxdepth: 2
    :caption: Contents:

    security-warnings.rst
    amqp.rst
    authors.rst
