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
      "description": "<p>Feed aggregation is perhaps one of the simplest services you can provide on the Internet. It (usually) doesn’t require authentication, datasets are readily available, and the data itself is simple enough that it can be stored and rendered relatively easily.</p> \n<p>However it is still not trivial to build a robust and scalable feed aggregation service, as there are many concerns one must consider. Not all feeds conform to the same standards, and even within a single standard there are different levels of feed compliance. Scheduled feed updates and cleansing also require much consideration, along with caching strategies, persistence, and supported rendering formats. Figurate makes these concerns much simpler by providing pre-packaged Components and Constellations that take each of these concerns into account.</p> \n<h2>Creating a project</h2> \n<p>Starting with a new Gradle project, your build script should look like this:</p> \n<div>\n <pre><code><span>buildscript</span> <span>{</span>\n    <span>repositories</span> <span>{</span>\n        <span>mavenCentral</span><span>()</span>\n        <span>maven</span> <span>{</span>\n            <span>url</span> <span>'https://oss.sonatype.org/content/repositories/snapshots'</span>\n        <span>}</span>\n    <span>}</span>\n    <span>dependencies</span> <span>{</span>\n        <span>classpath</span> <span>'org.figurate:gradle-figurate-plugin:master-SNAPSHOT'</span><span>,</span>\n                <span>'org.figurate:gradle-constellations-plugin:master-SNAPSHOT'</span>\n    <span>}</span>\n<span>}</span>\n\n<span>apply</span> <span>plugin:</span> <span>'figurate'</span>\n<span>apply</span> <span>plugin:</span> <span>'constellations'</span>\n\n<span>repositories</span> <span>{</span>\n    <span>mavenCentral</span><span>()</span>\n    <span>maven</span> <span>{</span>\n        <span>url</span> <span>'https://oss.sonatype.org/content/repositories/snapshots'</span>\n    <span>}</span>\n<span>}</span></code></pre>\n</div> \n<h2>Adding Constellations</h2> \n<p>To provide the features required for our aggregator, we add the relevant Constellations to our build dependencies. In this instance we require the RSS, HTTP, and <a href=\"http://felix.apache.org/\">Felix</a> OSGi runtime Constellations. Note how we use the <strong>bundle</strong> dependency scope, which will ensure the OSGi bundles in each Constellation are included in the final application distribution.</p> \n<div>\n <pre><code><span>...</span>\n<span>apply</span> <span>plugin:</span> <span>'figurate'</span>\n<span>apply</span> <span>plugin:</span> <span>'constellations'</span>\n\n<span>repositories</span> <span>{</span>\n    <span>mavenCentral</span><span>()</span>\n    <span>maven</span> <span>{</span>\n        <span>url</span> <span>'https://oss.sonatype.org/content/repositories/snapshots'</span>\n    <span>}</span>\n<span>}</span>\n\n<span>dependencies</span> <span>{</span>\n    <span>bundle</span> <span>configurations</span><span>.</span><span>constellation</span><span>$felix</span><span>,</span> <span>// OSGi runtime</span>\n            <span>configurations</span><span>.</span><span>constellation</span><span>$webconsole</span><span>,</span> <span>// OSGi dashboard application</span>\n            <span>configurations</span><span>.</span><span>constellation</span><span>$http</span><span>,</span> <span>// embedded Jetty HTTP server</span>\n            <span>configurations</span><span>.</span><span>constellation</span><span>$rest</span><span>,</span> <span>// REST support from Amdatu</span>\n            <span>configurations</span><span>.</span><span>constellation</span><span>$jackson</span><span>,</span> <span>// JSON databinding support for the REST framework</span>\n            <span>configurations</span><span>.</span><span>constellation</span><span>$logback</span><span>,</span> <span>// logging framework</span>\n            <span>'org.codehaus.groovy:groovy-all:2.4.3'</span><span>,</span> <span>// Groovy runtime</span>\n            <span>configurations</span><span>.</span><span>constellation</span><span>$rss</span> <span>// RSS parsing and REST-ful endpoints from Newsagent library</span>\n\n    <span>runtime</span> <span>configurations</span><span>.</span><span>constellation</span><span>$logback</span>\n<span>}</span></code></pre>\n</div> \n<h2>Configuring Components</h2> \n<p><code>TODO</code></p> \n<h2>Launching the application</h2> \n<p>Finally we need to specify any custom configuration for the OSGi runtime itself. Typically it will have sensible defaults, however we still occasionally will need to override these defaults, or provide configuration where no default values are possible.</p> \n<div>\n <pre><code><span>...</span>\n<span>apply</span> <span>plugin:</span> <span>'groovy'</span>\n<span>apply</span> <span>plugin:</span> <span>'osgi'</span>\n<span>apply</span> <span>plugin:</span> <span>'figurate'</span>\n<span>apply</span> <span>plugin:</span> <span>'constellations'</span>\n\n<span>repositories</span> <span>{</span>\n    <span>mavenCentral</span><span>()</span>\n    <span>maven</span> <span>{</span>\n        <span>url</span> <span>'https://oss.sonatype.org/content/repositories/snapshots'</span>\n    <span>}</span>\n<span>}</span>\n\n<span>dependencies</span> <span>{</span>\n    <span>bundle</span> <span>configurations</span><span>.</span><span>constellation</span><span>$felix</span><span>,</span> <span>// OSGi runtime</span>\n            <span>configurations</span><span>.</span><span>constellation</span><span>$webconsole</span><span>,</span> <span>// OSGi dashboard application</span>\n            <span>configurations</span><span>.</span><span>constellation</span><span>$http</span><span>,</span> <span>// embedded Jetty HTTP server</span>\n            <span>configurations</span><span>.</span><span>constellation</span><span>$rest</span><span>,</span> <span>// REST support from Amdatu</span>\n            <span>configurations</span><span>.</span><span>constellation</span><span>$jackson</span><span>,</span> <span>// JSON databinding support for the REST framework</span>\n            <span>configurations</span><span>.</span><span>constellation</span><span>$logback</span><span>,</span> <span>// logging framework</span>\n            <span>'org.codehaus.groovy:groovy-all:2.4.3'</span><span>,</span> <span>// Groovy runtime</span>\n            <span>configurations</span><span>.</span><span>constellation</span><span>$rss</span> <span>// RSS parsing and REST-ful endpoints from Newsagent library</span>\n\n    <span>runtime</span> <span>configurations</span><span>.</span><span>constellation</span><span>$logback</span>\n<span>}</span>\n\n<span>...</span>\n\n<span>launcherConfig</span> <span>{</span>\n    <span>configProps</span> <span>&lt;&lt;</span> <span>[</span>\n        <span>// use the next available port from 8080 onwards</span>\n        <span>'org.osgi.service.http.port'</span>        <span>:</span> <span>'[8080,8099)'</span><span>,</span>\n        <span>'org.apache.felix.http.jettyEnabled'</span><span>:</span> <span>'true'</span><span>,</span>\n        <span>'org.apache.felix.http.whiteboardEnabled'</span><span>:</span> <span>'true'</span><span>,</span>\n        <span>'org.apache.felix.http.debug'</span>       <span>:</span> <span>'true'</span><span>,</span>\n    <span>]</span>\n<span>}</span></code></pre>\n</div> \n<p>To launch the application from the command line we use the <strong>run</strong> task implicitly included via the <a href=\"http://gradle.org/docs/current/userguide/application_plugin.html\">Gradle Application Plugin</a>:</p> \n<p><code>gradlew run</code></p> \n<h2>The Result</h2> \n<p>We can test our results in a browser, or with <a href=\"http://curl.haxx.se/docs/manpage.html\">curl</a> as follows:</p> \n<div>\n <pre><code>curl http://localhost:8080/feed?url<span>=</span>http://rss.slashdot.org/Slashdot/slashdotMain</code></pre>\n</div> \n<p>Output:</p> \n<div>\n <pre><code><span>{</span><span>\"entries\"</span><span>:[{</span><span>\"uri\"</span><span>:</span><span>\"http://news.slashdot.org/story/15/05/08/1414226/top-advisor-to-australian-govt-says-climate-change-is-a-un-conspiracy?utm_source=rss1.0mainlinkanon&amp;utm_medium=feed\"</span><span>,</span><span>\"title\"</span><span>:</span><span>\"Top Advisor To Australian Gov't Says Climate Change is a UN Conspiracy\"</span><span>,</span><span>\"description\"</span><span>:</span><span>\"An anonymous reader writes: Maurice Newman, the top business advisor to conservative Australian Prime Minister Tony Abbott, today published an opinion piece (paywalled) in which he claims, \\\"It's a well-kept secret, but 95 per cent of the climate models ... have been found ... to be in error.\\\" He goes on to write \\\"This is not about facts or logic. It's about a new world order under the control of the UN.\\\" While Newman's 'skeptical' views have long been on record, it's unclear when he came to believe in this vast global conspiracy. Last year, the Abbott government removed Australia's Emissions Trading Scheme, and recently gave $4 million in funding to contrarian Bjorn Lomberg, while cutting hundreds of millions of dollars from science across the country.\\n&lt;p&gt;&lt;/p&gt;\\n&lt;div&gt; \\n &lt;a href=\\\"http://twitter.com/home?status=Top+Advisor+To+Australian+Gov't+Says+Climate+Change+is+a+UN+Conspiracy%3A+http%3A%2F%2Fbit.ly%2F1Kqd2vY\\\"&gt;&lt;img src=\\\"http://a.fsdn.com/sd/twitter_icon_large.png\\\"&gt;&lt;/a&gt; \\n &lt;a href=\\\"http://www.facebook.com/sharer.php?u=http%3A%2F%2Fnews.slashdot.org%2Fstory%2F15%2F05%2F08%2F1414226%2Ftop-advisor-to-australian-govt-says-climate-change-is-a-un-conspiracy%3Futm_source%3Dslashdot%26utm_medium%3Dfacebook\\\"&gt;&lt;img src=\\\"http://a.fsdn.com/sd/facebook_icon_large.png\\\"&gt;&lt;/a&gt; \\n &lt;a href=\\\"http://plus.google.com/share?url=http://news.slashdot.org/story/15/05/08/1414226/top-advisor-to-australian-govt-says-climate-change-is-a-un-conspiracy?utm_source=slashdot&amp;amp;utm_medium=googleplus\\\"&gt;&lt;img src=\\\"http://www.gstatic.com/images/icons/gplus-16.png\\\" alt=\\\"Share on Google+\\\"&gt;&lt;/a&gt; \\n&lt;/div&gt;\\n&lt;p&gt;&lt;/p&gt;\\n&lt;p&gt;&lt;a href=\\\"http://news.slashdot.org/story/15/05/08/1414226/top-advisor-to-australian-govt-says-climate-change-is-a-un-conspiracy?utm_source=rss1.0moreanon&amp;amp;utm_medium=feed\\\"&gt;Read more of this story&lt;/a&gt; at Slashdot.&lt;/p&gt;\"</span><span>,</span><span>\"text\"</span><span>:[],</span><span>\"link\"</span><span>:</span><span>\"http://news.slashdot.org/story/15/05/08/1414226/top-advisor-to-australian-govt-says-climate-change-is-a-un-conspiracy?utm_source=rss1.0mainlinkanon&amp;utm_medium=feed\"</span><span>,</span><span>\"published\"</span><span>:</span><span>\"2015-05-08T14:20:00+0000\"</span><span>},</span><span>...</span></code></pre>\n</div> \n<p>The default output is in JSON, however we can ask for YAML by adding the appropriate header:</p> \n<div>\n <pre><code>curl -H <span>\"Accept: application/yaml\"</span> http://localhost:8080/feed?url<span>=</span>http://rss.slashdot.org/Slashdot/slashdotMain</code></pre>\n</div> \n<p>Output:</p> \n<div>\n <pre><code><span>entries</span><span>:</span>\n<span>-</span> <span>uri</span><span>:</span> <span>http://it.slashdot.org/story/15/05/08/1423208/cybersecurity-company-extorted-its-clients-says-whistleblower?utm_source=rss1.0mainlinkanon&amp;utm_medium=feed</span>\n  <span>title</span><span>:</span> <span>Cybersecurity Company Extorted Its Clients, Says Whistleblower</span>\n  <span>description</span><span>:</span> <span>\"An</span><span> </span><span>anonymous</span><span> </span><span>reader</span><span> </span><span>writes:</span><span> </span><span>Richard</span><span> </span><span>Wallace</span><span> </span><span>used</span><span> </span><span>to</span><span> </span><span>be</span><span> </span><span>an</span><span> </span><span>investigator\\</span>\n    <span>\\ for</span><span> </span><span>Tiversa,</span><span> </span><span>a</span><span> </span><span>cybersecurity</span><span> </span><span>company</span><span> </span><span>that</span><span> </span><span>sells</span><span> </span><span>services</span><span> </span><span>like</span><span> </span><span>\\\"breach</span><span> </span><span>protection\\\"\\</span>\n    <span>\\ and</span><span> </span><span>\\\"incident</span><span> </span><span>response.\\\"</span><span> </span><span>These</span><span> </span><span>days,</span><span> </span><span>Wallace</span><span> </span><span>is</span><span> </span><span>testifying</span><span> </span><span>in</span><span> </span><span>federal</span><span> </span><span>court\\</span>\n    <span>\\ that</span><span> </span><span>Tiversa</span><span> </span><span>faked</span><span> </span><span>breaches</span><span> </span><span>to</span><span> </span><span>encourage</span><span> </span><span>sales,</span><span> </span><span>and</span><span> </span><span>extorted</span><span> </span><span>clients</span><span> </span><span>that</span><span> </span><span>weren't\\</span>\n    <span>\\ interested.</span><span> </span><span>For</span><span> </span><span>example,</span><span> </span><span>Wallace</span><span> </span><span>said</span><span> </span><span>Tiversa</span><span> </span><span>targeted</span><span> </span><span>a</span><span> </span><span>cancer</span><span> </span><span>testing</span><span> </span><span>center\\</span>\n    <span>\\ called</span><span> </span><span>LabMD</span><span> </span><span>in</span><span> </span><span>2010,</span><span> </span><span>tapping</span><span> </span><span>into</span><span> </span><span>their</span><span> </span><span>computers</span><span> </span><span>and</span><span> </span><span>downloading</span><span> </span><span>medical</span><span> </span><span>records.\\</span>\n    <span>\\ Tiversa</span><span> </span><span>then</span><span> </span><span>used</span><span> </span><span>those</span><span> </span><span>records</span><span> </span><span>as</span><span> </span><span>evidence</span><span> </span><span>to</span><span> </span><span>convince</span><span> </span><span>LabMD</span><span> </span><span>they</span><span> </span><span>had</span><span> </span><span>been\\</span>\n    <span>\\ hacked,</span><span> </span><span>offering</span><span> </span><span>its</span><span> </span><span>\\\"incident</span><span> </span><span>response\\\"</span><span> </span><span>service</span><span> </span><span>at</span><span> </span><span>the</span><span> </span><span>same</span><span> </span><span>time.</span><span> </span><span>LabMD</span><span> </span><span>didn't\\</span>\n    <span>\\ fall</span><span> </span><span>for</span><span> </span><span>it,</span><span> </span><span>so</span><span> </span><span>Tiversa</span><span> </span><span>told</span><span> </span><span>the</span><span> </span><span>FTC</span><span> </span><span>about</span><span> </span><span>the</span><span> </span><span>\\\"hack.\\\"</span><span> </span><span>The</span><span> </span><span>FTC,</span><span> </span><span>none-the-wiser,\\</span>\n    <span>\\ went</span><span> </span><span>after</span><span> </span><span>LabMD</span><span> </span><span>in</span><span> </span><span>court,</span><span> </span><span>eventually</span><span> </span><span>destroying</span><span> </span><span>the</span><span> </span><span>business.</span><span> </span><span>Wallace</span><span> </span><span>has</span><span> </span><span>also\\</span>\n    <span>\\ cast</span><span> </span><span>suspicion</span><span> </span><span>on</span><span> </span><span>reports</span><span> </span><span>Tiversa</span><span> </span><span>has</span><span> </span><span>issued,</span><span> </span><span>including</span><span> </span><span>one</span><span> </span><span>saying</span><span> </span><span>President\\</span>\n    <span>\\ Obama's</span><span> </span><span>helicopter</span><span> </span><span>blueprints</span><span> </span><span>were</span><span> </span><span>found</span><span> </span><span>on</span><span> </span><span>Iranian</span><span> </span><span>computers.\\n&lt;p&gt;&lt;/p&gt;\\n&lt;div&gt;\\</span>\n    <span>\\ \\n</span><span> </span><span>&lt;a</span><span> </span><span>href=\\\"http://twitter.com/home?status=Cybersecurity+Company+Extorted+Its+Clients%2C+Says+Whistleblower%3A+http%3A%2F%2Fbit.ly%2F1Kqfoeh\\\"\\</span>\n    <span>&gt;&lt;img</span><span> </span><span>src=\\\"http://a.fsdn.com/sd/twitter_icon_large.png\\\"&gt;&lt;/a&gt;</span><span> </span><span>\\n</span><span> </span><span>&lt;a</span><span> </span><span>href=\\\"http://www.facebook.com/sharer.php?u=http%3A%2F%2Fit.slashdot.org%2Fstory%2F15%2F05%2F08%2F1423208%2Fcybersecurity-company-extorted-its-clients-says-whistleblower%3Futm_source%3Dslashdot%26utm_medium%3Dfacebook\\\"\\</span>\n    <span>&gt;&lt;img</span><span> </span><span>src=\\\"http://a.fsdn.com/sd/facebook_icon_large.png\\\"&gt;&lt;/a&gt;</span><span> </span><span>\\n</span><span> </span><span>&lt;a</span><span> </span><span>href=\\\"\\</span>\n    <span>http://plus.google.com/share?url=http://it.slashdot.org/story/15/05/08/1423208/cybersecurity-company-extorted-its-clients-says-whistleblower?utm_source=slashdot&amp;amp;utm_medium=googleplus\\\"\\</span>\n    <span>&gt;&lt;img</span><span> </span><span>src=\\\"http://www.gstatic.com/images/icons/gplus-16.png\\\"</span><span> </span><span>alt=\\\"Share</span><span> </span><span>on\\</span>\n    <span>\\ Google+\\\"&gt;&lt;/a&gt;</span><span> </span><span>\\n&lt;/div&gt;\\n&lt;p&gt;&lt;/p&gt;\\n&lt;p&gt;&lt;a</span><span> </span><span>href=\\\"http://it.slashdot.org/story/15/05/08/1423208/cybersecurity-company-extorted-its-clients-says-whistleblower?utm_source=rss1.0moreanon&amp;amp;utm_medium=feed\\\"\\</span>\n    <span>&gt;Read</span><span> </span><span>more</span><span> </span><span>of</span><span> </span><span>this</span><span> </span><span>story&lt;/a&gt;</span><span> </span><span>at</span><span> </span><span>Slashdot.&lt;/p&gt;\"</span>\n  <span>text</span><span>:</span> <span>[]</span>\n  <span>link</span><span>:</span> <span>http://it.slashdot.org/story/15/05/08/1423208/cybersecurity-company-extorted-its-clients-says-whistleblower?utm_source=rss1.0mainlinkanon&amp;utm_medium=feed</span>\n  <span>published</span><span>:</span> <span>2015-05-08T15:02:00Z</span>\n<span>-</span> <span>uri</span><span>:</span> <span>http://news.slashdot.org/story/15/05/08/1414226/top-advisor-to-australian-govt-says-climate-change-is-a-un-conspiracy?utm_source=rss1.0mainlinkanon&amp;utm_medium=feed</span></code></pre>\n</div> \n<h2>Conclusion</h2> \n<p>So now we have a fully functional application that is capable of transforming and serving feeds in different formats. Technically we can’t really call it an aggregator yet, as it doesn’t cache feed history, however that functionality will be explored in the next instalment.</p> \n<h2>References</h2> \n<p>You can find the <a href=\"https://github.com/figurate/figurate-examples/tree/master/feed-aggregator\">full source code</a> for this example on <a href=\"https://github.com/figurate\">GitHub</a></p> \n<p>Other libraries used in this example are as follows:</p> \n<ul> \n <li><a href=\"https://github.com/benfortuna/newsagent\">Newsagent</a></li> \n <li><a href=\"http://www.amdatu.org/components/web.html\">Amdatu</a></li> \n <li><a href=\"http://felix.apache.org/\">Felix</a></li> \n</ul>",
      "text": [],
      "link": "http://figurate.org/2015/05/02/building-a-feed-aggregator.html",
      "published": "2015-05-02T00:00:00+0000"
    }
  ],
  "enclosures": [],
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
  description: "<p>Feed aggregation is perhaps one of the simplest services you can\
    \ provide on the Internet. It (usually) doesn’t require authentication, datasets\
    \ are readily available, and the data itself is simple enough that it can be stored\
    \ and rendered relatively easily.</p> \n<p>However it is still not trivial to\
    \ build a robust and scalable feed aggregation service, as there are many concerns\
    \ one must consider. Not all feeds conform to the same standards, and even within\
    \ a single standard there are different levels of feed compliance. Scheduled feed\
    \ updates and cleansing also require much consideration, along with caching strategies,\
    \ persistence, and supported rendering formats. Figurate makes these concerns\
    \ much simpler by providing pre-packaged Components and Constellations that take\
    \ each of these concerns into account.</p> \n<h2>Creating a project</h2> \n<p>Starting\
    \ with a new Gradle project, your build script should look like this:</p> \n<div>\n\
    \ <pre><code><span>buildscript</span> <span>{</span>\n    <span>repositories</span>\
    \ <span>{</span>\n        <span>mavenCentral</span><span>()</span>\n        <span>maven</span>\
    \ <span>{</span>\n            <span>url</span> <span>'https://oss.sonatype.org/content/repositories/snapshots'</span>\n\
    \        <span>}</span>\n    <span>}</span>\n    <span>dependencies</span> <span>{</span>\n\
    \        <span>classpath</span> <span>'org.figurate:gradle-figurate-plugin:master-SNAPSHOT'</span><span>,</span>\n\
    \                <span>'org.figurate:gradle-constellations-plugin:master-SNAPSHOT'</span>\n\
    \    <span>}</span>\n<span>}</span>\n\n<span>apply</span> <span>plugin:</span>\
    \ <span>'figurate'</span>\n<span>apply</span> <span>plugin:</span> <span>'constellations'</span>\n\
    \n<span>repositories</span> <span>{</span>\n    <span>mavenCentral</span><span>()</span>\n\
    \    <span>maven</span> <span>{</span>\n        <span>url</span> <span>'https://oss.sonatype.org/content/repositories/snapshots'</span>\n\
    \    <span>}</span>\n<span>}</span></code></pre>\n</div> \n<h2>Adding Constellations</h2>\
    \ \n<p>To provide the features required for our aggregator, we add the relevant\
    \ Constellations to our build dependencies. In this instance we require the RSS,\
    \ HTTP, and <a href=\"http://felix.apache.org/\">Felix</a> OSGi runtime Constellations.\
    \ Note how we use the <strong>bundle</strong> dependency scope, which will ensure\
    \ the OSGi bundles in each Constellation are included in the final application\
    \ distribution.</p> \n<div>\n <pre><code><span>...</span>\n<span>apply</span>\
    \ <span>plugin:</span> <span>'figurate'</span>\n<span>apply</span> <span>plugin:</span>\
    \ <span>'constellations'</span>\n\n<span>repositories</span> <span>{</span>\n\
    \    <span>mavenCentral</span><span>()</span>\n    <span>maven</span> <span>{</span>\n\
    \        <span>url</span> <span>'https://oss.sonatype.org/content/repositories/snapshots'</span>\n\
    \    <span>}</span>\n<span>}</span>\n\n<span>dependencies</span> <span>{</span>\n\
    \    <span>bundle</span> <span>configurations</span><span>.</span><span>constellation</span><span>$felix</span><span>,</span>\
    \ <span>// OSGi runtime</span>\n            <span>configurations</span><span>.</span><span>constellation</span><span>$webconsole</span><span>,</span>\
    \ <span>// OSGi dashboard application</span>\n            <span>configurations</span><span>.</span><span>constellation</span><span>$http</span><span>,</span>\
    \ <span>// embedded Jetty HTTP server</span>\n            <span>configurations</span><span>.</span><span>constellation</span><span>$rest</span><span>,</span>\
    \ <span>// REST support from Amdatu</span>\n            <span>configurations</span><span>.</span><span>constellation</span><span>$jackson</span><span>,</span>\
    \ <span>// JSON databinding support for the REST framework</span>\n          \
    \  <span>configurations</span><span>.</span><span>constellation</span><span>$logback</span><span>,</span>\
    \ <span>// logging framework</span>\n            <span>'org.codehaus.groovy:groovy-all:2.4.3'</span><span>,</span>\
    \ <span>// Groovy runtime</span>\n            <span>configurations</span><span>.</span><span>constellation</span><span>$rss</span>\
    \ <span>// RSS parsing and REST-ful endpoints from Newsagent library</span>\n\n\
    \    <span>runtime</span> <span>configurations</span><span>.</span><span>constellation</span><span>$logback</span>\n\
    <span>}</span></code></pre>\n</div> \n<h2>Configuring Components</h2> \n<p><code>TODO</code></p>\
    \ \n<h2>Launching the application</h2> \n<p>Finally we need to specify any custom\
    \ configuration for the OSGi runtime itself. Typically it will have sensible defaults,\
    \ however we still occasionally will need to override these defaults, or provide\
    \ configuration where no default values are possible.</p> \n<div>\n <pre><code><span>...</span>\n\
    <span>apply</span> <span>plugin:</span> <span>'groovy'</span>\n<span>apply</span>\
    \ <span>plugin:</span> <span>'osgi'</span>\n<span>apply</span> <span>plugin:</span>\
    \ <span>'figurate'</span>\n<span>apply</span> <span>plugin:</span> <span>'constellations'</span>\n\
    \n<span>repositories</span> <span>{</span>\n    <span>mavenCentral</span><span>()</span>\n\
    \    <span>maven</span> <span>{</span>\n        <span>url</span> <span>'https://oss.sonatype.org/content/repositories/snapshots'</span>\n\
    \    <span>}</span>\n<span>}</span>\n\n<span>dependencies</span> <span>{</span>\n\
    \    <span>bundle</span> <span>configurations</span><span>.</span><span>constellation</span><span>$felix</span><span>,</span>\
    \ <span>// OSGi runtime</span>\n            <span>configurations</span><span>.</span><span>constellation</span><span>$webconsole</span><span>,</span>\
    \ <span>// OSGi dashboard application</span>\n            <span>configurations</span><span>.</span><span>constellation</span><span>$http</span><span>,</span>\
    \ <span>// embedded Jetty HTTP server</span>\n            <span>configurations</span><span>.</span><span>constellation</span><span>$rest</span><span>,</span>\
    \ <span>// REST support from Amdatu</span>\n            <span>configurations</span><span>.</span><span>constellation</span><span>$jackson</span><span>,</span>\
    \ <span>// JSON databinding support for the REST framework</span>\n          \
    \  <span>configurations</span><span>.</span><span>constellation</span><span>$logback</span><span>,</span>\
    \ <span>// logging framework</span>\n            <span>'org.codehaus.groovy:groovy-all:2.4.3'</span><span>,</span>\
    \ <span>// Groovy runtime</span>\n            <span>configurations</span><span>.</span><span>constellation</span><span>$rss</span>\
    \ <span>// RSS parsing and REST-ful endpoints from Newsagent library</span>\n\n\
    \    <span>runtime</span> <span>configurations</span><span>.</span><span>constellation</span><span>$logback</span>\n\
    <span>}</span>\n\n<span>...</span>\n\n<span>launcherConfig</span> <span>{</span>\n\
    \    <span>configProps</span> <span>&lt;&lt;</span> <span>[</span>\n        <span>//\
    \ use the next available port from 8080 onwards</span>\n        <span>'org.osgi.service.http.port'</span>\
    \        <span>:</span> <span>'[8080,8099)'</span><span>,</span>\n        <span>'org.apache.felix.http.jettyEnabled'</span><span>:</span>\
    \ <span>'true'</span><span>,</span>\n        <span>'org.apache.felix.http.whiteboardEnabled'</span><span>:</span>\
    \ <span>'true'</span><span>,</span>\n        <span>'org.apache.felix.http.debug'</span>\
    \       <span>:</span> <span>'true'</span><span>,</span>\n    <span>]</span>\n\
    <span>}</span></code></pre>\n</div> \n<p>To launch the application from the command\
    \ line we use the <strong>run</strong> task implicitly included via the <a href=\"\
    http://gradle.org/docs/current/userguide/application_plugin.html\">Gradle Application\
    \ Plugin</a>:</p> \n<p><code>gradlew run</code></p> \n<h2>The Result</h2> \n<p>We\
    \ can test our results in a browser, or with <a href=\"http://curl.haxx.se/docs/manpage.html\"\
    >curl</a> as follows:</p> \n<div>\n <pre><code>curl http://localhost:8080/feed?url<span>=</span>http://rss.slashdot.org/Slashdot/slashdotMain</code></pre>\n\
    </div> \n<p>Output:</p> \n<div>\n <pre><code><span>{</span><span>\"entries\"</span><span>:[{</span><span>\"\
    uri\"</span><span>:</span><span>\"http://news.slashdot.org/story/15/05/08/1414226/top-advisor-to-australian-govt-says-climate-change-is-a-un-conspiracy?utm_source=rss1.0mainlinkanon&amp;utm_medium=feed\"\
    </span><span>,</span><span>\"title\"</span><span>:</span><span>\"Top Advisor To\
    \ Australian Gov't Says Climate Change is a UN Conspiracy\"</span><span>,</span><span>\"\
    description\"</span><span>:</span><span>\"An anonymous reader writes: Maurice\
    \ Newman, the top business advisor to conservative Australian Prime Minister Tony\
    \ Abbott, today published an opinion piece (paywalled) in which he claims, \\\"\
    It's a well-kept secret, but 95 per cent of the climate models ... have been found\
    \ ... to be in error.\\\" He goes on to write \\\"This is not about facts or logic.\
    \ It's about a new world order under the control of the UN.\\\" While Newman's\
    \ 'skeptical' views have long been on record, it's unclear when he came to believe\
    \ in this vast global conspiracy. Last year, the Abbott government removed Australia's\
    \ Emissions Trading Scheme, and recently gave $4 million in funding to contrarian\
    \ Bjorn Lomberg, while cutting hundreds of millions of dollars from science across\
    \ the country.\\n&lt;p&gt;&lt;/p&gt;\\n&lt;div&gt; \\n &lt;a href=\\\"http://twitter.com/home?status=Top+Advisor+To+Australian+Gov't+Says+Climate+Change+is+a+UN+Conspiracy%3A+http%3A%2F%2Fbit.ly%2F1Kqd2vY\\\
    \"&gt;&lt;img src=\\\"http://a.fsdn.com/sd/twitter_icon_large.png\\\"&gt;&lt;/a&gt;\
    \ \\n &lt;a href=\\\"http://www.facebook.com/sharer.php?u=http%3A%2F%2Fnews.slashdot.org%2Fstory%2F15%2F05%2F08%2F1414226%2Ftop-advisor-to-australian-govt-says-climate-change-is-a-un-conspiracy%3Futm_source%3Dslashdot%26utm_medium%3Dfacebook\\\
    \"&gt;&lt;img src=\\\"http://a.fsdn.com/sd/facebook_icon_large.png\\\"&gt;&lt;/a&gt;\
    \ \\n &lt;a href=\\\"http://plus.google.com/share?url=http://news.slashdot.org/story/15/05/08/1414226/top-advisor-to-australian-govt-says-climate-change-is-a-un-conspiracy?utm_source=slashdot&amp;amp;utm_medium=googleplus\\\
    \"&gt;&lt;img src=\\\"http://www.gstatic.com/images/icons/gplus-16.png\\\" alt=\\\
    \"Share on Google+\\\"&gt;&lt;/a&gt; \\n&lt;/div&gt;\\n&lt;p&gt;&lt;/p&gt;\\n&lt;p&gt;&lt;a\
    \ href=\\\"http://news.slashdot.org/story/15/05/08/1414226/top-advisor-to-australian-govt-says-climate-change-is-a-un-conspiracy?utm_source=rss1.0moreanon&amp;amp;utm_medium=feed\\\
    \"&gt;Read more of this story&lt;/a&gt; at Slashdot.&lt;/p&gt;\"</span><span>,</span><span>\"\
    text\"</span><span>:[],</span><span>\"link\"</span><span>:</span><span>\"http://news.slashdot.org/story/15/05/08/1414226/top-advisor-to-australian-govt-says-climate-change-is-a-un-conspiracy?utm_source=rss1.0mainlinkanon&amp;utm_medium=feed\"\
    </span><span>,</span><span>\"published\"</span><span>:</span><span>\"2015-05-08T14:20:00+0000\"\
    </span><span>},</span><span>...</span></code></pre>\n</div> \n<p>The default output\
    \ is in JSON, however we can ask for YAML by adding the appropriate header:</p>\
    \ \n<div>\n <pre><code>curl -H <span>\"Accept: application/yaml\"</span> http://localhost:8080/feed?url<span>=</span>http://rss.slashdot.org/Slashdot/slashdotMain</code></pre>\n\
    </div> \n<p>Output:</p> \n<div>\n <pre><code><span>entries</span><span>:</span>\n\
    <span>-</span> <span>uri</span><span>:</span> <span>http://it.slashdot.org/story/15/05/08/1423208/cybersecurity-company-extorted-its-clients-says-whistleblower?utm_source=rss1.0mainlinkanon&amp;utm_medium=feed</span>\n\
    \  <span>title</span><span>:</span> <span>Cybersecurity Company Extorted Its Clients,\
    \ Says Whistleblower</span>\n  <span>description</span><span>:</span> <span>\"\
    An</span><span> </span><span>anonymous</span><span> </span><span>reader</span><span>\
    \ </span><span>writes:</span><span> </span><span>Richard</span><span> </span><span>Wallace</span><span>\
    \ </span><span>used</span><span> </span><span>to</span><span> </span><span>be</span><span>\
    \ </span><span>an</span><span> </span><span>investigator\\</span>\n    <span>\\\
    \ for</span><span> </span><span>Tiversa,</span><span> </span><span>a</span><span>\
    \ </span><span>cybersecurity</span><span> </span><span>company</span><span> </span><span>that</span><span>\
    \ </span><span>sells</span><span> </span><span>services</span><span> </span><span>like</span><span>\
    \ </span><span>\\\"breach</span><span> </span><span>protection\\\"\\</span>\n\
    \    <span>\\ and</span><span> </span><span>\\\"incident</span><span> </span><span>response.\\\
    \"</span><span> </span><span>These</span><span> </span><span>days,</span><span>\
    \ </span><span>Wallace</span><span> </span><span>is</span><span> </span><span>testifying</span><span>\
    \ </span><span>in</span><span> </span><span>federal</span><span> </span><span>court\\\
    </span>\n    <span>\\ that</span><span> </span><span>Tiversa</span><span> </span><span>faked</span><span>\
    \ </span><span>breaches</span><span> </span><span>to</span><span> </span><span>encourage</span><span>\
    \ </span><span>sales,</span><span> </span><span>and</span><span> </span><span>extorted</span><span>\
    \ </span><span>clients</span><span> </span><span>that</span><span> </span><span>weren't\\\
    </span>\n    <span>\\ interested.</span><span> </span><span>For</span><span> </span><span>example,</span><span>\
    \ </span><span>Wallace</span><span> </span><span>said</span><span> </span><span>Tiversa</span><span>\
    \ </span><span>targeted</span><span> </span><span>a</span><span> </span><span>cancer</span><span>\
    \ </span><span>testing</span><span> </span><span>center\\</span>\n    <span>\\\
    \ called</span><span> </span><span>LabMD</span><span> </span><span>in</span><span>\
    \ </span><span>2010,</span><span> </span><span>tapping</span><span> </span><span>into</span><span>\
    \ </span><span>their</span><span> </span><span>computers</span><span> </span><span>and</span><span>\
    \ </span><span>downloading</span><span> </span><span>medical</span><span> </span><span>records.\\\
    </span>\n    <span>\\ Tiversa</span><span> </span><span>then</span><span> </span><span>used</span><span>\
    \ </span><span>those</span><span> </span><span>records</span><span> </span><span>as</span><span>\
    \ </span><span>evidence</span><span> </span><span>to</span><span> </span><span>convince</span><span>\
    \ </span><span>LabMD</span><span> </span><span>they</span><span> </span><span>had</span><span>\
    \ </span><span>been\\</span>\n    <span>\\ hacked,</span><span> </span><span>offering</span><span>\
    \ </span><span>its</span><span> </span><span>\\\"incident</span><span> </span><span>response\\\
    \"</span><span> </span><span>service</span><span> </span><span>at</span><span>\
    \ </span><span>the</span><span> </span><span>same</span><span> </span><span>time.</span><span>\
    \ </span><span>LabMD</span><span> </span><span>didn't\\</span>\n    <span>\\ fall</span><span>\
    \ </span><span>for</span><span> </span><span>it,</span><span> </span><span>so</span><span>\
    \ </span><span>Tiversa</span><span> </span><span>told</span><span> </span><span>the</span><span>\
    \ </span><span>FTC</span><span> </span><span>about</span><span> </span><span>the</span><span>\
    \ </span><span>\\\"hack.\\\"</span><span> </span><span>The</span><span> </span><span>FTC,</span><span>\
    \ </span><span>none-the-wiser,\\</span>\n    <span>\\ went</span><span> </span><span>after</span><span>\
    \ </span><span>LabMD</span><span> </span><span>in</span><span> </span><span>court,</span><span>\
    \ </span><span>eventually</span><span> </span><span>destroying</span><span> </span><span>the</span><span>\
    \ </span><span>business.</span><span> </span><span>Wallace</span><span> </span><span>has</span><span>\
    \ </span><span>also\\</span>\n    <span>\\ cast</span><span> </span><span>suspicion</span><span>\
    \ </span><span>on</span><span> </span><span>reports</span><span> </span><span>Tiversa</span><span>\
    \ </span><span>has</span><span> </span><span>issued,</span><span> </span><span>including</span><span>\
    \ </span><span>one</span><span> </span><span>saying</span><span> </span><span>President\\\
    </span>\n    <span>\\ Obama's</span><span> </span><span>helicopter</span><span>\
    \ </span><span>blueprints</span><span> </span><span>were</span><span> </span><span>found</span><span>\
    \ </span><span>on</span><span> </span><span>Iranian</span><span> </span><span>computers.\\\
    n&lt;p&gt;&lt;/p&gt;\\n&lt;div&gt;\\</span>\n    <span>\\ \\n</span><span> </span><span>&lt;a</span><span>\
    \ </span><span>href=\\\"http://twitter.com/home?status=Cybersecurity+Company+Extorted+Its+Clients%2C+Says+Whistleblower%3A+http%3A%2F%2Fbit.ly%2F1Kqfoeh\\\
    \"\\</span>\n    <span>&gt;&lt;img</span><span> </span><span>src=\\\"http://a.fsdn.com/sd/twitter_icon_large.png\\\
    \"&gt;&lt;/a&gt;</span><span> </span><span>\\n</span><span> </span><span>&lt;a</span><span>\
    \ </span><span>href=\\\"http://www.facebook.com/sharer.php?u=http%3A%2F%2Fit.slashdot.org%2Fstory%2F15%2F05%2F08%2F1423208%2Fcybersecurity-company-extorted-its-clients-says-whistleblower%3Futm_source%3Dslashdot%26utm_medium%3Dfacebook\\\
    \"\\</span>\n    <span>&gt;&lt;img</span><span> </span><span>src=\\\"http://a.fsdn.com/sd/facebook_icon_large.png\\\
    \"&gt;&lt;/a&gt;</span><span> </span><span>\\n</span><span> </span><span>&lt;a</span><span>\
    \ </span><span>href=\\\"\\</span>\n    <span>http://plus.google.com/share?url=http://it.slashdot.org/story/15/05/08/1423208/cybersecurity-company-extorted-its-clients-says-whistleblower?utm_source=slashdot&amp;amp;utm_medium=googleplus\\\
    \"\\</span>\n    <span>&gt;&lt;img</span><span> </span><span>src=\\\"http://www.gstatic.com/images/icons/gplus-16.png\\\
    \"</span><span> </span><span>alt=\\\"Share</span><span> </span><span>on\\</span>\n\
    \    <span>\\ Google+\\\"&gt;&lt;/a&gt;</span><span> </span><span>\\n&lt;/div&gt;\\\
    n&lt;p&gt;&lt;/p&gt;\\n&lt;p&gt;&lt;a</span><span> </span><span>href=\\\"http://it.slashdot.org/story/15/05/08/1423208/cybersecurity-company-extorted-its-clients-says-whistleblower?utm_source=rss1.0moreanon&amp;amp;utm_medium=feed\\\
    \"\\</span>\n    <span>&gt;Read</span><span> </span><span>more</span><span> </span><span>of</span><span>\
    \ </span><span>this</span><span> </span><span>story&lt;/a&gt;</span><span> </span><span>at</span><span>\
    \ </span><span>Slashdot.&lt;/p&gt;\"</span>\n  <span>text</span><span>:</span>\
    \ <span>[]</span>\n  <span>link</span><span>:</span> <span>http://it.slashdot.org/story/15/05/08/1423208/cybersecurity-company-extorted-its-clients-says-whistleblower?utm_source=rss1.0mainlinkanon&amp;utm_medium=feed</span>\n\
    \  <span>published</span><span>:</span> <span>2015-05-08T15:02:00Z</span>\n<span>-</span>\
    \ <span>uri</span><span>:</span> <span>http://news.slashdot.org/story/15/05/08/1414226/top-advisor-to-australian-govt-says-climate-change-is-a-un-conspiracy?utm_source=rss1.0mainlinkanon&amp;utm_medium=feed</span></code></pre>\n\
    </div> \n<h2>Conclusion</h2> \n<p>So now we have a fully functional application\
    \ that is capable of transforming and serving feeds in different formats. Technically\
    \ we can’t really call it an aggregator yet, as it doesn’t cache feed history,\
    \ however that functionality will be explored in the next instalment.</p> \n<h2>References</h2>\
    \ \n<p>You can find the <a href=\"https://github.com/figurate/figurate-examples/tree/master/feed-aggregator\"\
    >full source code</a> for this example on <a href=\"https://github.com/figurate\"\
    >GitHub</a></p> \n<p>Other libraries used in this example are as follows:</p>\
    \ \n<ul> \n <li><a href=\"https://github.com/benfortuna/newsagent\">Newsagent</a></li>\
    \ \n <li><a href=\"http://www.amdatu.org/components/web.html\">Amdatu</a></li>\
    \ \n <li><a href=\"http://felix.apache.org/\">Felix</a></li> \n</ul>"
  text: []
  link: http://figurate.org/2015/05/02/building-a-feed-aggregator.html
  published: 2015-05-02T00:00:00Z
enclosures: []
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

[Felix]: http://felix.apache.org/
[Gradle Application Plugin]: http://gradle.org/docs/current/userguide/application_plugin.html
[curl]: http://curl.haxx.se/docs/manpage.html
[full source code]: https://github.com/figurate/figurate-examples/tree/master/feed-aggregator
[GitHub]: https://github.com/figurate
[Newsagent]: https://github.com/benfortuna/newsagent
[Amdatu]: http://www.amdatu.org/components/web.html
[Felix]: http://felix.apache.org/
