---
layout: page
title: Constellations
---
A Figurate Constellation is a collection of bundles that are designed to work together to provide a specific feature.

# Gradle Plugin

Figurate provides a gradle plugin to make it simple to add constellations to your application, without needing to
worry about compatibility issues between bundles across different constellations.

## Configurations

The Gradle Constellation Plugin uses Gradle configurations to define constellations. As such, to add a constellation
to your application you just include the configuration in Figurate's custom *bundle* configuration, like so:

`bundle constellation$felix`

You can also make use of Gradle's dependency tools to query the actual bundles included in a given constellation:

`gradle dependencies --configuration constellation\$felix`

Note that as Figurate constellations are defined using a dollar ($) symbol, some shells may try to apply variable
substitution, which is why we need to escape the dollar.

The output should look something like this:

{% highlight bash %}
Bens-MacBook-Pro:feed-aggregator fortuna$ gradle dependencies --configuration constellation\$felix
:feed-aggregator:dependencies

------------------------------------------------------------
Project :feed-aggregator
------------------------------------------------------------

constellation$felix
+--- org.apache.felix:org.apache.felix.dependencymanager:4.0.1
+--- org.apache.felix:org.apache.felix.http.bundle:2.2.2
+--- org.apache.felix:org.apache.felix.log:1.0.1
+--- org.apache.felix:org.apache.felix.scr:1.8.2
+--- org.osgi:org.osgi.compendium:4.2.0
\--- org.apache.felix:org.apache.felix.configadmin:1.8.0

BUILD SUCCESSFUL

Total time: 6.027 secs
{% endhighlight %}

