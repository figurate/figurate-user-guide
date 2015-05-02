---
layout: post
title: Building a feed aggregator
---
Feed aggregation is perhaps one of the simplest services you can provide on the Internet. It (usually) doesn't require
authentication, datasets are readily available, and the data itself is simple enough that it can be stored and rendered
relatively easily.

However it is still not trivial to build a robust and scalable feed aggregation service, as there are many concerns
one must consider. Not all feeds conform to the same standards, and even within a single standard there are different
levels of feed compliance. Scheduled feed updates and cleansing also require much consideration, along with caching
strategies, persistence, and supported rendering formats. Figurate makes these concerns much simpler by providing
pre-packaged Components and Constellations that take each of these concerns into account.

## Creating a project

Starting with a new Gradle project, your build script should look like this:

{% highlight groovy %}
buildscript {
    repositories {
        mavenCentral()
        maven {
            url 'https://oss.sonatype.org/content/repositories/snapshots'
        }
    }
    dependencies {
        classpath 'org.figurate:gradle-figurate-plugin:master-SNAPSHOT',
                'org.figurate:gradle-constellations-plugin:master-SNAPSHOT'
    }
}

apply plugin: 'groovy'
apply plugin: 'osgi'
apply plugin: 'figurate'
apply plugin: 'constellations'

repositories {
    mavenCentral()
    maven {
        url 'https://oss.sonatype.org/content/repositories/snapshots'
    }
}
{% endhighlight %}

## Adding Constellations

To provide the features required for our aggregator, we add the relevant Constellations to our build dependencies. In
this instance we require the RSS, HTTP, and Felix OSGi runtime Constellations. Note how we use the **bundle** dependency
scope, which will ensure the OSGi bundles in each Constellation are included in the final application distribution.

{% highlight groovy %}
...
apply plugin: 'groovy'
apply plugin: 'osgi'
apply plugin: 'figurate'
apply plugin: 'constellations'

repositories {
    mavenCentral()
    maven {
        url 'https://oss.sonatype.org/content/repositories/snapshots'
    }
}

dependencies {
    bundle constellation$felix, constellation$rss, constellation$http
}
{% endhighlight %}

## Configuring Components

`TODO`

## Launching the application

Finally we need to specify any custom configuration for the OSGi runtime itself. Typically it will have sensible defaults,
however we still occasionally will need to override these defaults, or provide configuration where no default values are
possible.

{% highlight groovy %}
...
apply plugin: 'groovy'
apply plugin: 'osgi'
apply plugin: 'figurate'
apply plugin: 'constellations'

repositories {
    mavenCentral()
    maven {
        url 'https://oss.sonatype.org/content/repositories/snapshots'
    }
}

dependencies {
    bundle constellation$felix, constellation$rss, constellation$http
}

...

launcherConfig {
        configProps << [
            'org.osgi.framework.system.packages.extra': 'sun.misc,org.gcontracts,org.gcontracts.annotations,org.gcontracts.generation',
            'org.osgi.service.http.port'        : '[8080,8099)',
            'org.apache.felix.http.jettyEnabled': 'true',
            'org.apache.felix.http.whiteboardEnabled': 'true',
            'org.apache.felix.http.debug'       : 'true',
        ]
    }
{% endhighlight %}



