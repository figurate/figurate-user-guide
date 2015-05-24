---
layout: post
title: The pitfalls and wins of OSGi
---
The history of OSGi has been a bumpy road of support and rejection from all parts of the wider IT community, most notably
from Spring betting the farm on it, only to reject it a few months later. 

Ultimately there are some pitfalls, but also some major wins, which should be considered when assessing OSGi.

# *Pitfall:* Transitive dependencies don't apply

One of the major benefits realised by a build system such as [Maven] was that it didn't just know where to find your
project's dependencies, it also knew about the dependencies of those dependencies. As such you could specify as little
as a single dependency for your project and automatically it would provide all of the dependencies you needed for those
immediate dependencies to work. Ultimately this has proven to be not such a good thing all the time, as these transitive
dependencies are often over-prescribed. As annoying as this may be, at least you can be reasonably confident that the
code will work in the way it is intended.

Due to it's principles of loose-coupling, OSGi has no such recognition of transitive dependencies. With OSGi you must
identify all of the packages (and versions) a bundle imports, and manually add them to your list of deployed bundles.
The primary reason for this is that there is not necessarily a single definitive bundle that exports the packages you
need. So whilst this does empower the developer in choosing their bundles, it also can be quite a tedious process.

# *Win:* Awareness of what is going into your recipe

Whilst it may not always be recognised at first, the major benefit of this close inspection of bundles is that you are
gaining valuable knowledge about what the bundles do and what they contain. Ideally when constructing an OSGi application
you should be aware of the purpose of every single bundle, and why it is needed. With traditional development you often
trust the transitive dependencies that are implicitly included in your final application, which means that you cannot
be entirely confident about everything you are deploying into your production environment.

# *Win:* Not constrained to a single dependency version

When developing more complex applications there are often issues with conflicting transitive dependencies. This is where
two or more dependencies require the same dependency, but different versions. In such cases you are required to choose
a single version and hope that it will work with all the libraries that require it. OSGi has no such contraint, as it
is possible to include multiple versions of the same dependency. In critical systems, where you must be absolutely sure
that the libraries are functioning correctly, OSGi provides the option of embedding your dependencies within the bundle
itself, thus isolating the code from other parts of the system. OSGi ultimately provides more options for solving
dependency issues, however it can take an amount of learning and experience to be able to choose the most appropriate
approach.

# *Pitfall:* Loose coupling requires more testing

# *Win:* Loose coupling enforces robustness

[Maven]: http://maven.apache.org
