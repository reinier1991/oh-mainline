odict
=====

Dictionary in which the *insertion* order of items is preserved (using an
internal double linked list). In this implementation replacing an existing 
item keeps it at its original position.

Internal representation: values of the dict:
::

    [pred_key, val, succ_key]

The sequence of elements uses as a double linked list. The ``links`` are dict
keys. ``self.lh`` and ``self.lt`` are the keys of first and last element 
inseted in the odict. In a C reimplementation of this data structure, things 
can be simplified (and speed up) a lot if given a value you can at the same 
time find its key. With that, you can use normal C pointers.

Memory used (Python 2.5)
------------------------

- set(int): 28.2 bytes/element

- dict(int:None): 36.2 bytes/element

- odict(int:None): 102 bytes/element

Performance
-----------

When running the benchmark script, you get results similar to the one below
on recent common hardware.

Adding and deleting ``dict`` objects.

+----------------+--------------------+
| Add 1000       | 0.000653028488159  |
+----------------+--------------------+
| Delete 1000    | 0.000339031219482  |
+----------------+--------------------+
| Add 10000      | 0.00774908065796   |
+----------------+--------------------+
| Delete 10000   | 0.00406503677368   |
+----------------+--------------------+
| Add 100000     | 0.130412101746     |
+----------------+--------------------+
| Delete 100000  | 0.0517508983612    |
+----------------+--------------------+
| Add 1000000    | 3.17614817619      |
+----------------+--------------------+
| Delete 1000000 | 0.564585924149     |
+----------------+--------------------+

Adding and deleting ``odict`` objects.

+----------------+--------------------+
| Add 1000       | 0.0096800327301    |
+----------------+--------------------+
| Delete 1000    | 0.00276303291321   |
+----------------+--------------------+
| Add 10000      | 0.107849836349     |
+----------------+--------------------+
| Delete 10000   | 0.0282921791077    |
+----------------+--------------------+
| Add 100000     | 1.05564808846      |
+----------------+--------------------+
| Delete 100000  | 0.297379016876     |
+----------------+--------------------+
| Add 1000000    | 16.451695919       |
+----------------+--------------------+
| Delete 1000000 | 3.03641605377      |
+----------------+--------------------+

Relation ``dict:odict`` of creating and deleting.


+---------------------------+-----------+
| creating 1000 objects     | 1:14.823  |
+---------------------------+-----------+
| deleting 1000 objects     | 1:8.1497  |
+---------------------------+-----------+
| creating 10000 objects    | 1:13.917  |
+---------------------------+-----------+
| deleting 10000 objects    | 1:6.9598  |
+---------------------------+-----------+
| creating 100000 objects   | 1:8.0947  |
+---------------------------+-----------+
| deleting 100000 objects   | 1:5.7463  |
+---------------------------+-----------+
| creating 1000000 objects  | 1:5.1797  |
+---------------------------+-----------+
| deleting 1000000 objects  | 1:5.3781  |
+---------------------------+-----------+

Usage
-----

Import and create ordered dictionary.
::

    >>> from odict import odict
    >>> od = odict()

type conversion to ordinary ``dict``. This will fail.
::

    >>> dict(odict([(1, 1)]))
    {1: [nil, 1, nil]}

The reason for this is here -> http://bugs.python.org/issue1615701

The ``__init__`` function of ``dict`` checks wether arg is subclass of dict,
and ignores overwritten ``__getitem__`` & co if so.

This was fixed and later reverted due to behavioural problems with ``pickle``.

Use one of the following ways for type conversion.
::

    >>> dict(odict([(1, 1)]).items())
    {1: 1}
    
    >>> odict([(1, 1)]).as_dict()
    {1: 1}

It is possible to use abstract mixin class ``_odict`` to hook another dict base
implementation. This is useful i.e. when persisting to ZODB. Inheriting from
``dict`` and ``Persistent`` at the same time fails.
::

    >>> from persistent.dict import PersistentDict 
    >>> class podict(_odict, PersistentDict):
    ...     def _dict_impl(self):
    ...         return PersistentDict

Requires
-------- 

- Python 2.4+

Changes
=======

Version 1.4.3
-------------

- get rid of annoying warning about "global" usage in bench.py
  [jensens, 2011-09-20]

Version 1.4.2
-------------

- More ``copy`` testing

- Add ``has_key`` to odict
  rnix, 2010-12-18

Version 1.4.1
-------------

- Fix release, README.rst was missing, added MANIFEST.in file to include it
  jensens, 2010-11-29

Version 1.4.0
-------------

- Full test coverage
  chaoflow, rnix, 2010-08-17

- Code cleanup and optimizing
  chaoflow, rnix, 2010-08-17

Version 1.3.2
-------------

- Access ``dict`` API providing class via function ``_dict_impl()`` and
  provide odict logic as abstract base class ``_odict``.
  rnix, 2010-07-08

Version 1.3.1
-------------

- Add test for bool evaluation
  rnix, 2010-04-21

Version 1.3.0
-------------

- Fix access to ``odict.lt`` and ``odict.lh`` properties. Now it's possible
  to overwrite ``__setattr__`` and ``__getattr__`` on ``odict`` subclass
  without hassle.
  rnix, 2010-04-06

- Add ``sort`` function to odict.
  rnix, 2010-03-03

Version 1.2.6
-------------

- Make ``odict`` serialize and deserialize properly
  gogo, 2010-01-12

Version 1.2.5
-------------

- Add ``as_dict`` function. Supports type conversion to ordinary ``dict``.
  rnix, 2009-12-19

- Add benchmark script
  rnix, 2009-12-19

Version 1.2.4
-------------

- Do not check for ``key in self`` on ``__delitem__``, ``KeyError`` is raised
  properly anyway. Huge Speedup!
  rnix, jensens, 2009-12-18

Version 1.2.3
-------------

- Move tests to seperate file and make egg testable with 
  ``python setup.py test``.
  rnix, 2009-12-07

- improve ``lt`` and ``lh`` properties to make ``odict`` work with 
  ``copy.deepcopy``.
  rnix, 2009-12-07

Version 1.2.2
-------------

- Use try/except instead of ``__iter__`` in ``__setitem__`` to determine if
  value was already set.
  rnix, 2009-07-17

Version 1.2.1
-------------

- Add missing ``__len__`` and ``__contains__`` functions.
  rnix, 2009-03-17
   
Version 1.2.0
-------------

- eggified
  rnix, 2009-03-17

Version < 1.2
-------------

- http://code.activestate.com/recipes/498195/
  bearophile, 2006-10-12
 
Contributors
============
  
- bearophile

- Robert Niederreiter <rnix@squarewave.at>

- Georg Bernhard <g.bernhard@akbild.ac.at>

- Florian Friesdorf <flo@chaoflow.net>

under the `Python Software Foundation License 
<http://www.opensource.org/licenses/PythonSoftFoundation.php>`_.
