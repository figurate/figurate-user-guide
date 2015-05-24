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

## OSGi Framework Selection

Figurate supports any OSGi framework that implements the OSGi [FrameworkFactory]. Just add the appropriate libraries
as runtime dependencies:

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
    runtime 'org.apache.felix:org.apache.felix.framework:5.0.0'
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
    bundle configurations.constellation$felix, // OSGi runtime
            configurations.constellation$webconsole, // OSGi dashboard application
            configurations.constellation$http, // embedded Jetty HTTP server
            configurations.constellation$rest, // REST support from Amdatu
            configurations.constellation$jackson, // JSON databinding support for the REST framework
            configurations.constellation$logback, // logging framework
            'org.codehaus.groovy:groovy-all:2.4.3', // Groovy runtime
            configurations.constellation$rss // RSS parsing and REST-ful endpoints from Newsagent library

    runtime 'org.apache.felix:org.apache.felix.framework:5.0.0', configurations.constellation$logback
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
    bundle configurations.constellation$felix, // OSGi runtime
            configurations.constellation$webconsole, // OSGi dashboard application
            configurations.constellation$http, // embedded Jetty HTTP server
            configurations.constellation$rest, // REST support from Amdatu
            configurations.constellation$jackson, // JSON databinding support for the REST framework
            configurations.constellation$logback, // logging framework
            'org.codehaus.groovy:groovy-all:2.4.3', // Groovy runtime
            configurations.constellation$rss // RSS parsing and REST-ful endpoints from Newsagent library

    runtime 'org.apache.felix:org.apache.felix.framework:5.0.0', configurations.constellation$logback
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

{% highlight bash %}
curl http://localhost:8080/feed?url=http://www.figurate.org/feed.xml
{% endhighlight %}

Output:
{% highlight json %}
{
  "entries": [
    {
      "uri": "http://figurate.org/2015/05/02/building-a-feed-aggregator.html",
      "title": "Building a feed aggregator - Part one",
      "description": "Feed aggregation is perhaps one of the simplest services you can provide on the Internet. It (usually) doesn’t require authentication, datasets are readily available, and the data itself is simple enough that it can be stored and rendered relatively easily.",
      "text": [],
      "link": "http://figurate.org/2015/05/02/building-a-feed-aggregator.html",
      "published": "2015-05-02T00:00:00+0000"
    }
  ],
  "title": "Figurate",
  "description": "Figurate is a framework for building robust, scalable and modular Java applications. Figurate is based on the principles of modularity, testability and adaptability, which provide the basis for a platform that is capable of supporting the processes that underpin many of the modern design and implementation strategies.\n",
  "url": "http://www.figurate.org/feed.xml",
  "links": [
    "http://figurate.org/"
  ],
  "tags": []
}
{% endhighlight %}

The default output is in JSON, however we can ask for YAML by adding the appropriate header:

{% highlight bash %}
curl -H "Accept: application/yaml" http://localhost:8080/feed?url=http://www.figurate.org/feed.xml
{% endhighlight %}

Output:
{% highlight yaml %}
entries:
- uri: http://figurate.org/2015/05/02/building-a-feed-aggregator.html
  title: Building a feed aggregator - Part one
  description: Feed aggregation is perhaps one of the simplest services you can provide on the Internet. It (usually) doesn’t require authentication, datasets are readily available, and the data itself is simple enough that it can be stored and rendered relatively easily.
  text: []
  link: http://figurate.org/2015/05/02/building-a-feed-aggregator.html
  published: 2015-05-02T00:00:00Z
title: Figurate
description: |
  Figurate is a framework for building robust, scalable and modular Java applications. Figurate is based on the principles of modularity, testability and adaptability, which provide the basis for a platform that is capable of supporting the processes that underpin many of the modern design and implementation strategies.
url: http://www.figurate.org/feed.xml
links: ['http://figurate.org/']
tags: []
{% endhighlight %}

## Conclusion

So now we have a fully functional application that is capable of transforming and serving feeds in different formats.
Technically we can't really call it an aggregator yet, as it doesn't cache feed history, however that functionality
will be explored in the next instalment.

## References

You can find the [full source code] for this example on [GitHub]

Other libraries used in this example are as follows:

* [Newsagent]
* [Amdatu]
* [Felix]

[FrameworkFactory]: https://osgi.org/javadoc/r4v42/org/osgi/framework/launch/FrameworkFactory.html
[Felix]: http://felix.apache.org/
[Gradle Application Plugin]: http://gradle.org/docs/current/userguide/application_plugin.html
[curl]: http://curl.haxx.se/docs/manpage.html
[full source code]: https://github.com/figurate/figurate-examples/tree/master/feed-aggregator
[GitHub]: https://github.com/figurate
[Newsagent]: https://github.com/benfortuna/newsagent
[Amdatu]: http://www.amdatu.org/components/web.html
[Felix]: http://felix.apache.org/
