tess-ephem
==========


**Where are Solar System objects located in TESS FFI data?**

|pypi| |pytest| |black| |flake8| |mypy|

.. |pypi| image:: https://img.shields.io/pypi/v/tess-ephem
                :target: https://pypi.python.org/pypi/tess-ephem
.. |pytest| image:: https://github.com/SSDataLab/tess-ephem/workflows/pytest/badge.svg
.. |black| image:: https://github.com/SSDataLab/tess-ephem/workflows/black/badge.svg
.. |flake8| image:: https://github.com/SSDataLab/tess-ephem/workflows/flake8/badge.svg
.. |mypy| image:: https://github.com/SSDataLab/tess-ephem/workflows/mypy/badge.svg

``tess-ephem`` is a user-friendly package which enables users to compute the positions of Solar System objects -- asteroids, comets, and planets --
in the data archive of NASA's TESS Space Telescope.

Installation
------------

.. code-block:: bash

    python -m pip install tess-ephem


Example use
-----------

tess-ephem allows you to search the entire archive of TESS FFI's for the presence
of a known minor planet, and obtain the result as a Pandas DataFrame. The output pixel coordinates (column and row) follow the TESS convention, with (1,1) being the middle of the pixel in the lower left corner of the FFI. For example:

.. code-block:: python

    >>> from tess_ephem import ephem
    >>> ephem("Sedna")
               sector  camera  ccd       column          row     tdb-ut  ...      dec    vmag  hmag  sun_distance  obs_distance  sto_angle
    time                                                                 ...                                                              
    2458438.5       5       1    4  1543.104021  1102.948351  69.182738  ...  7.64961  20.803  1.49     84.947536     83.981060     0.1466
    2458439.5       5       1    4  1544.978969  1103.000701  69.182758  ...  7.64724  20.802  1.49     84.946760     83.978870     0.1435
    2458440.5       5       1    4  1546.866007  1103.027415  69.182777  ...  7.64500  20.801  1.49     84.945985     83.977335     0.1412
    2458441.5       5       1    4  1548.750885  1103.040242  69.182797  ...  7.64284  20.801  1.49     84.945209     83.976334     0.1397
    2458442.5       5       1    4  1550.627199  1103.044020  69.182818  ...  7.64074  20.801  1.49     84.944433     83.975802     0.1393
    ...           ...     ...  ...          ...          ...        ...  ...      ...     ...   ...           ...           ...        ...
    2460254.5      71       2    4  1984.578578  1003.555888  69.182570  ...  8.36089  20.750  1.49     83.592740     82.661771     0.2440
    2460255.5      71       2    4  1984.808484  1001.737047  69.182584  ...  8.35803  20.749  1.49     83.592017     82.656607     0.2347
    2460256.5      71       2    4  1985.037223   999.911091  69.182599  ...  8.35517  20.747  1.49     83.591294     82.651920     0.2257
    2460257.5      71       2    4  1985.258387   998.084718  69.182615  ...  8.35235  20.745  1.49     83.590572     82.647750     0.2168
    2460258.5      71       2    4  1985.462457   996.279638  69.182631  ...  8.34964  20.744  1.49     83.589849     82.644149     0.2083

    [75 rows x 14 columns]


The ``time`` is in the UT scale. To convert this to the TDB timescale, the column ``tdb-ut`` (offset in seconds) should be used.

You can also obtain the ephemeris for one or more specific times
by passing the ``time`` parameter:

.. code-block:: python

    >>> ephem("Sedna", time="2018-11-21 17:35:00")
                             sector  camera  ccd       column          row     tdb-ut  ...      dec    vmag  hmag  sun_distance  obs_distance  sto_angle
    time                                                                               ...                                                              
    2018-11-21 17:35:00.000       5       1    4  1553.858404  1103.035027  69.182854  ...  7.63721  20.802  1.49      84.94309     83.975894     0.1409

    >>> from astropy.time import Time
    >>> ephem("Sedna", time=Time([2458441.5,2460258.5], format='jd'))
               sector  camera  ccd       column          row     tdb-ut  ...      dec    vmag  hmag  sun_distance  obs_distance  sto_angle
    time                                                                 ...                                                              
    2458441.5       5       1    4  1548.750885  1103.040242  69.182797  ...  7.64284  20.801  1.49     84.945209     83.976334     0.1397
    2460258.5      71       2    4  1985.462457   996.279638  69.182631  ...  8.34964  20.744  1.49     83.589849     82.644149     0.2083


Orbital elements can be obtained by passing the ``orbital_elements=True`` parameter. The function returns the average orbital elements of the target during the queried time. Perihelion distance is in AU and orbital inclination is in degrees.

.. code-block:: python

    >>> df_ephem, orbital_elements = ephem("Sedna", time="2018-11-21 17:35:00", orbital_elements=True)
    >>> orbital_elements
    {'perihelion_distance': 76.13306961191206, 'eccentricity': 0.8413469227223953, 'orbital_inclination': 11.929585162007143}


You can alternatively obtain the ephemeris during a specific sector by passing 
the ``sector`` parameter:

.. code-block:: python

    >>> ephem("Sedna", sector=70)
               sector  camera  ccd       column          row     tdb-ut  ...      dec    vmag  hmag  sun_distance  obs_distance  sto_angle
    time                                                                 ...                                                              
    2460208.5      70       4    2  1965.994639  1827.049672  69.182409  ...  8.49483  20.815  1.49     83.626009     83.171983     0.6184
    2460209.5      70       4    2  1966.304467  1826.442414  69.182402  ...  8.49248  20.814  1.49     83.625285     83.156153     0.6137
    2460210.5      70       4    2  1966.639579  1825.743198  69.182395  ...  8.48989  20.813  1.49     83.624561     83.140210     0.6085
    2460211.5      70       4    2  1966.995717  1824.975902  69.182389  ...  8.48711  20.812  1.49     83.623838     83.124461     0.6029
    2460212.5      70       4    2  1967.363962  1824.160062  69.182383  ...  8.48421  20.811  1.49     83.623114     83.108974     0.5970
    2460213.5      70       4    2  1967.743247  1823.299432  69.182377  ...  8.48120  20.810  1.49     83.622391     83.093770     0.5908
    2460214.5      70       4    2  1968.127856  1822.402268  69.182372  ...  8.47812  20.809  1.49     83.621667     83.078862     0.5845
    2460215.5      70       4    2  1968.516359  1821.470633  69.182367  ...  8.47498  20.808  1.49     83.620944     83.064264     0.5779
    2460216.5      70       4    2  1968.909479  1820.507912  69.182363  ...  8.47178  20.806  1.49     83.620220     83.049995     0.5712
    2460217.5      70       4    2  1969.301139  1819.520671  69.182359  ...  8.46856  20.805  1.49     83.619497     83.036082     0.5644
    2460218.5      70       4    2  1969.690629  1818.514364  69.182355  ...  8.46533  20.804  1.49     83.618773     83.022562     0.5575
    2460219.5      70       4    2  1970.070096  1817.504775  69.182353  ...  8.46215  20.803  1.49     83.618050     83.009481     0.5504
    2460220.5      70       4    2  1970.413447  1816.527398  69.182350  ...  8.45920  20.802  1.49     83.617326     82.996774     0.5435
    2460221.5      70       4    2  1970.685594  1815.593040  69.182349  ...  8.45668  20.800  1.49     83.616603     82.983681     0.5369
    2460222.5      70       4    2  1970.945096  1814.590453  69.182347  ...  8.45415  20.799  1.49     83.615879     82.969891     0.5301
    2460223.5      70       4    2  1971.228110  1813.505068  69.182347  ...  8.45140  20.798  1.49     83.615156     82.956113     0.5228
    2460224.5      70       4    2  1971.528584  1812.361144  69.182347  ...  8.44849  20.796  1.49     83.614433     82.942589     0.5152
    2460225.5      70       4    2  1971.840095  1811.172397  69.182347  ...  8.44547  20.795  1.49     83.613709     82.929377     0.5074
    2460226.5      70       4    2  1972.160145  1809.944650  69.182348  ...  8.44236  20.794  1.49     83.612986     82.916495     0.4993
    2460227.5      70       4    2  1972.484084  1808.682404  69.182350  ...  8.43919  20.792  1.49     83.612263     82.903956     0.4911
    2460228.5      70       4    2  1972.811565  1807.392809  69.182352  ...  8.43597  20.791  1.49     83.611539     82.891774     0.4827
    2460229.5      70       4    2  1973.139007  1806.076605  69.182354  ...  8.43272  20.790  1.49     83.610816     82.879968     0.4742
    2460230.5      70       4    2  1973.467494  1804.738876  69.182358  ...  8.42944  20.788  1.49     83.610093     82.868566     0.4657
    2460231.5      70       4    2  1973.791305  1803.387890  69.182361  ...  8.42617  20.787  1.49     83.609370     82.857609     0.4570
    2460232.5      70       4    2  1974.102222  1802.037747  69.182365  ...  8.42297  20.785  1.49     83.608646     82.847150     0.4483


When passing the ``sector`` parameter, the ``time_step`` is by default 1 day. 
This can be changed as follows:

.. code-block:: python

    >>> ephem("Sedna", sector=70, time_step=0.1)
               sector  camera  ccd       column          row     tdb-ut  ...       dec       vmag  hmag  sun_distance  obs_distance  sto_angle
    time                                                                 ...                                                                  
    2460207.6      70       4    2  1965.698467  1827.543203  69.182416  ...  8.496977  20.815973  1.49     83.626660     83.185519   0.622423
    2460207.7      70       4    2  1965.734658  1827.487317  69.182415  ...  8.496720  20.816002  1.49     83.626588     83.184049   0.621956
    2460207.8      70       4    2  1965.769986  1827.432018  69.182415  ...  8.496468  20.816045  1.49     83.626515     83.182573   0.621497
    2460207.9      70       4    2  1965.804411  1827.377187  69.182414  ...  8.496221  20.816058  1.49     83.626443     83.181091   0.621046
    2460208.0      70       4    2  1965.837889  1827.322705  69.182413  ...  8.495980  20.816000  1.49     83.626370     83.179599   0.620600
    ...           ...     ...  ...          ...          ...        ...  ...       ...        ...   ...           ...           ...        ...
    2460233.0      70       4    2  1974.248380  1801.371667  69.182368  ...  8.421430  20.785000  1.49     83.608285     82.842110   0.444000
    2460233.1      70       4    2  1974.275695  1801.240320  69.182368  ...  8.421134  20.784886  1.49     83.608212     82.841115   0.443137
    2460233.2      70       4    2  1974.302128  1801.109815  69.182369  ...  8.420844  20.784707  1.49     83.608140     82.840122   0.442275
    2460233.3      70       4    2  1974.327659  1800.980176  69.182369  ...  8.420560  20.784486  1.49     83.608068     82.839129   0.441414
    2460233.4      70       4    2  1974.352268  1800.851430  69.182370  ...  8.420282  20.784243  1.49     83.607995     82.838136   0.440555

    [259 rows x 14 columns]
