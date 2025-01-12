---
title: Wavefront for Spring Boot 3
keywords:
tags:
sidebar: doc_sidebar
permalink: wavefront_springboot3.html
summary: Examine Spring Boot 3 data in Wavefront dashboards and charts
---

Wavefront for Spring Boot allows you to quickly configure your environment, so Spring Boot 3 components send metrics, histograms, and traces or spans to the Wavefront service.

## Features

If you use Wavefront for Spring Boot:

* A large number of metrics are exposed by default. See the [Spring Boot documentation on Supported Metrics](https://docs.spring.io/spring-boot/docs/3.0.x/reference/html/actuator.html#actuator.metrics.supported) for details.
* You have tracing support for all the common Spring Boot components, such as Spring MVC, Spring Web, Spring Async, Feign, Hysterix, JMS, JDBC, Mongo, Zuul, Reactor, RxJava, Redis, Logging, Spring Messaging, and RabbitMQ.
* The distributed tracing spans in your applications are automatically converted to the Wavefront span format.

## Sending Data from Spring Boot into Wavefront

You can send data from your Spring Boot applications into Wavefront using the Wavefront for Spring Boot Starter (all users) or the Wavefront Spring Boot integration (Wavefront customers and trial users).

* **Wavefront for Spring Boot Starter**
  <br/> If you configure your application with the Wavefront for Spring Boot starter, you can send metrics, histograms, and traces/spans to the Wavefront service. Once the data is in Wavefront, you can view your data, find hotspots, and gather more data.
  - **Freemium**: All users can run the Spring Boot Starter with the default settings to view their data in the Wavefront Freemium instance. Certain limitations apply, for example, alerts are not available.
  - **Wavefront Customer or Trial User**: Wavefront customers or trial users can modify the default Wavefront Spring Boot Starter to send data to their cluster. You can sign up for a [free 30-day trial here](https://tanzu.vmware.com/observability).
* **Wavefront Spring Boot Integration**: Wavefront customers and trial users can access the Wavefront Spring Boot integration directly from their clusters.

## Dashboards

After you complete the setup, you can examine the data in the Wavefront dashboards.

<table style="width: 100%;">
<tbody>
<tr>
<td width="50%">The <strong>Spring Boot Inventory dashboard</strong> provides real-time visibility into your Spring Boot environment.
<ul><li>
This is the default dashboard you see when you run the Spring Boot initializer. </li>
<li>You can also access this dashboard from the Spring Boot integration <strong>Dashboards</strong> tab.</li></ul> </td>
<td width="50%"><img src="/images/springboot_metrics.png" alt="screenshot of spring boot dashboard">
</td></tr>
<tr>
<td width="50%">The <strong> Wavefront Traces Browser</strong> allows you to examine traces for your applications. You can access this browser if you have trace data flowing:
<ul><li>From the Spring Boot Inventory dashboard. </li>
<li>From the Spring Boot integration <strong>Dashboards</strong> tab.</li></ul>
<img src="/images/springboot_trace_data.png" alt="Tracing section has link to Application Dashboard">
</td>
<td width="50%"><img src="/images/springboot_span_logs_pet_clinic.png" alt="screenshot of the traces browser">
</td></tr>
</tbody>
</table>


<!---Not sure this table makes sense any more. It's really about Freemium vs. Customer/trial.
<table style="width: 100%;">
<tbody>
<thead>
<tr><th width="20%">&nbsp;</th><th width="40%">Wavefront Spring Boot Integration</th><th width="40%">Wavefront Spring Boot Starter</th></tr>
</thead>
<tr>
<td><strong>Setup</strong></td>
<td>
Customize the configuration file (integration Setup tab) or set up Maven or Gradle.</td>
<td>Auto Configuration (plus: Custom Configuration options)</td>
</tr>
<tr>
<td><strong>Supported telemetry</strong></td>
<td>Metrics, histograms, and traces/spans</td>
<td>Metrics, histograms, and traces/spans</td>
</tr>
<tr>
<td><strong>Ingestion options</strong></td>
<td>Proxy, direct ingestion </td>
<td>Proxy, direct ingestion </td>
</tr>
<tr>
<td><strong>Dashboards</strong></td>
<td>Spring Boot Inventory dashboard for metrics, Spring Boot Application dashboard for traces. </td>
<td>Spring Boot Inventory dashboard for metrics, Spring Boot Application dashboard for traces.</td>
</tr>
<tr>
<td><strong>Modify dashboards</strong></td>
<td>Clone and customize the predefined dashboard. Create dashboards, add charts, specify metrics.  </td>
<td>Clone and customize the predefined dashboard. Create dashboards, add charts, specify metrics.</td>
</tr>
<tr>
<td><strong>Alerts</strong></td>
<td>Create smart alerts that dynamically filter noise and capture true anomalies.</td>
<td>Alerts are not available on this free cluster.</td>
</tr>
<tr>
<td><strong>Actuator endpoint</strong></td>
<td>Not available.</td>
<td markdown="span">Dedicated actuator endpoint at <br/><code><b>/actuator/wavefront</b></code> to access your Wavefront dashboard.</td>
</tr>
</tbody>
</table>
--->



<!-- ## Wavefront Spring Boot Starter Setup

Anyone can get started using the Wavefront Spring Boot Starter.

* The easiest way to get started is to create a new project on [start.spring.io](https://start.springboot.io).
  - Select Spring Boot 2.3 or later and your favorite build system
  - Include at least the `Wavefront` entry.
  - If you want to opt-in for tracing support, add the `Spring Cloud Sleuth` entry as well.
* If you already have a Spring Boot application, you can also use [start.spring.io](https://start.springboot.io) to explore the project from your browser. You can determine the setup for the Spring Boot generation that your  project is using.

Here are some things to know before you start:

* **Ingestion Method**: Wavefront for Spring Boot sends data to Wavefront via [direct ingestion](direct_ingestion.html) by default. You can [configure your application to send data via the Wavefront proxy](#proxy).
* **Target**: Wavefront for Spring Boot sends data to the Wavefront Freemium instance by default. You can [configure your application to send data to your Wavefront instance](#wf_instance).
* **Account**: By default the starter sends you to the Freemium instance,  auto-negotiates an account, and saves the API token in the `~/.wavefront_freemium` file in your home directory. If you customize the starter to go to your Wavefront instance (see Step 2 below) you need to include an API token for that instance. -->

<!---
![Ways to get started with the Wavefront for Spring Boot offering](/images/spring_boot_getting_started.png)--->

## Getting Started

Getting started is easy. Here are some things to know before you start:

* **Ingestion Method**: Wavefront for Spring Boot sends data to Wavefront via [direct ingestion](direct_ingestion.html) by default. You can [configure your application to send data via the Wavefront proxy](#proxy).
* **Target**: Wavefront for Spring Boot sends data to the Wavefront Freemium instance. You can change the default behavior and  [configure your application to send data to your Wavefront instance](#step-2-optional-specify-your-wavefront-instance).
* **Account**: By default, the starter sends you to the Freemium instance, auto-negotiates an account, and saves the API token in the `~/.wavefront_freemium` file in your home directory. If you customize the starter to go to your Wavefront instance (see Step 2 below), you need to include an API token for that instance.

### Prerequisites for Wavefront Spring Boot Starter

* Spring Boot 3.0.0 or above
  <a name="versionCompatibility"></a>
  <table style="width: 70%;">
  <tbody>
  <thead>
  <tr><th width="35%">Spring Boot Version</th><th width="35%">Wavefront for Spring Boot Version</th></tr>
  </thead>
  <tr>
  <td>3.0.x</td>
  <td>3.0.x</td>
  </tr>
  </tbody>
  </table>
* Java 17 or above
* Maven 3.5+ or Gradle 7.5 or later
  <br/>See the[System Requirements](https://docs.spring.io/spring-boot/docs/3.0.x/reference/html/getting-started.html#getting-started.system-requirements) in the Spring Boot documentation.

{% include note.html content="If you currently use Spring Boot 2, see our [Spring Boot 2 documentation](wavefront_springboot.html)."%}

### Step 1: Initialize and Configure Your Project

Initialize a new project using the Spring Initializer or add the required dependencies to an existing Spring project to send data to Wavefront.

<ul id="profileTabs" class="nav nav-tabs">
    <li class="active">
      <a href="#new" data-toggle="tab">Initialize a New Project</a>
    </li>
    <li>
      <a href="#existing" data-toggle="tab">Initialize an Existing Project</a>
    </li>
</ul>
<div class="tab-content">
  <div role="tabpanel" class="tab-pane active" id="new">
    <p>Follow these steps:</p>
    <ol>
      <li markdown="span">
        Navigate to [https://start.spring.io](https://start.spring.io/).
      </li>
      <li markdown="span">
        Select 3.0.0 or later as the Spring Boot version and define the other parameters for your project.
          <br/> ![Spring Initializr](images/spring_boot3_initializr.png)
      </li>
      <li>
        Click <b>Add dependency</b> and select Wavefront from the dependency list.
          <br/> <img src="/images/spring_boot3_wavefront_depdendency.png" alt="Wavefront dependency">
          {{site.data.alerts.important}}
            <p>
              If you are using the Wavefront dependency and there is no web service in the application, the application stops soon as it starts, and data is not sent to Wavefront. In such cases, make sure to add a dependency under the Web category, such as the Spring Web dependency.
            </p>
          {{site.data.alerts.end}}
      </li>
      <li markdown="span">
        Optionally, add Distributed Tracing as a dependency to send trace data to Wavefront.
        <br/> ![Distributed Tracing dependency](/images/spring_boot3_dt_depdendency.png)
      </li>
      <li>
        Click <b>Generate</b> to download the project as a Zip file.
        {{site.data.alerts.tip}}
          To check out all the dependencies and the versions used in your project, click <b>Explore</b>.
        {{site.data.alerts.end}}
      </li>
      <li>
         Open the project, add the application logic, and start the project.<br/>
      </li>
    </ol>
  </div>

  <div role="tabpanel" class="tab-pane"  id="existing">
    <p>Follow these steps:</p>
    <ol>
      <li> Add the Wavefront dependency.
        <ul id="profileTabs" class="nav nav-tabs">
            <li class="active"><a href="#maven" data-toggle="tab">Maven</a></li>
            <li><a href="#gradle" data-toggle="tab">Gradle</a></li>
        </ul>
          <div class="tab-content">
            <div role="tabpanel" class="tab-pane active" id="maven">
              <p>Open your application and add the following code to your <code>pom.xml</code> file.   </p>
              <pre>
&lt;dependency&gt;
  &lt;groupId&gt;com.wavefront&lt;/groupId&gt;
  &lt;artifactId&gt;wavefront-spring-boot-starter&lt;/artifactId&gt;
  &lt;scope&gt;runtime&lt;/scope&gt;
&lt;/dependency&gt;
              </pre>
           </div>

           <div role="tabpanel" class="tab-pane" id="gradle">
             <p>Open your application and add the following code to your <code>build.gradle</code> file.  </p>
             <pre>
dependencies {
  ...
  runtimeOnly 'com.wavefront:wavefront-spring-boot-starter'
}
             </pre>
           </div>
         </div>
      </li>

      <li>
        Import the Wavefront for Spring Boot Bill of Materials (BOM) to your project.
        {{site.data.alerts.tip}}
          <p>The Wavefront for Spring Boot dependency needs to be compatible with the Spring Boot release version. Therefore, replace <code>$releaseVersion</code> with the correct dependency version. See <a href="#versionCompatibility">System Requirements</a> to get the correct dependency version.</p>
        {{site.data.alerts.end}}
        <ul id="profileTabs" class="nav nav-tabs">
            <li class="active"><a href="#mavenbom" data-toggle="tab">Maven</a></li>
            <li><a href="#gradlebom" data-toggle="tab">Gradle</a></li>
        </ul>
          <div class="tab-content">
            <div role="tabpanel" class="tab-pane active" id="mavenbom">
              <pre>

&lt;dependencyManagement&gt;
  &lt;dependencies&gt;
  .....
    &lt;dependency&gt;
      &lt;groupId&gt;com.wavefront&lt;/groupId&gt;
      &lt;artifactId&gt;wavefront-spring-boot-bom&lt;/artifactId&gt;
      &lt;version&gt;$releaseVersion&lt;/version&gt;
      &lt;type&gt;pom&lt;/type&gt;
      &lt;scope&gt;import&lt;/scope&gt;
    &lt;/dependency&gt;
  .....
  &lt;/dependencies&gt;
&lt;/dependencyManagement&gt;
              </pre>
            </div>

            <div role="tabpanel" class="tab-pane" id="gradlebom">
              <pre>
dependencyManagement {
  imports {
    mavenBom "com.wavefront:wavefront-spring-boot-bom:$releaseVersion"
  }
}
            </pre>
          </div>
        </div>
      </li>

      <li>
        If you want to send trace data to Wavefront using Micrometer Tracing, add the following dependencies.
        <ul id="profileTabs" class="nav nav-tabs">
            <li class="active"><a href="#maven-dt" data-toggle="tab">Maven</a></li>
            <li><a href="#gradle-dt" data-toggle="tab">Gradle</a></li>
        </ul>
          <div class="tab-content">
            <div role="tabpanel" class="tab-pane active" id="maven-dt">
              <pre>
&lt;dependency&gt;
  &lt;groupId&gt;io.micrometer&lt;/groupId&gt;
  &lt;artifactId&gt;micrometer-tracing-bridge-brave&lt;/artifactId&gt;
&lt;/dependency&gt;
&lt;dependency&gt;
  &lt;groupId&gt;io.micrometer&lt;/groupId&gt;
  &lt;artifactId&gt;micrometer-tracing-reporter-wavefront&lt;/artifactId&gt;
  &lt;scope&gt;runtime&lt;/scope&gt;
&lt;/dependency&gt;
              </pre>
            </div>

            <div role="tabpanel" class="tab-pane" id="gradle-dt">
              <pre>
dependencies {
  ...
  implementation 'io.micrometer:micrometer-tracing-bridge-brave'
  runtimeOnly 'io.micrometer:micrometer-tracing-reporter-wavefront'
}
              </pre>
            </div>
          </div>
      </li>
    </ol>
  </div>
</div>

### Step 2 (Optional): Specify Your Wavefront Instance

By default, the Wavefront Spring Boot Starter creates an account for you and sends data to the Freemium instance. If you already have a Wavefront account, you can send data there instead by specifying the `uri` and `api-token` properties as follows:

```
management.wavefront.api-token=$API_Token
management.wavefront.uri=$wavefront_instance
```

* `$API_Token` is a valid [API token for your Wavefront instance](users_account_managing.html#generate-an-api-token).
* `$wavefront_instance` is the name of your Wavefront instance, for example, `https://example.wavefront.com`.


### Step 3:  View Your Data in Wavefront

To view your data, you first run your project from the command line, and then click the link that directs you to Wavefront. Follow these steps:

1. Run your project.
    {% include note.html content="If you're using the Freemium instance, an account is auto-negotiated or restored from `~/.wavefront_freemium` in your home directory each time the application starts. Otherwise, you're directed to the instance you specified. "%}
    <ul id="profileTabs" class="nav nav-tabs">
        <li class="active"><a href="#mavenrun" data-toggle="tab">Maven</a></li>
        <li><a href="#gradlerun" data-toggle="tab">Gradle</a></li>
    </ul>
      <div class="tab-content">
        <div role="tabpanel" class="tab-pane active" id="mavenrun">
            <pre>
./mvnw spring-boot:run
            </pre>
        </div>

        <div role="tabpanel" class="tab-pane" id="gradlerun">
            <pre>
./gradlew bootRun
            </pre>
        </div>
      </div>
     You see the following printed on your console:
     <br/>The following example shows what you see if you're using the Freemium instance:
      ```
        To share this account, make sure the following is added to your configuration:

         management.wavefront.api-token=44444-34this-45is-123a-sampletoken
         management.wavefront.uri=https://wavefront.surf

        Connect to your Wavefront dashboard using this one-time use link:
        https://wavefront.surf/us/example
      ```
1. Add data to your application before you start to view the data in Wavefront.
    {% include tip.html content="Try out the [Wavefront for Spring Boot 3 Tutorial](wavefront_springboot3_tutorial.html)."%}
1. Click the link (for example, `https://wavefront.surf/us/<name>`) and you are taken to the Wavefront Spring Boot Inventory dashboard where you can examine the data sent by your application.
    <br/>Example:
    ![Spring Boot metrics dashbaord](images/springboot_metrics.png)
    If your application uses trace data, click the link in the **Tracing** section of the dashboard to be directed to the Wavefront Traces Browser.
    <br/>Example:
    ![Spring Boot traces browser](images/springboot_span_logs_pet_clinic.png)
    {% include note.html content="Want to see the cool information you can gather from the Traces Browser? See [Explore the Traces Browser](tracing_traces_browser.html)." %}
<!---
<iframe width="768" height="432" src="https://www.youtube.com/embed/K-cviV9mKKA" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
--->


### Custom Configurations

Add the following custom configurations to the `application.properties` file.

<p><span style="font-size: large; font-weight: 500">Invite Users</span></p>

You can invite users and let them send data to the same cluster:

1. Click the link that was printed on your console and navigate to the Wavefront Service Dashboard:
    {% include note.html content="Save the link that you used to access the Service Dashboard." %}
    1. Click the gear icon and select **Accounts**.
    1. Click **Invite New Users** and specify a comma-separated list of email addresses.<br/>
        ![Invite Users](/images/spring_boot_invite_users.png)
  The users get an email with a link to reset their password. They can then access your dashboard.
1. Information about the token and URL are displayed on your terminal. Add them to your project’s `application.properties` file.
    ```
    management.wavefront.api-token=<Enter_Token>
    management.wavefront.uri=https://wavefront.surf
    ```
1. If you are using the freemium account and want the single-use login URL to show on the terminal each time you start the application, add `wavefront.freemium-account` to the `application.properties` file and set it to `true`.
  ```
  wavefront.freemium-account=true
  ```
    {% include tip.html content="If you don’t want Wavefront to auto-negotiation a freemium account for you, set the value to `false`."%}
1. Restart your application.

<a name="proxy"></a>
<p><span style="font-size: large; font-weight: 500">Use the Wavefront Proxy</span></p>

The [Wavefront proxy](proxies.html) ingests data and forwards it to the Wavefront service in a secure, fast, and reliable manner. It prevents data loss, simplifies firewall configuration, and allows you to filter or enrich data before it arrives at the Wavefront service.
{% include note.html content="Supported with Wavefront Proxy version 7.0 and later. Before sending data via the proxy, you must [Install and Manage Wavefront Proxies](proxies_installing.html)."%}

Copy and paste the following property to the `application.properties` file.
```
management.wavefront.uri=proxy://<Proxy_Host>:2878
```

<p><span style="font-size: large; font-weight: 500">Specify Application and Service Names</span></p>

If you have more than one Spring Boot application, you can specify the names of the application and the service in the `application.properties` file.

{{site.data.alerts.important}}
<p>The valid characters in an application and service name are: a-z, A-Z, 0-9, hyphen ("-"), underscore ("_"), dot ("."), forward slash ("/") and comma (","). </p>
<p>If your application or service names have any other characters other than the valid characters, the Wavefront service replaces each of those characters with a hyphen ("-").</p>
{{site.data.alerts.end}}

<br/>Example:
```
management.wavefront.application.name=my-application
management.wavefront.application.service-name=my-service
```

Example: If you are using a YAML file.
```
management:
  wavefront:
    application:
      name: my-application
      service-name: my-service
```

Optionally:
* If you configured `spring.application.name` in your application, it is automatically used as the service name.

You can configure the cluster and shard names under the `management.wavefront.application` namespace as well. This information is used to tag metrics and traces.
* If you want to take full control over [`ApplicationTags`](trace_data_details.html#application-tags), you can create a `@Bean`.
* If you want to customize the instance that is auto-configured, add an `ApplicationTagsBuilderCustomizer` bean.


## Wavefront Spring Boot Integration

If you already have a Wavefront account, you can start the setup and examine the dashboards from the Wavefront Spring Boot integration. This is supported for [Wavefront trial accounts](https://tanzu.vmware.com/observability).

1. Log in to your Wavefront instance.
2. Click **Integrations** on the toolbar, search for Spring Boot, and click the Spring Boot integration.
3. Use the information displayed on the **Setup** tab to set up the integration.
4. When setup is complete, click the **Dashboard** tab to examine your data.

## Next Steps

* [Try out the Wavefront for Spring Boot 3 Tutorial](wavefront_springboot3_tutorial.html) and see how you can send your data to Wavefront in a few simple steps!
* See the [Wavefront for Spring Boot FAQs](wavefront_spring_boot_faq.html).
