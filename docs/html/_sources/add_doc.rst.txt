

Creating/Edit Documentation 
===========================

We use Sphinx to create documentation across all our products. Here is what you need to know to participate:

Python
------
You need to have Python on your system, as it is the engine of Sphinx. We recommend you follow our `Python Configuration and Usage`_ for setting up Python using miniconda. This documentation describes what a Python environment is.


Sphinx
------
This documentation was created using `sphinx <https://www.sphinx-doc.org/en/master/>`_ which is a python-based documentation tool originally facilitating the documentation of Python projects but now used for manual production in general, a big example being `ReadTheDocs <https://readthedocs.org>` which adds templates and version control. Sphinx has powerful ways of embedding documentation from python functions, youtube tutorials, images etc. There are graphical tools for previewing (e.g. sublime) but little in-house experience with them.

We have a sample environment for sphinx that works across a wide swath of our products. There is one catch, however, which is that sphinx works by introspection -- importing and interrogating the python package that is being documented. This means you the package and its dependencies have to be available. This makes it impossible for us to give out a single environment for sphinx.

There are several ways to approach this:

* You can use our generic but fairly full-featured `sphinx_env.py <https://github.com/CADWRDeltaModeling/CADWRDeltaModeling.github.io/blob/master/sphinx_env.yml>`_ as a start and append the project name that you are documenting to the dependencies section.
  
* For some larger programs such as SCHISM a sphinx environment is offered that covers one or more libraries plus the required sphinx. See BayDeltaSCHISM/sphinx_env.yml.

* There is a tools called conda-merge that you can use to fuse together env.yml files that you could use to combine the example sphinx environment with your projects recommended minimum environment. 


Running Sphinx
--------------

There isn't much to this:

* We typically install sphinx using conda
* The build is usually very simple. It is done from the /docsrc directory on a conda command prompt and done either with the commands `make html` or `doall.bat`. If you see `doall.bat` that is the one to use.
* For new projects, there are some configuration steps, which are best tackled using the Sphinx online documentation and refering to another project. 


ReStructured Text Markdown
--------------------------

The actual text you will be editing is often in .rst files inside a directory called .docsrc. 
Format and structure of documentation in Sphinx is based on 
`ReStructured Text <https://www.sphinx-doc.org/en/master/usage/restructuredtext/basics.html>`_ 
markdown language, which provides for things like section headers, table of contents and cross-references. 
Tutorials and anuals are widely available

Git and GitHub
--------------
--------------
The documentation is delivered through Git and GitHub. The basic actions you would need to be able to do are:
#. Clone the repository containing the documentation.
#. Edit and add files (text and figures) to the documentation directory, often called /docsrc.
#. Build the documetnation by launching sphinx in that directory (often a script called doall.bat or the command `make html`, generating html and other webe products in a directory called /docs
#. Go through the sequence that moves it back to GitHub: add locally, commit locally and "push".
#. The server side stuff is magic unless you are creating a new repo, in which case you should learn about GitHub Pages. In that case, for consistency please follow the general layout (docsrc and docs) and point to the same branch (master) as we do for other products.



