# Smalltalk Fit

![Maintenance Status](https://img.shields.io/badge/maintenance-active-green.svg)

Smalltalk Fit is a Smalltalk implementation of Ward Cunningham's
[Framework for Integrated Tests](http://fit.c2.com),
[Fitnesse](http://www.fitnesse.org), and Rick Mugridge's
[FitLibrary](http://fitlibrary.sourceforge.net).

This version contains the equivalent features of:
* fit-java-1.1
* fitnesse-20121220
* FitLibrary-2.0

# License

    Copyright (C) 2004-2013 Randy Coulman

    This program is free software; you can redistribute it and/or modify
    it under the terms of the GNU General Public License as published by
    the Free Software Foundation; either version 2 of the License, or
    (at your option) any later version.

    This program is distributed in the hope that it will be useful,
    but WITHOUT ANY WARRANTY; without even the implied warranty of
    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
    GNU General Public License for more details.

    You should have received a copy of the GNU General Public License along
    with this program; if not, write to the Free Software Foundation, Inc.,
    51 Franklin Street, Fifth Floor, Boston, MA 02110-1301 USA.

See the copyright tab in the RB, the 'notice' property of this
package, or the License.txt file on GitHub.

I am using the GPL because this software is considered a derivative
work of the original Fit and FitLibrary implementations in Java, and
they are both licensed under the GPL, so I must use it.  The use of
the GPL in Smalltalk is complicated, and I am not a lawyer.  My
intention is that you may write Fit/FitLibrary tests for your software
without causing your software to come under the terms of the GPL.  The
test fixture code you write necessarily comes under the GPL, because
it likely inherits from base classes provided in the library.
However, I consider there to be a "firewall" between the fixture code
and the domain code as long as you keep them in separate packages.  I
believe that this is consistent with the original Java implementation,
where your domain code would be in a separate .jar file from your test
fixture code.  If you have any questions about this, please contact me
(see below for contact information).

# Introduction

Smalltalk Fit's primary home is the
[Cincom Public Store Repository](http://www.cincomsmalltalk.com/CincomSmalltalkWiki/Public+Store+Repository).
Check there for the latest version.  It is also on
[GitHub](https://github.com/randycoulman/SmalltalkFit).

SmalltalkFit was developed in VW 7.9.1, but is compatible with VW 7.7
and later.  If you find any incompatibilities with VW 7.7 or later,
let me know (see below for contact information) or file an issue on
GitHub.

# Contents

FitBundle contains the following packages:

* `Fit`: The Smalltalk port of Fit and Fitnesse.
* `FitLibrary`/`FitLibrary-Internals`: The Smalltalk port of FitLibrary.
* `Fit-Tests`: The unit tests for Fit.
* `FitLibrary-Tests`: The unit tests for FitLibrary.
* `Fit-Examples`/`Fit-Examples-Tests`: Support code for the examples.
* `Fit-SpecFixtures`/`Fit-SpecFixtures-Tests`: Support code for the Fit
  specifications.
* `FitLibrary-SpecFixtures`: Support code for running the FitLibrary
  specifications.
* `FitLibrary-Tutorial`: Support code for running the FitLibrary
  tutorial examples.
* `FitLibrary-UserGuideFixtures`: Support code for running the examples
  in the FitLibrary User's Guide.

There is also a Smalltalk port of the examples in Rick Mugridge and
Ward Cunningham's book,
[Fit for Developing Software"](http://www.amazon.com/Fit-Developing-Software-Framework-Integrated/dp/0321269349):

* `Fit-Book-DomainCode`/`Fit-Book-Fixtures`: Support code for the
  examples.
* `Fit-Book-RPS`/`Fit-Book-RPS-Tests`/`Fit-Book-RPS-Fixtures`: Support code
  for an updated version of the Rent-a-Party Software example.

Finally, there is a Smalltalk port of Gojko Adzic's
[Fixture Gallery](http://gojko.net/fitnesse/fixturegallery):

* `FixtureGallery-DomainCode`/`FixtureGallery-Fixtures`: Support code for
  the fixture gallery.

If you just want to use Fit or Fitnesse, all you need is the `Fit`
package. If you also want to use FitLibrary, you'll also need `FitLibrary`
and `FitLibrary-Internals`.  You can refer to any of the other
packages for examples of usage.  If you plan to do any development on
this project or to contribute in any way, then I recommend loading
`FitBundle`.

# Fit

The Java Fitnesse implementation has forked the Fit source code to add
some enhancements.  Some of the changes are incompatible with the
current Fit specification.  In particular, Fitnesse uses CSS to
"annotate" table cells with colors and labels that indicate test
status, whereas Fit uses embedded markup in the HTML.  Also, FItnesse
allows much more flexibility in mapping fixture names in tables to
fixture classes than the Fit spec allows.

In order to accommodate these differences, SmalltalkFit uses the Fit
implementation when running the Fit batch test runner (see the
`FileRunner` class), and uses the Fitnesse implementation when running
the Fitnesse test server (see the `FitServer` class).

# FitLibrary

This is an almost-complete implementation of FitLibrary 2.  Missing
features include:

* Dot Graphics
* Arithmetic expressions in table cells
* Recording dynamic variables to file
* Full Unicode support
* The "define actions slowly" action

In order to use the DefinedActions feature where the actions are
specified on a different page, it is necessary to tell the Smalltalk
FitLibrary code where to find your fitnesse implementation, especially
when running from a development image.  There are two options:

1. On platforms which support symbolic links, make a symlink from
FitNesseRoot in your image's working directory to the FitNesseRoot
directory of your Fitnesse installation.

2. Send DefineActions class>>fitnesseDirectory: with the path to your
fitnesse installation (the parent directory of your FitNesseRoot
directory).

If you are upgrading from the previous version of FitLibrary, you'll
need to adjust for the following incompatibilities:

* Any suites that use FitLibrary must define `TEST_RUNNER` to be
  fitlibrary.suite.FitLibraryServer.
* Your `COMMAND_PATTERN` must make use of the `%m` argument so that
   the proper `TEST_RUNNER` is passed to it.  If you were using `%m`
   for something else, you'll need to change that.
* Any method signatures that specify `Collection` need to specify a
  more concrete collection type instead (such as `OrderedCollection`).
* Any method that returns a collection of a domain class must now
  specify the type of the elements in the collection.  Thus, instead
  of taking or returning `OrderedCollection`, take or return
  `#(#OrderedCollection #MyDomainClass)`.
* FitLibrary fixtures no longer have a `systemUnderTest` instance
  variable.  Use `self systemUnderTest` in your subclasses instead.
* `FitLibraryFixture` no longer implements `#tearDown`, so any
  super-sends in your subclasses will need to be removed.
* `ArrayFixture`/`ListFixture`/etc: The `#actualCollection:` instance
  creation method has been renamed to `#actuals:`.  You'll need to
  adjust for this rename.
* Overly permissive `#signatureFor:` methods will need to be
  restricted.  If your implementation returns a default method
  signature, you wll begin to see errors in your tests for completely
  unrelated methods.  This version of FitLibrary uses a more flexible
  approach to finding methods and will attempt to find signatures for
  methods that it hadn't considered in the past.  Your
  `#signatureFor:` methods should either answer `super` or `nil` if a
  specific match isn't found.  I recommend switching over to the new
  pragma method signatures instead, but `#signatureFor:` will continue
  to work as long as no instance-specific actions are required to
  compute a proper `MethodSignature`.
* `#signatureFor:` methods must not do anything instance-specific, or
   they will not work correctly due to several caches that were
   implemented for optimization purposes.

This version of FitLibrary is designed to work with Fitnesse-20121220
or later.  It will work with earlier versions of Fitnesse as well,
with one minor exception: The "show as after table" feature generates
explicit HTML markup for collapsible sections, which changed in
Fitnesse 20121220.  To use the older markup, you can evaluate
`FoldingTexts useOldMarkup: true`.  Or, modify
`FitLibraryServer>>initialize` to include the line `batching
useOldFoldingTextMarkup: true`.

The internal implementation details of FitLibrary have been moved into
the FitLibrary-Internals package.  If you find that you need to depend
on something in FitLibrary-Internals, I consider that a bug.  Please
let me know so I can fix it.

# Usage

## Running Fit

The Fit batch test runner can be run from within a development image,
or from a command line using a deployed image.

To run from a development image, run the following in a workspace:

	Fit.FileRunner runInput: '<path to input filename>' output: '<path to output filename>'.

To run from a deployed image, use a command line of the following
form:

	<visual> <image name> -fit <path to input filename> <path to output filename>

where:
* `<visual>` is the path to the VisualWorks VM executable you wish to
  use;
* `<image name>` is the path to an image containing Fit and your
  Fixture classes;
* `<path to input filename>` is the path to the html file containing
  the tests you want to run; and
* `<path to output filename>` is the path to the html file that will
  contain the results of running the tests.

Note that the image will immediately exit after running the tests.

## Running Fitnesse

The main Fitnesse application is written in Java.  It is necessary to
get that running first.  Fitnesse uses language-specific
implementations of FitServer to actually run the tests.  It is
possible to run Smalltalk-based Fitnesse tests in either a development
image or deployed image.

### Running From a Development Image

Fitnesse expects to launch an application that will connect back to it
to retrieve the tests to run.  A development image is normally already
running, so we have to fake out Fitnesse.  To do this:

* Create an executable shell script or batch file called
  `fakeFitServer[.bat]`.  See `DevelopmentFitServer
  class>>exampleFitServerBat` or `exampleFitServerShellScript`.  This
  script must be somewhere in your path, or you must use a full
  pathname in your test pages.
* Create a Fitnesse page as described in `DevelopmentFitServer
  class>>exampleFitnessePage`.
* In a workspace, tell `FitDevelopmentSystem` where to find the shell
  script or batch file you created:

	`FitDevelopmentSystem current filename: '/path/to/file/used/in/fakeFitServer/script' asFilename.`

* Then, activate the subsystem, which will start the polling loop.

	`FitDevelopmentSystem activate.`

Once you have done this, the development Fit server will start when
you load your image, stop when you exit, and also stop and re-start
when you save your image.  If you want to keep this from happening,
execute the following:

	FitDevelopmentSystem canActivate: false.

You can now run your tests from Fitnesse and they will run against the
fixtures in your development image.

### Running From a Deployed Image

To run from a deployed image, define a `COMMAND_PATTERN` in your
Fitnesse pages like this:

	!define COMMAND_PATTERN {<visual> <image name> -fitnesse %m}

where:
* `<visual>` is the path to the VisualWorks VM executable you wish to
  use;
* `<image name>` is the path to an image containing Fit and your
  Fixture classes;

Fitnesse will append the necessary parameters at the end of the
pattern.  If you use a batch file or shell script, make sure that
those arguments are passed on to the application at the end of the
command line.

Note that the image will immediately exit after running the tests.

# Acknowledgements

I inherited Smalltalk Fit from Dave Astels.  He had a mostly working
port of Fit done.  I enhanced it and added Fitnesse and FitLibrary
support.

Alan Wostenberg devised the simple file-based scheme that is used for
running tests against a development image.

# Contributing

I'm happy to receive bug fixes and improvements to this package.  If
you'd like to contribute, please publish your changes as a "branch"
(non-integer) version in the Public Store Repository and contact me as
outlined below to let me know.  I will merge your changes back into
the "trunk" as soon as I can review them.

# Contact Information

If you have any questions about SmalltalkFit and how to use it, feel
free to contact me.

* Web site: http://randycoulman.com
* Blog: Courageous Software (http://randycoulman.com/blog)
* E-mail: randy _at_ randycoulman _dot_ com
* Twitter: @randycoulman
* GitHub: randycoulman
