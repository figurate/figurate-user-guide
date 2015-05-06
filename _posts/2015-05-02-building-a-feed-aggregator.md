---
layout: post
title: Building a feed aggregator - Part one
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
this instance we require the RSS, HTTP, and [Felix] OSGi runtime Constellations. Note how we use the **bundle** dependency
scope, which will ensure the OSGi bundles in each Constellation are included in the final application distribution.

{% highlight groovy %}
...
apply plugin: 'figurate'
apply plugin: 'constellations'

repositories {
    mavenCentral()
    maven {
        url 'https://oss.sonatype.org/content/repositories/snapshots'
    }
}

dependencies {
    bundle configurations.constellation$felix, configurations.constellation$rss, configurations.constellation$http,
            configurations.constellation$webconsole, configurations.constellation$logback,
            'org.codehaus.groovy:groovy-all:2.4.3', 'org.apache.felix:org.apache.felix.configadmin:1.8.0',
            configurations.constellation$rest, configurations.constellation$jackson
            
    runtime configurations.constellation$logback
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
    bundle configurations.constellation$felix, configurations.constellation$rss, configurations.constellation$http,
            configurations.constellation$webconsole, configurations.constellation$logback,
            'org.codehaus.groovy:groovy-all:2.4.3', 'org.apache.felix:org.apache.felix.configadmin:1.8.0',
            configurations.constellation$rest, configurations.constellation$jackson
            
    runtime configurations.constellation$logback
}

...

launcherConfig {
    configProps << [
        // use the next available port from 8080 onwards
        'org.osgi.service.http.port'        : '[8080,8099)',
        'org.apache.felix.http.jettyEnabled': 'true',
        'org.apache.felix.http.whiteboardEnabled': 'true',
        'org.apache.felix.http.debug'       : 'true',
    ]
}
{% endhighlight %}

To launch the application from the command line we use the **run** task implicitly included via the [Gradle Application
Plugin]:

`gradlew run`

## The Result

We can test our results in a browser, or with [curl] as follows:

`curl http://localhost:8080/feed?url=http://rss.slashdot.org/Slashdot/slashdotMain`

In a browser application we might prefer the output in JSON format, which we can do as follows:

`curl http://localhost:8080/feed/json?url=http://rss.slashdot.org/Slashdot/slashdotMain`

Similarly if we are embedding a feed in a HTML page we can request html output:

`curl http://localhost:8080/feed/html?url=http://rss.slashdot.org/Slashdot/slashdotMain`



## Conclusion

So now we have a fully functional application that is capable of transforming and serving feeds in different formats.
Technically we can't really call it an aggregator yet, as it doesn't cache feed history, however that functionality
will be explored in the next instalment.

[Felix]: http://felix.apache.org/
[Gradle Application Plugin]: http://gradle.org/docs/current/userguide/application_plugin.html
[curl]: http://curl.haxx.se/docs/manpage.html