---
layout: post
title: "Let’s create an open-source project in Python from scratch"
date: "2020-02-06"
slug: "Let’s create an open-source project in Python from scratch"
description: "Are you able to code in Python but not able to create a Python package? Have you wondered how to create a package that can be easily contribute, maintained and used? Well, if those thoughts have ever plagued you then this post is just for you."
category:
  - views
  - featured
metatags:
  - python
  - open-source
  - Travis-CI
  - Flake8
  - PyPI
  - Pip
  - Linter
tags:
  - python
  - open-source
show_meta: true
comments: true
mathjax: true
gistembed: true
published: true
noindex: false
nofollow: false
---

In the summer of 2018, I had the privilege to create a plotting library in Ruby (Have patience we will get to Python soon) from the scratch, that is when I realised that creating a package in a programming language is quite different from simply using the language. You need to make sure that your code is **readable**, its working is **reproducible**, and **robust**, so that you can make changes to the code-base without affecting the prior functionality.

To solidify the software development concepts that I learned, I decided to use them with Python. In this post we will create a simple open-source Python package from scratch that should work with Python 2.7 and Python 3.

Let us name our package **sampkg** (named after the Greek god of sample packages). Our package is going to have a module, **sammod** (which is actually named after the Nordic god of sample modules).  
The module, will contain a class (**Samclass**) that will have a string (**samstring**) and a function that returns the string with “hello “ prepended (**sam\_inst\_func**) as instance attributes along with a integer(**counter** , which counts number of instances) as a class attribute.  
Along with _Samclass_, _sammod_ will also contain a function (**sam\_func**) that will that take a Samclass object and return a string containing natural logarithm of _counter_ (calculated using numpy) and a reversed _samstring._

Now that we have chalked out plans for our package, we will look into the software development aspect.

First, we will create a Project hosted on Github for sampkg (make sure you include .gitignore for python) and clone it.

We now will set up the package directory, followed by tests, code linters, Setup script, continuous integration and then we will put our nifty package on PyPI.

So we will create the package directory to look like this:

```
sampkg/  <-- In my case I cloned it to my user's home folder  
├── LICENSE  
├── README.md  
├── sampkg <-- our package  
│   ├── __init__.py <-- contains info about the package  
│   └── sammod <-- module in our package  
│       ├── __init__.py <-- imports the contents of the module  
│       └── sammod.py  
├── .gitignore
├── setup.py <- our setup script  
└── tests    <- we will put all our test here  
    └── sammod  
        ├── test_samclass.py  
        └── test_sam_func.py
```

Before we write any code, we need to plan out what that code is going to do, for that we will write tests that out code needs to pass.  
After all, standardised testing is the best measure of capability. /s

## Writing tests

We will be using **pytest** as our testing tool. First, we need to install pytest .

```
pip install pytest
```

Since we have a class and a function in the module sammod, we shall write two tests:
<script src="https://gist.github.com/pgtgrly/bfe97281cc8480020b6f72562e2d36e6.js"></script>
<script src="https://gist.github.com/pgtgrly/def44aed5dbceccb3e7619443cf2ed91.js"></script>
Make sure that the name of the files containing tests and the test functions start with “test\_”

We will run the tests from the project’s base directory.

```
 python -m pytest
```

{:.text-center img}
![All tests failing]({{ site.urlimg }}/media/2020-02-06-basic-open-source-python-package/testfail_0.png "All tests are failing!")

As expected, the tests are failing. As we still need to write the code to make them pass.
You might be wondering why we did not call ```pytest``` to run the tests, You can read about it [here](https://docs.pytest.org/en/latest/pythonpath.html#pytest-vs-python-m-pytest).

Let’s write the code for sammod:
<script src="https://gist.github.com/pgtgrly/01a06cf85186c87c4c10d2e5251a377c.js"></script>

And edit ~/ sampkg/sammod/\_\_init\_\_.py
<script src="https://gist.github.com/pgtgrly/0fc66d2ee33318f337f152e059c66f00.js"></script>

Running the tests again we get:

{:.text-center img}
![One test failing]({{ site.urlimg }}/media/2020-02-06-basic-open-source-python-package/testfail_1.png "One test is still failing!")

Seems like I have forgotten to reduce the counter when an object of Samclass is destructed.

Fixing the code:
<script src="https://gist.github.com/pgtgrly/ab3ea50e69f5921ed699c932142e6917.js"></script>
And running the tests again, we get:

{:.text-center img}
![All tests passing]({{ site.urlimg }}/media/2020-02-06-basic-open-source-python-package/testpass.png "All tests are passing!!")

Success!

## Linting your code

Why do you need to write beautiful code?  
To quote John Keats:

> A thing of beauty is a joy for ever:  
> Its loveliness increases; it will never  
> Pass into nothingness;

The same is true for your code. A well written code-base is easy to read, maintain and expand.  
However, beauty is in the eye of beholder. I might prefer to indent with a tab and you might prefer to indent with two spaces. But this will not only lead to inconsistencies in the project, but it will also lead to those nasty TabErrors.  
Hence, it is better to follow a fixed set of rules. Most of the projects follow the standard [PEP8](https://www.python.org/dev/peps/pep-0008/) guidelines.  
To check if our code is at par with the desired guidelines we will use a code-linter named Flake8.

To install flake8, simply type:

```
pip install flake8
```

Now we run it from the project base:

```
flake8 .
```

This will go through all the py files in the package and point out all the PEP8 violations.

{:.text-center img}
![Needs Linting]({{ site.urlimg }}/media/2020-02-06-basic-open-source-python-package/LinterOutput.png "This code needs cleaning!")

This is how our code looks like after cleaning our code.
<script src="https://gist.github.com/pgtgrly/4ed48f1ab768e0ca7d4e2674d1bb2c83.js"></script>
See, our code is much better and more readable now!

Alternatively, you can use flake8 with some text editors and get real-time feedback while typing your code.

{:.text-center img}
![Flake8 with Atom]({{ site.urlimg }}/media/2020-02-06-basic-open-source-python-package/LinterAtom.png "Flake8 integrated with Atom")

## Creating setup.py

Whenever a user downloads our package, we would like all the dependencies for the package to be installed (which is numpy in this case) and the tests to be run. We create a setup.py file to facilitate that.
<script src="https://gist.github.com/pgtgrly/cfd14e24e493a7c8f1dd099cb0a1bd03.js"></script>
What is the use of setup.py?  
Now the package can be installed in the active Python environment by using:

```
python setup.py install
```

It can be simply uninstalled by:

```
pip uninstall sampkg
```

We will also create a setup.cfg:

<script src="https://gist.github.com/pgtgrly/4f9c2262782fbbf66c397b871be3b9b6.js"></script>

So that we can run the tests when we type:

```
python setup.py test
```

You can go through the official Python documentation for creating the setup file [here](https://docs.python.org/3.7/distutils/setupscript.html).

## Continuous Integration
Since we are building an open source project, we can expect others interested in the project to be able to contribute to it. Before merging their pull requests, we need to be sure that their code changes integrate well with the existing codebase for different environments supported by us. This is where Continuous integration (CI) comes in.

As the name suggests, it continuously runs integration tests for every commit/pull request to your repository.
You can find a list of Continuous Integration services that you can use [here](https://github.com/ligurio/awesome-ci).

I will be using [Travis-CI](http://travis-ci.org/) , a continuous integration service which is free for open source projects on GitHub.


First, you need to authorise Travis-CI to access your Github account and select the repository that you want Travis-CI to build. Follow steps one to three[here][https://docs.travis-ci.com/user/tutorial/#to-get-started-with-travis-ci). Spoiler Alert! Make sure that you do not read step number four.

After selecting the repository it will trigger a build which will fail and gives this error:

{:.text-center img}
![Initial Travis failure]({{ site.urlimg }}/media/2020-02-06-basic-open-source-python-package/TravisFail0.png "Initial Travis failure") 

This is because we haven't pushed a file that will configure Travis-CI to our repository yet, so it reverts to a default config file.

You read step four, didn't you?  I am not angry, just disappointed.

Sigh, now you know that to instruct Travis-CI about the build specifications we need create a markup file named “.travis.yml”.
We will create the following file in the root of our project:

<script src="https://gist.github.com/pgtgrly/1b2ac06692e5c05dbb598bfa181b7081.js"></script>

This .yml file tells our continuous integration service, how to install our file, what scripts to run and what environments we want to test it for.

Now that we have written it, we commit the file to the repository. Travis-CI automatically picks up the commit and runs the integration test in the environments defined by you. Look at the Tests run!

{:.text-center img}
![Travis Tests running]({{ site.urlimg }}/media/2020-02-06-basic-open-source-python-package/TravisTestRunning.png "Travis Tests running")

It seems like our tests are failing for two environments, Python 2.7 and nightly builds.

![Travis failure]({{ site.urlimg }}/media/2020-02-06-basic-open-source-python-package/TravisFail1.png "Travis failure") 

Since, we want our package to work with the stable releases we will remove the "nightly" environment from our .travis.yml file.

<script src="https://gist.github.com/pgtgrly/d15367a54d06361391a40ab4afce7efe.js"></script>

However, we do want our package to work with Python 2.7. We need yo investigate the logs for this environment.
![Travis failure reason]({{ site.urlimg }}/media/2020-02-06-basic-open-source-python-package/TravisFail1Reason.png "Travis failure reason") 
It seems like type(self) within Samclass is returning "instance" instead of "Samclass" in Python 2.7.
After some effective Googling, I came across this [answer](https://stackoverflow.com/a/6667098). We need to explicitly inherit from "object" class.

Fixing the code:

<script src="https://gist.github.com/pgtgrly/ef62bc3fe8111988262503c0171bf3ba.js"></script>

Now that we have fixed the code we commit it to the repository and trigger another round of builds. 
![Travis Pass]({{ site.urlimg }}/media/2020-02-06-basic-open-source-python-package/TravisPass.png "Travis Pass") 

Rejoice! Everything is passing. 

Whenever we will push a new commit now, Travis-CI will inform you if it is successfully building in all the desired environments!

PS: You can also specify the OS and lot more, make sure to go through [their documentation](https://docs.travis-ci.com/user/languages/python/).

## Uploading your project on PyPI
We have worked really hard to create your project, now we need a way to distribute it so that people around the world can easily download it. We want the community to be able to download sampkg using ```pip install sampkg``` just like we easily downloaded pytest.

 To do so first we need to [create a PyPI account](https://pypi.org/account/register/).

After creating the account, we will pack our code for distribution. We can either use ```setuptools``` or ```wheel``` to do so. I have a crazy idea, we can use both!

```
pip install setuptools wheel

python setup.py sdist bdist_wheel
```

This will add two files (along with other artefacts) to a newly created **dist** folder:

* sampkg-0.1.0-py3-none-any.whl
* sampkg-0.1.0.tar.gz

What is difference between these two, [read that for yourself](https://stackoverflow.com/a/42226284).

Our code is now ready for distribution. To upload it on PyPI we will use **twine**

```
pip install twine
twine upload dist/*
```
After running the above commands you will be prompted to enter your PyPI credentials, after which you will receive a response similar to:

```
View at:
https://pypi.org/project/sampkg/0.1.0/
```

Congratulations! Your package can now be downloaded and used by anyone by using:
```
pip install sampkg
```

If you want to update your package and publish the changes in PyPI, update the version number in setup.py and run:

```
python setup.py sdist bdist_wheel
twine upload --skip-existing dist/*
```

You can also update the package you downloaded using pip by:

```
pip install --upgrade sampkg
```

## What next?
If you have been following this blogpost, then your package would be look something like this:

```
├── LICENSE
├── README.md
├── dist
│   ├── sampkg-0.1.0-py3-none-any.whl
│   └── sampkg-0.1.0.tar.gz
├── sampkg
│   ├── __init__.py
│   └── sammod
│       ├── __init__.py
│       └── sammod.py
├── sampkg.egg-info
│   ├── PKG-INFO
│   ├── SOURCES.txt
│   ├── dependency_links.txt
│   ├── requires.txt
│   └── top_level.txt
├── .gitignore
├── .travis.yml
├── setup.cfg
├── setup.py
└── tests
    └── sammod
        ├── test_sam_func.py
        └── test_samclass.py
```
I have included dist and .egg-info for your viewing pleasure, they will (and should) be ignored by you .gitignore file when you will commit your changes.

Now that you have been familiarised to the tools you need to create an open-source Python project. What are you waiting for? Go create the next disruptive package!

Make sure to tweet to me[@pgtgrly](https://twitter.com/pgtgrly), if this post helped you or if you have any suggestions. I would love to hear from you!

