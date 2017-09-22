---
layout: page
title: Guide
---
The Figurate Guide provides documentation to assist with getting the most out of the Figurate framework.

1. [Getting Started](#getting-started)
2. [Launcher](#launcher)
3. [Components](#components)
4. [Constellations](#constellations)

## <a name="getting-started"></a>Getting Started

The following instructions will help you to get a Figurate project up and running as quickly as possible.

### Gradle Plugin

The simplest way to use Figurate is to apply the Figurate plugin in a Gradle build script. Some [example applications][figurate-examples] are
provided that demonstrate usage of this plugin.

## <a name="launcher"></a>Launcher

The Figurate Launcher is a convenient way to configure the bootstrap of the underlying OSGi platform.

### Startup Configuration

### Bundles

### Start Levels

## <a name="components"></a>Components

Figurate provides configuration via the OSGi standard Configuration Admin support.

### Component Configuration

### Factory Configuration

## <a name="constellations"></a>Constellations

A Figurate Constellation is a collection of bundles that are designed to work together to provide a specific feature.
The available Figurate constellations are as follows:

| Constellation   | Figurate Versions | Description
|-
| felix           | All               | Felix OSGi runtime
| http            | All               | HTTP server/Servlet container (Jetty)
| scr             | All               | Service Component Runtime
| webconsole      | All               | Felix OSGi dashboard


### Gradle Plugin

Figurate provides a gradle plugin to make it simple to add constellations to your application, without needing to
worry about compatibility issues between bundles across different constellations. You apply the constellation plugin as
follows:

`apply plugin: 'constellations'`


### Configurations

The Gradle Constellation Plugin uses Gradle configurations to define constellations. As such, to add a constellation
to your application you just include the configuration in Figurate's custom *bundle* configuration, like so:

`bundle constellation$felix`

You can also make use of Gradle's dependency tools to query the actual bundles included in a given constellation:

`gradle dependencies --configuration constellation\$felix`

Note that as Figurate constellations are defined using a dollar ($) symbol, some shells may try to apply variable
substitution, which is why we need to escape the dollar.

The output should look something like this:

{% highlight bash %}
\$ gradle dependencies --configuration constellation\$felix
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

[figurate-examples]: https://github.com/figurate/figurate-examples

