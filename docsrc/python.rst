.. _Python Configuration and Usage:

Python Configuration and Usage
===============================

Intro to Conda and Python environments
------------------------------------------

Most of our production libraries are deployed via conda. For brevity we begin with the recommended practice for those who are already familiar. Then we follow up with some details that may make it easy to do things like development. 

Bottom line for experienced users
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Use miniconda with environments

`Miniconda <https://docs.conda.io/en/latest/miniconda.html>`_ is a python installation that serves as both a very lean python plus a package manager for environments (background is given below). Installation is straightforward and similar to other apps. It is easy to install. You may be interested in the `libmamba <https://www.anaconda.com/blog/a-faster-conda-for-a-growing-community>`_ 
solver which is more efficient than the native one that comes with conda. 

Many of our larger projects such as BayDeltaSCHISM come with environment.yml files in their base directories, which list the preprequisite libraries you would need. For example, the BayDeltaSCHISM project provides a monolith *schism_environment.yml* that is known to successfully install and covers a lot of the tools used with SCHISM such as schimpy. 

Use the command below to create an environment (with name my_env_name)::

    conda env create -n my_env_name -f environment.yml

Use the command below to update the enviornment after changing the environment.yml file::

    conda env update -n my_env_name -f environment.yml

These are just suggestions, and note that the file name typically won't be exactly `environment.yml`. 
The distributed environment.yml files will get you going, but many of us 
tweak the distributed environments for our needs. 

Channel priorities
^^^^^^^^^^^^^^^^^^

We maintain our own conda channel in order to provide stability for a few libraries, 
but in a lot of cases you should set up conda priorities in this order::

    channels:
      - conda-forge
      - defaults
      - cadwr-dms


You can specifiy this in yaml. You can also do it in your conda configuration which is covered in the next section. 

This obviously is specific to our products (see next section). The main incompatibility we deal with is between the two major sources: "defaults" and "conda-forge". The defaults and conda-forge channels are not fully compatible, particularly for the numpy library. The problem is that they are based on different linear algebra libraries at the system level, and those do not play well with one another. This is a cascading problem for other libraries like gdal (geo spatial) that depend on numpy and have to "link" to the same runtime libraries as the numpy they depend on. This means that for a number of core tools, we need to pick our poison and the poison we pick currently is conda-forge because defaults doesn't seem well maintained. 

Channel priority strict
^^^^^^^^^^^^^^^^^^^^^^^^

If you can afford working with our channel recommendations, we recommend you set conda channel priority strict. 
If you are only using our products or are a member of the DWR modeling group you could do this globally in your base environment::

$ conda config --set channel_priority strict

Alternatively if you use conda already and don't want to adapt our policies 
globally you can set channel prioritiy per-environment::

$ conda activate target_env
$ conda config --env --set channel_priority strict
$ conda config --env --append channels cadwr-dms
$ conda config --env --prepend channels conda-forge


Note that the last two lines are not needed to be strict, but they are a great way to hard wire the channels so that you can do casual installs of new libraries without messing up
the channel order.

The reason for setting these preferences is to avoid the lack of persistence of channel priority during updates. Channel preferences with yaml files or -C installations do not stick. Setting them in the conda configuration makes them permanent.

Background and Details
-----------------------

The idea of conda environments starts out a bit difficult. As you adjust you may want more information. Here it is...

Python is a confederation of libraries, and the more powerful it gets the more the library versions have to be coordinated so that they work well together. This is what a package manager like conda does. In 2016, it was reasonable to want to find a "universal bundle" you could use for everything, a monolithic distribution like Anaconda that puts an enormous number of compatible libraries at your disposal. Fast-forward to 2023 and Python libraries have proliferated and coming up with a set that is fully compatible is a hard optimization problem. A good installer that accurately lists dependencies (without over or under-reaching) helps the "solve" but even still usage has gravitated towards the minimal miniconda plus a user specific environment so that you don't catch a lot of requirements and constraints from packages you don't actually use. The example environment below shows how to do this without hardwiring a lot of versions -- you still get a lot of help from the conda package manager getting things that fit together.

For best practices see this `Scipy tutorial <https://carpentries-incubator.github.io/introduction-to-conda-for-data-scientists/setup/>`_

Please also note that the new `libmamba solver <https://conda.github.io/conda-libmamba-solver/>`_  has worked well for us and might be worth exploring. It does a better job of reconciling library versions and it it is much faster.


Environment
^^^^^^^^^^^

What is an environment? A package manager?

In a nutshell, a package manager (miniconda) allows you to toggle between complete python setups that are optimized for particular uses with particular bunches of libraries. Your microconda python installation will come configured with a base environment, but you will definitely not want to touch it very much. Instead, create other environments that have unique bundles of libraries tuned for specific tasks.  You can have a number of these on your computer – perhaps targeting different use cases that require different and conflicting sets of libraries.

A miniconda base environment should be very stripped down so that it never breaks. This is what distinguishes it with Anaconda, an older product which is a giant and feature rich environment but that tends to break as you add in new components. With miniconda, as long as base is not corrupted, you can create and manage other environments with little potential for something going wrong and if something does go wrong you just replace the offending environment. On our end, we can provide specifications for complete environements in the form of yaml files that will provide everything you need for a set of tasks.

For some uses like SCHISM or DSM2 modeling we have set up recommended environments that not only fulfill the requirements of our pre- and post-processing scripts but also establish a typical analysis environment for modeliers. Use them if they help.
 
Developer installs: fast interaction with GitHub/Git
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If you are a developer or using a product (say vtools) that is fast moving and likely to lead to changes to that module, you may prefer a developer install rather than an ordinary conda install. This is probably a bad way to get your feet wet. However some problems with using conda when things are changing fast are:

* There is a lot of automatic machinery between a GitHub commit on a library and when it appears in our channel. That pipeline occasionally fails or slows.
* Not every project is on our conda channel. 
* Conda installation locations are buried and hard to access. This is particularly inconvenient if there are data or examples.
* Propagating changes you make in a conda installation back to GitHub is error prone.

A developer install is based on a clone from GitHub and the way that you update it is by pulling from GitHub. You clone the project yourself first, say off GitHub or Gitea and put it where ever you want. You are now using, developing and versioning all from the same copy. 

Developer installs are done with pip which is a package manager like conda.  Conda community decided not to compete with this method. Go to the parent directory of the project you are installing, which should have a file called setup.py in it. Then use this command::

  > pip install --no-deps -e .

Here pip is the name of the package manager, `--no-deps` says no dependencies (see below), -e stands for "editable" which means install in place and the dot (.) means "this directory". The main thing to explain here is the `--no-deps` part. If pip detects missing dependencies it will install them using pip. For instance, vtools depends on pandas, numpy, as well as a number of dependencies of dependencies and smaller libraries.  If it doesn't not detect the presence of these small libraries, pip will install them and "hijack" things you will later wish had been installed through conda.  You will end up with a mess between two package managers and have to throw away the environment and re-do it. 

Here are three hints to making it work:

#. Do your best to install dependencies first using conda. These are listed in the requires or install_requires list inside setup.py at the parent directory of the module. 
#. You can use the `--no-deps` flag to suppress the installation of dependencies. Note that if you are missing things, the module (maybe even the install?) may fail. 
#. You can install first in conda the ordinary way then follow with a developer install with `--no-deps`. This not only is allowed, it assures that conda has already detected and met any dependent library requirements. It seems redundant, but works very well.

Coding standards
----------------


Conda and Python version
^^^^^^^^^^^^^^^^^^^^^^^^

Please use conda (anaconda or miniconda) to setup and install python and its dependencies. Use Python 3.9.x as of 2023. 

Use git to clone to your local directory. Use a developer install (pip install --no-deps -e .) to install. Note that you should use pip judiciously and with the --no-deps flag to avoid pip invasively taking over dependencies..

Please adhere to `PEP-8 <https://www.python.org/dev/peps/pep-0008/>`_ which serves as the official style guide for Python. Both Spyder and VS Code provide hooks to autopep8 which can help with "linting" (auto correcting to these and other standards. For instance, please use these guidelines for style as they are a python standard:

* Use lower case consistently ( no exceptions for things like institutional names or common acronyms. So not: so_not_USGS ).
* CamelForClassNames is OK.
* Use numpydoc notation for function documentation (input output parameters). See the `example <https://numpydoc.readthedocs.io/en/latest/example.html>`_.
* Use Autopep8 (autopep8-PyPI) with your editor if possible.
* Use the autocrlf on Git::

    $ git config --global core.autocrlf true

All our github repos are at  https://github.com/CADWRDeltaModeling. 
Please ask for write permissions if you would like to submit code there. Forking and submitting pull requests are also fine.

Project/Module Structure
Python tools are expected to use python cookie cutter. https://cookiecutter.readthedocs.io/en/latest/. See Python New Project Template for details. 

Testing
-------

We use pytest, and GitHub action or Jenkins CI (See Internal CI platforms) for test automation. For routine development we recommend that you use unit tests and if you integrate them well we will add them to the CI suite pytest.

