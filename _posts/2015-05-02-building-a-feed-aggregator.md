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
    bundle configurations.constellation$felix, // OSGi runtime
            configurations.constellation$webconsole, // OSGi dashboard application
            configurations.constellation$http, // embedded Jetty HTTP server
            configurations.constellation$rest, // REST support from Amdatu
            configurations.constellation$jackson, // JSON databinding support for the REST framework
            configurations.constellation$logback, // logging framework
            'org.codehaus.groovy:groovy-all:2.4.3', // Groovy runtime
            configurations.constellation$rss // RSS parsing and REST-ful endpoints from Newsagent library

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
    bundle configurations.constellation$felix, // OSGi runtime
            configurations.constellation$webconsole, // OSGi dashboard application
            configurations.constellation$http, // embedded Jetty HTTP server
            configurations.constellation$rest, // REST support from Amdatu
            configurations.constellation$jackson, // JSON databinding support for the REST framework
            configurations.constellation$logback, // logging framework
            'org.codehaus.groovy:groovy-all:2.4.3', // Groovy runtime
            configurations.constellation$rss // RSS parsing and REST-ful endpoints from Newsagent library

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

```
{"entries":[{"uri":"http://news.slashdot.org/story/15/05/08/1414226/top-advisor-to-australian-govt-says-climate-change-is-a-un-conspiracy?utm_source=rss1.0mainlinkanon&utm_medium=feed","title":"Top Advisor To Australian Gov't Says Climate Change is a UN Conspiracy","description":"An anonymous reader writes: Maurice Newman, the top business advisor to conservative Australian Prime Minister Tony Abbott, today published an opinion piece (paywalled) in which he claims, \"It's a well-kept secret, but 95 per cent of the climate models ... have been found ... to be in error.\" He goes on to write \"This is not about facts or logic. It's about a new world order under the control of the UN.\" While Newman's 'skeptical' views have long been on record, it's unclear when he came to believe in this vast global conspiracy. Last year, the Abbott government removed Australia's Emissions Trading Scheme, and recently gave $4 million in funding to contrarian Bjorn Lomberg, while cutting hundreds of millions of dollars from science across the country.\n<p></p>\n<div> \n <a href=\"http://twitter.com/home?status=Top+Advisor+To+Australian+Gov't+Says+Climate+Change+is+a+UN+Conspiracy%3A+http%3A%2F%2Fbit.ly%2F1Kqd2vY\"><img src=\"http://a.fsdn.com/sd/twitter_icon_large.png\"></a> \n <a href=\"http://www.facebook.com/sharer.php?u=http%3A%2F%2Fnews.slashdot.org%2Fstory%2F15%2F05%2F08%2F1414226%2Ftop-advisor-to-australian-govt-says-climate-change-is-a-un-conspiracy%3Futm_source%3Dslashdot%26utm_medium%3Dfacebook\"><img src=\"http://a.fsdn.com/sd/facebook_icon_large.png\"></a> \n <a href=\"http://plus.google.com/share?url=http://news.slashdot.org/story/15/05/08/1414226/top-advisor-to-australian-govt-says-climate-change-is-a-un-conspiracy?utm_source=slashdot&amp;utm_medium=googleplus\"><img src=\"http://www.gstatic.com/images/icons/gplus-16.png\" alt=\"Share on Google+\"></a> \n</div>\n<p></p>\n<p><a href=\"http://news.slashdot.org/story/15/05/08/1414226/top-advisor-to-australian-govt-says-climate-change-is-a-un-conspiracy?utm_source=rss1.0moreanon&amp;utm_medium=feed\">Read more of this story</a> at Slashdot.</p>","text":[],"link":"http://news.slashdot.org/story/15/05/08/1414226/top-advisor-to-australian-govt-says-climate-change-is-a-un-conspiracy?utm_source=rss1.0mainlinkanon&utm_medium=feed","published":"2015-05-08T14:20:00+0000"},...
```

The default output is in JSON, however we can ask for YAML by adding the appropriate header:

`curl -H "Accept: application/yaml" http://localhost:8080/feed?url=http://rss.slashdot.org/Slashdot/slashdotMain`

```
entries:
- uri: http://it.slashdot.org/story/15/05/08/1423208/cybersecurity-company-extorted-its-clients-says-whistleblower?utm_source=rss1.0mainlinkanon&utm_medium=feed
  title: Cybersecurity Company Extorted Its Clients, Says Whistleblower
  description: "An anonymous reader writes: Richard Wallace used to be an investigator\
    \ for Tiversa, a cybersecurity company that sells services like \"breach protection\"\
    \ and \"incident response.\" These days, Wallace is testifying in federal court\
    \ that Tiversa faked breaches to encourage sales, and extorted clients that weren't\
    \ interested. For example, Wallace said Tiversa targeted a cancer testing center\
    \ called LabMD in 2010, tapping into their computers and downloading medical records.\
    \ Tiversa then used those records as evidence to convince LabMD they had been\
    \ hacked, offering its \"incident response\" service at the same time. LabMD didn't\
    \ fall for it, so Tiversa told the FTC about the \"hack.\" The FTC, none-the-wiser,\
    \ went after LabMD in court, eventually destroying the business. Wallace has also\
    \ cast suspicion on reports Tiversa has issued, including one saying President\
    \ Obama's helicopter blueprints were found on Iranian computers.\n<p></p>\n<div>\
    \ \n <a href=\"http://twitter.com/home?status=Cybersecurity+Company+Extorted+Its+Clients%2C+Says+Whistleblower%3A+http%3A%2F%2Fbit.ly%2F1Kqfoeh\"\
    ><img src=\"http://a.fsdn.com/sd/twitter_icon_large.png\"></a> \n <a href=\"http://www.facebook.com/sharer.php?u=http%3A%2F%2Fit.slashdot.org%2Fstory%2F15%2F05%2F08%2F1423208%2Fcybersecurity-company-extorted-its-clients-says-whistleblower%3Futm_source%3Dslashdot%26utm_medium%3Dfacebook\"\
    ><img src=\"http://a.fsdn.com/sd/facebook_icon_large.png\"></a> \n <a href=\"\
    http://plus.google.com/share?url=http://it.slashdot.org/story/15/05/08/1423208/cybersecurity-company-extorted-its-clients-says-whistleblower?utm_source=slashdot&amp;utm_medium=googleplus\"\
    ><img src=\"http://www.gstatic.com/images/icons/gplus-16.png\" alt=\"Share on\
    \ Google+\"></a> \n</div>\n<p></p>\n<p><a href=\"http://it.slashdot.org/story/15/05/08/1423208/cybersecurity-company-extorted-its-clients-says-whistleblower?utm_source=rss1.0moreanon&amp;utm_medium=feed\"\
    >Read more of this story</a> at Slashdot.</p>"
  text: []
  link: http://it.slashdot.org/story/15/05/08/1423208/cybersecurity-company-extorted-its-clients-says-whistleblower?utm_source=rss1.0mainlinkanon&utm_medium=feed
  published: 2015-05-08T15:02:00Z
- uri: http://news.slashdot.org/story/15/05/08/1414226/top-advisor-to-australian-govt-says-climate-change-is-a-un-conspiracy?utm_source=rss1.0mainlinkanon&utm_medium=feed
```

## Conclusion

So now we have a fully functional application that is capable of transforming and serving feeds in different formats.
Technically we can't really call it an aggregator yet, as it doesn't cache feed history, however that functionality
will be explored in the next instalment.

[Felix]: http://felix.apache.org/
[Gradle Application Plugin]: http://gradle.org/docs/current/userguide/application_plugin.html
[curl]: http://curl.haxx.se/docs/manpage.html