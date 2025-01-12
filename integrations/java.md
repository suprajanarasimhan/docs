---
title: Java Integration
tags: [integrations list]
permalink: java.html
summary: Learn about the Wavefront Java Integration.
---
# Java Integration

The Wavefront Java integration allows you to send Java application metrics to Wavefront. You can collect metrics in two ways.

* Java SDKs
* Java using Telegraf Jolokia2 Agent Plugin

### Java SDKs

Wavefront provides several Java SDKs for different purposes on GitHub:

- **[wavefront-sdk-java](https://github.com/wavefrontHQ/wavefront-sdk-java)**: Core SDK for sending different telemetry data to Wavefront. Data include metrics, delta counters, distributions, and spans.
- **[wavefront-dropwizard-metrics-sdk-java](https://github.com/wavefrontHQ/wavefront-dropwizard-metrics-sdk-java)**: Provides reporters and constructs, such as counters, meters, and histograms to periodically report application metrics and distributions to Wavefront.
- **[wavefront-dropwizard-sdk-java](https://github.com/wavefrontHQ/wavefront-dropwizard-sdk-java)**: Provides out-of-the-box metrics for your Dropwizard application and periodically reports that data to Wavefront.
- **[wavefront-runtime-sdk-jvm](https://github.com/wavefrontHQ/wavefront-runtime-sdk-jvm)**: Wavefront JVM SDK. Provides out-of-the-box metrics for the Java Virtual Machine (JVM) that runs your Java application.
- **[wavefront-opentracing-sdk-java](https://github.com/wavefrontHQ/wavefront-opentracing-sdk-java)**: Wavefront OpenTracing Java SDK. See [our tracing documentation](https://docs.wavefront.com/tracing_basics.html) for background.
- **[wavefront-grpc-sdk-java](https://github.com/wavefrontHQ/wavefront-grpc-sdk-java)**: Wavefront gRPC Java SDK. Provides out-of-the-box metrics, histograms, and trace data from gRPC operations in your Java application and reports that data to Wavefront.
- **[wavefront-jaxrs-sdk-java](https://github.com/wavefrontHQ/wavefront-jaxrs-sdk-java)**: Wavefront JAX-RS Java SDK. Provides out-of-the-box trace data from JAX-RS based clients in your Java application and reports that data to Wavefront.
- **[wavefront-jersey-sdk-java](https://github.com/wavefrontHQ/wavefront-jersey-sdk-java)**: Wavefront Jersey Java SDK. Provides out-of-the-box metrics, histograms, and, optionally, traces from your Jersey-based Java application, and reports that data to Wavefront.

On the **Setup** tab, the integration includes sample code based on `wavefront-dropwizard-metrics-sdk-java` for sending metrics to a [Wavefront proxy](https://docs.wavefront.com/proxies.html) or using [direct ingestion](https://docs.wavefront.com/direct_ingestion.html).

### Java using Telegraf Jolokia2 Agent Plugin

You can use the Jolokia2 Agent Telegraf Input Plugin to collect and send JVM metrics to Wavefront which does not require any code changes.

### Dashboards

In addition to setting up the metrics flow, this integration also installs dashboards:

* Java 
* Java on Kubernetes
* Java Using Telegraf

Here's a screenshot of the Java on Kubernetes dashboard with metrics collected from JVM by `wavefront-dropwizard-metrics-sdk-java`.

{% include image.md src="images/dropwizard-dashboard.png" width="80" %}


To see a list of the metrics for this integration, select the integration from <https://github.com/influxdata/telegraf/tree/master/plugins/inputs>.
# Java Setup



* [Java SDK](#java-sdk)
* [Java using Telegraf Jolokia2 Agent Plugin](#java-telegraf)


## <a name="java-sdk"></a> Java SDK

The Wavefront plugin for [Dropwizard Metrics](https://metrics.dropwizard.io) adds [Wavefront reporters](https://github.com/wavefrontHQ/wavefront-dropwizard-metrics-sdk-java) and an abstraction that supports tagging at the reporter level. The reporters support sending metrics to Wavefront using the [Wavefront proxy](https://docs.wavefront.com/proxies.html) or using [direct ingestion](https://docs.wavefront.com/direct_ingestion.html).

### Set up Maven

Add the `wavefront-dropwizard-metrics-sdk-java` library as a dependency in your project:
{% raw %}
```
<dependencies>
    <dependency>
        <groupId>com.wavefront</groupId>
        <artifactId>wavefront-dropwizard-metrics-sdk-java</artifactId>
        <version>$releaseVersion</version>
    </dependency>
</dependencies>
```
{% endraw %}

### Option 1. Create a Wavefront Proxy Reporter and Register Metrics

Follow these steps for sending metrics to a Wavefront proxy. See Option 2 for sending metrics directly to a Wavefront service.

#### Step 1. Set up Wavefront Proxy

If you do not have a [Wavefront proxy](https://docs.wavefront.com/proxies.html) installed on your network and reachable from your Java application, install a proxy. You configure the Wavefront proxy hostname and port (by default 2878) when you create the reporter.

#### Step 2. Initialize the WavefrontClient and send data via Wavefront Proxy
To create a reporter which will emit data to a Wavefront proxy every 5 seconds:{% raw %}
```
import com.codahale.metrics.Counter;
import com.codahale.metrics.MetricRegistry;
import com.wavefront.dropwizard.metrics.DropwizardMetricsReporter;
import com.wavefront.sdk.common.clients.WavefrontClientFactory;
import com.wavefront.sdk.common.WavefrontSender;
import java.util.concurrent.TimeUnit;
import java.net.InetAddress;
import java.net.UnknownHostException;

MetricRegistry metricRegistry = new MetricRegistry();
Counter evictions = metricRegistry.counter("cache-evictions");
evictions.inc();

WavefrontClientFactory wavefrontClientFactory = new WavefrontClientFactory();
// Add a client with the following URL format: "proxy://<your.proxy.fqdn>:<somePort>"
// to send data to proxies
wavefrontClientFactory.addClient(wavefrontURL);

WavefrontSender wavefrontSender = wavefrontClientFactory.getClient();

try {
    DropwizardMetricsReporter reporter = DropwizardMetricsReporter.forRegistry(metricRegistry).
        withSource(InetAddress.getLocalHost().getHostName()).
        prefixedWith("service"). // Prefix is optional
        withReporterPointTag("dc", "us-west-2").
        withReporterPointTag("env", "staging"). // Mandatory fields for K8s
        withReporterPointTag("service", "service-name"). // Mandatory fields for K8s
        build(wavefrontSender);

    reporter.start(5, TimeUnit.SECONDS);
} catch (UnknownHostException e) {
    e.printStackTrace();
}
```
{% endraw %}

### Option 2. Create a Wavefront Direct Reporter and Register Metrics
To create a reporter which will emit data to a Wavefront service every 5 seconds:{% raw %}
```
import com.codahale.metrics.Counter;
import com.codahale.metrics.MetricRegistry;
import com.wavefront.dropwizard.metrics.DropwizardMetricsReporter;
import com.wavefront.sdk.common.clients.WavefrontClientFactory;
import com.wavefront.sdk.common.WavefrontSender;
import java.util.concurrent.TimeUnit;
import java.net.InetAddress;
import java.net.UnknownHostException;

MetricRegistry metricRegistry = new MetricRegistry();
Counter evictions = metricRegistry.counter("cache-evictions");
evictions.inc();

WavefrontClientFactory wavefrontClientFactory = new WavefrontClientFactory();

// Create a factory and add a client with the following URL format: "https://TOKEN@DOMAIN.wavefront.com"
// and a Wavefront API token with direct ingestion permission
wavefrontClientFactory.addClient(wavefrontURL,
  20_000,           // This is the max batch of data sent per flush interval
  100_000,          // This is the size of internal buffer beyond which data is dropped
  2,                // Together with the batch size controls the max theoretical throughput of the sender
  Integer.MAX_VALUE // The maximum message size in bytes we will push with on each flush interval
);

WavefrontSender wavefrontSender = wavefrontClientFactory.getClient();

try {
    DropwizardMetricsReporter reporter = DropwizardMetricsReporter.forRegistry(metricRegistry).
        withSource(InetAddress.getLocalHost().getHostName()).
        prefixedWith("service"). // Prefix is optional
        withReporterPointTag("dc", "us-west-2").
        withReporterPointTag("env", "staging"). // Mandatory fields for K8s
        withReporterPointTag("service", "service-name"). // Mandatory fields for K8s
        build(wavefrontSender);

    reporter.start(5, TimeUnit.SECONDS);
} catch (UnknownHostException e) {
    e.printStackTrace();
}
```
{% endraw %}

### JVM Metrics

To add default JVM metrics to the `MetricsRegistry`, call `.withJvmMetrics()` when you create the reporter. If you call `.withJvmMetrics()`, the following metrics are added to the registry:
{% raw %}
```
metricRegistry.register("jvm.uptime",
    (Gauge<Long>) () -> ManagementFactory.getRuntimeMXBean().getUptime());
metricRegistry.register("jvm.current_time", (Gauge<Long>) clock::getTime);

metricRegistry.register("jvm.buffers",
    new BufferPoolMetricSet(ManagementFactory.getPlatformMBeanServer()));
metricRegistry.register("jvm.classes", new ClassLoadingGaugeSet());
metricRegistry.register("jvm.fd_usage", new FileDescriptorRatioGauge());
metricRegistry.register("jvm.gc", new GarbageCollectorMetricSet());
metricRegistry.register("jvm.memory", new MemoryUsageGaugeSet());
metricRegistry.register("jvm.thread-states", new ThreadStatesGaugeSet());
```
{% endraw %}

## <a name="java-telegraf"></a> Java using Telegraf Jolokia2 Agent Plugin

### Step 1. Install the Telegraf Agent

This integration uses the Jolokia2 Agent input plugin for Telegraf to extract metrics from Jenkins. If you've already installed Telegraf on one of your servers, you can skip to Step 2.

Log in to your Wavefront instance and follow the instructions in the **Setup** tab to install Telegraf and a Wavefront proxy in your environment. If a proxy is already running in your environment, you can select that proxy and the Telegraf install command connects with that proxy. Sign up for a [free trial](https://tanzu.vmware.com/observability-trial){:target="_blank" rel="noopenner noreferrer"} to check it out!

### Step 2. Download the Jolokia JVM-Agent Library

Download the latest version of the Jolokia JVM-Agent JAR file `jolokia-jvm-x.x.x.jar` from the [Jolokia web site](https://jolokia.org/download.html).

### Step 3. Run a Java Application Using the Jolokia Agent

1. To start your application, run the following command.

   `java -javaagent:<PATH_TO_JOLOKIA_JVM>/jolokia-jvm-1.7.1.jar=port=<JOLOKIA_PORT>,host=<HOSTNAME> -jar <APPLICATION_NAME>`

   Here `jolokia-jvm-1.7.1.jar` is the latest version used for testing.


2. Verify that you can access the URL:
{% raw %}
   ```
   curl http://<HOSTNAME>:<JOLOKIA_PORT>/jolokia/
   ```
{% endraw %}

### Step 4. Configure the Jolokia2 Agent Telegraf Input Plugin

You can find a sample configuration of the Jolokia2 Agent input plugin for Java in the [Jolokia2 Agent Plugin GitHub repository](https://github.com/influxdata/telegraf/blob/master/plugins/inputs/jolokia2_agent/examples/java.conf).

Create a file called `java.conf` in the `/etc/telegraf/telegraf.d` directory and enter the following snippet:
{% raw %}
```
[[inputs.jolokia2_agent]]
#   # default_tag_prefix      = ""
#   # default_field_prefix    = ""
#   # default_field_separator = "."
#
#   # Add agents URLs to query
    urls = ["http://HOSTNAME:8778/jolokia"]

#   # username = ""
#   # password = ""
#   # response_timeout = "5s"
#
#   ## Optional TLS config
#   # tls_ca   = "/var/private/ca.pem"
#   # tls_cert = "/var/private/client.pem"
#   # tls_key  = "/var/private/client-key.pem"
#   # insecure_skip_verify = false
#
#   ## Add metrics to read
  [[inputs.jolokia2_agent.metric]]
    name  = "java_runtime"
    mbean = "java.lang:type=Runtime"
    paths = ["Uptime"]

  [[inputs.jolokia2_agent.metric]]
    name  = "java_memory"
    mbean = "java.lang:type=Memory"
    paths = ["HeapMemoryUsage", "NonHeapMemoryUsage", "ObjectPendingFinalizationCount"]

  [[inputs.jolokia2_agent.metric]]
    name     = "java_garbage_collector"
    mbean    = "java.lang:name=*,type=GarbageCollector"
    paths    = ["CollectionTime", "CollectionCount"]
    tag_keys = ["name"]
    tag_prefix = "gc_"

  [[inputs.jolokia2_agent.metric]]
    name  = "java_last_garbage_collection"
    mbean = "java.lang:name=*,type=GarbageCollector"
    paths = ["LastGcInfo"]
    tag_keys = ["name"]

  [[inputs.jolokia2_agent.metric]]
    name  = "java_threading"
    mbean = "java.lang:type=Threading"
    paths = ["TotalStartedThreadCount", "ThreadCount", "DaemonThreadCount", "PeakThreadCount"]

  [[inputs.jolokia2_agent.metric]]
    name  = "java_class_loading"
    mbean = "java.lang:type=ClassLoading"
    paths = ["LoadedClassCount", "UnloadedClassCount", "TotalLoadedClassCount"]

  [[inputs.jolokia2_agent.metric]]
    name     = "java_memory_pool"
    mbean    = "java.lang:name=*,type=MemoryPool"
    paths    = ["Usage", "PeakUsage", "CollectionUsage"]
    tag_keys = ["name"]
    tag_prefix = "pool_"

  [[inputs.jolokia2_agent.metric]]
    name     = "java_operating_system"
    mbean    = "java.lang:type=OperatingSystem"
    paths    = ["ProcessCpuLoad", "SystemLoadAverage", "SystemCpuLoad"]
    tag_keys = ["name"]

  [[inputs.jolokia2_agent.metric]]
    name     = "java_operating_system"
    mbean    = "java.lang:type=OperatingSystem"
    paths    = ["OpenFileDescriptorCount", "MaxFileDescriptorCount", "ProcessCpuTime", "ProcessCpuLoad", "AvailableProcessors", "SystemLoadAverage", "SystemCpuLoad", "FreePhysicalMemorySize", "TotalPhysicalMemorySize", "FreeSwapSpaceSize", "TotalSwapSpaceSize", "CommittedVirtualMemorySize"]
    tag_keys = ["name"]
```
{% endraw %}

For details, see [Jolokia2 Agent Plugin Configuration](https://github.com/influxdata/telegraf/tree/master/plugins/inputs/jolokia2_agent#configuration) and [Jolokia Metric Configuration](https://github.com/influxdata/telegraf/tree/master/plugins/inputs/jolokia2_agent#metric-configuration).

In addition, you can configure several MBeans in the Telegraf configuration. For example:{% raw %}
```
  [[inputs.jolokia2_agent.metric]]
    name     = "java_compilation"
    mbean    = "java.lang:type=Compilation"
    paths    = ["TotalCompilationTime"]
    tag_keys = ["name"]
```
{% endraw %}
For details on MBeans and their paths, see the [Jolokia documentation](https://jolokia.org/reference/html/protocol.html#jolokia-operations).

### Step 5. Restart Telegraf

Run `sudo service telegraf restart` to restart your Telegraf agent.


## Metrics


|Metric Name|Description|
| :--- | :--- |
|jvm.buffer-pool.direct.*|Metrics for direct (not mapped) JVM buffer pools.|
|jvm.buffer-pool.direct.count|Direct JVM buffer pools count.|
|jvm.buffer-pool.direct.capacity|Direct JVM buffer pools capacity.|
|jvm.buffer-pool.direct.used|Used direct JVM buffer pools.|
|jvm.buffer-pool.mapped.*|Metrics for mapped (not direct) JVM buffer pools.|
|jvm.buffer-pool.mapped.count|Mapped JVM buffer pools count.|
|jvm.buffer-pool.mapped.capacity|Mapped JVM buffer pools capacity.|
|jvm.buffer-pool.mapped.used|Used mapped JVM buffer pools.|
|jvm.buffer.*|JVM buffer metrics.|
|jvm.buffer.count|JVM buffer pools count.|
|jvm.buffer.count.buffers|Number of buffers in the JVM pool.|
|jvm.buffer.count.buffers.gauge|An estimate of the number of buffers in the JVM pool.|
|jvm.buffer.memory.*|JVM buffer memory metrics.|
|jvm.buffer.memory.used|The memory that the Java virtual machine is using for this buffer pool.|
|jvm.buffer.memory.used.bytes|The memory that the Java virtual machine is using for this buffer pool in bytes.|
|jvm.buffer.memory.used.bytes.gauge|An estimate of the memory that the Java virtual machine is using for this buffer pool.|
|jvm.buffer.total.capacity.*|JVM total buffer capacity metrics.|
|jvm.buffer.total.capacity|JVM total buffer capacity.|
|jvm.buffer.total.capacity.bytes|JVM total buffer capacity in bytes.|
|jvm.buffer.total.capacity.bytes.gauge|An estimate of the total capacity of the buffers in this pool.|
|jvm.buffers.direct.*|Metrics for direct (not mapped) JVM buffers.|
|jvm.buffers.direct.count|Number of direct JVM buffers in the pool.|
|jvm.buffers.direct.capacity|An estimate of the total capacity of the direct JVM buffers in this pool.|
|jvm.buffers.direct.used|Number of direct JVM buffers used in the pool.|
|jvm.buffers.mapped.count|Metrics for mapped (not direct) JVM buffers.|
|jvm.buffers.mapped.capacity|An estimate of the total capacity of the mapped JVM buffers in this pool|
|jvm.buffers.mapped.used|Number of Mapped JVM buffers used in the pool.|
|jvm.classes.loaded.*|Metrics for loaded JVM classes|
|jvm.classes.loaded|The number of classes that are currently loaded in the JVM.|
|jvm.classes.loaded.classes|The number of classes that are currently loaded in the JVM.|
|jvm.classes.loaded.classes.gauge|The estimated number of classes that are currently loaded in the JVM.|
|jvm.classes.unloaded.*|Metrics for unloaded JVM classes.|
|jvm.classes.unloaded|Number of unloaded JVM classes.|
|jvm.classes.unloaded.classes.total|Total number of unloaded JVM classes.|
|jvm.classes.unloaded.classes.total.counter|Total number of unloaded classes since the JVM has started execution.|
|jvm.current_time|Current time. |
|jvm.gc.G1-*-Generation. *|JVM garbage collection metrics (old or young generation)|
|jvm.gc.G1-Old-Generation.count|Old generation G1 garbage collections count.|
|jvm.gc.G1-Old-Generation.time|Time of old generation G1 garbage collections.|
|jvm.gc.G1-Young-Generation.count|Young generation G1 garbage collections count.|
|jvm.gc.G1-Young-Generation.time|Time of young generation G1 garbage collections.|
|jvm.gc.PS-*. *|Metrics for the PS MarkSweep and PS Scavenge Java beans. |
|jvm.gc.PS-MarkSweep.count|PS MarkSweep garbage collections count.|
|jvm.gc.PS-MarkSweep.time|PS MarkSweep garbage collections time.|
|jvm.gc.PS-Scavenge.count|PS Scavenge garbage collections count.|
|jvm.gc.PS-Scavenge.time|PS Scavenge garbage collections time.|
|jvm.gc.concurrent.phase.time.*|Metrics for JVM concurrent garbage collection (Mark Sweep collector).|
|jvm.gc.concurrent.phase.time.count|JVM concurrent garbage collection time count.|
|jvm.gc.concurrent.phase.time.max|JVM concurrent garbage collection maximum time.|
|jvm.gc.concurrent.phase.time.sum|Sum of the JVM concurrent garbage collection time.|
|jvm.gc.concurrent.phase.time.avg|Average time spent in JVM concurrent garbage collection.|
|jvm.gc.live.data.*|Metrics for JVM garbage collection live data.|
|jvm.gc.live.data.size|The live data size is the size (in bytes) of the old generation after a major garbage collection.|
|jvm.gc.live.data.size.bytes|The live data size is the size (in bytes) of the old generation after a major garbage collection.|
|jvm.gc.live.data.size.bytes.gauge|An estimate of the live data size of the old generation after a major garbage collection.|
|jvm.gc.max.data.*|Metrics for JVM garbage collection max data size.|
|jvm.gc.max.data.size|The maximum size of long-lived heap memory pool for the old generation (in bytes).|
|jvm.gc.max.data.size.bytes|The maximum size of long-lived heap memory pool for the old generation (in bytes).|
|jvm.gc.max.data.size.bytes.gauge|An estimate of the maximum size of long-lived heap memory pool for the old generation (in bytes).|
|jvm.gc.memory.allocated|Increase in the size of the young heap memory pool after one garbage collection and before the next.|
|jvm.gc.memory.allocated.bytes.total|Total number of bytes of increase in the size of the young heap memory pool after one garbage collection and before the next.|
|jvm.gc.memory.allocated.bytes.total.counter|Increase in the size of the young heap memory pool after one garbage collection and before the next (total count number).|
|jvm.gc.memory.*|Metrics for JVM garbage collection memory.|
|jvm.gc.memory.promoted|Count of positive increases in the size of the old generation memory pool from before garbage collection to after garbage collection.|
|jvm.gc.memory.promoted.bytes.total|Count of positive increases in the size of the old generation memory pool from before garbage collection to after garbage collection (total number of bytes).|
|jvm.gc.memory.promoted.bytes.total.counter|Count of positive increases in the size of the old generation memory pool from before garbage collection to after garbage collection (total count of number of bytes).|
|jvm.gc.pause.*|Pause-related metrics for JVM garbage collection.|
|jvm.gc.pause.count|Count of JVM garbage collection pauses.|
|jvm.gc.pause.max|Maximum number of JVM garbage collection pauses.|
|jvm.gc.pause.count|Count of JVM garbage collection pauses.|
|jvm.gc.pause.avg|Average JVM garbage collection pauses.|
|jvm.gc.pause.seconds.count|Time spent in JVM garbage collection pause.|
|jvm.gc.pause.seconds.max|Maximum time spent in JVM garbage collection pauses.|
|jvm.gc.pause.seconds.sum|Sum of the duration spent in JVM garbage collection pauses.|
|jvm.gc.pause.seconds.max.gauge|An estimate of the maximum time spent in JVM garbage collection pauses.|
|jvm.memory.max*|Maximum JVM memory metrics.|
|jvm.memory.max|The maximum JVM memory available.|
|jvm.memory.max.bytes|The maximum amount of memory that can be used for memory management.|
|jvm.memory.max.bytes.gauge|The maximum JVM memory space.|
|jvm.memory.committed*|Metrics for JVM committed memory.|
|jvm.memory.committed|JVM committed memory.|
|jvm.memory.committed.bytes|JVM committed memory in bytes.|
|jvm.memory.committed.bytes.gauge|An estimate of the JVM committed memory.|
|jvm.memory.heap.*|JVM memory heap metrics.|
|jvm.memory.heap.max|Maximum JVM memory heap.|
|jvm.memory.heap.committed|Committed JVM memory heap.|
|jvm.memory.heap.init|Initial amount of heap memory (in bytes) that the JVM requests.|
|jvm.memory.heap.usage|Amount of JVM memory heap.|
|jvm.memory.heap.used|The amount of used JVM memory heap.|
|jvm.memory.non-heap.*|JVM memory non-heap metrics.|
|jvm.memory.non-heap.max|The maximum memory the JVM allocated for purposes other than the heap.|
|jvm.memory.non-heap.committed|The committed memory the JVM allocated for purposes other than the heap.|
|jvm.memory.non-heap.init|Initial amount of non-heap memory.|
|jvm.memory.non-heap.usage|Amount of JVM non-heap memory.|
|jvm.memory.non-heap.used|The amount of used JVM non-heap memory.|
|jvm.memory.pools.Code-Cache.*|Metrics for the code cache memory pool.|
|jvm.memory.pools.Code-Cache.max|Maximum size of the code cache memory pool.|
|jvm.memory.pools.Code-Cache.committed|Committed size of the code cache memory pool.|
|jvm.memory.pools.Code-Cache.init|Initial size of the code cache memory pool.|
|jvm.memory.pools.Code-Cache.usage|Size of the code cache memory pool.|
|jvm.memory.pools.Code-Cache.used|Used size of the code cache memory pool.|
|jvm.memory.pools.Compressed-Class-Space.*|Metrics for the compressed class space memory pool.|
|jvm.memory.pools.Compressed-Class-Space.max|Maximum size of the compressed class space memory pool.|
|jvm.memory.pools.Compressed-Class-Space.committed|Committed size of the compressed class space memory pool.|
|jvm.memory.pools.Compressed-Class-Space.init|Initial size of the compressed class space memory pool.|
|jvm.memory.pools.Compressed-Class-Space.usage|Size of the compressed class space memory pool.|
|jvm.memory.pools.Compressed-Class-Space.used|Used size of the compressed class space memory pool.|
|jvm.memory.pools.G1-Eden-Space.*|Metrics for the G1-Eden-Space memory pool.|
|jvm.memory.pools.G1-Eden-Space.max|Maximum size of the G1-Eden-Space memory pool.|
|jvm.memory.pools.G1-Eden-Space.committed|Committed size of the G1-Eden-Space memory pool.|
|jvm.memory.pools.G1-Eden-Space.init|Initial size of the G1-Eden-Space memory pool.|
|jvm.memory.pools.G1-Eden-Space.usage|Size of the G1-Eden-Space memory pool.|
|jvm.memory.pools.G1-Eden-Space.used|Used size of the G1-Eden-Space memory pool.|
|jvm.memory.pools.G1-Eden-Space.used-after-gc|Used size of the G1-Eden-Space memory pool after garbage collection.|
|jvm.memory.pools.G1-Old-Gen.*|Metrics for the G1-Old-Gen memory pool.|
|jvm.memory.pools.G1-Old-Gen.max|Maximum size of the G1-Old-Gen memory pool.|
|jvm.memory.pools.G1-Old-Gen.committed|Committed size of the G1-Old-Gen memory pool.|
|jvm.memory.pools.G1-Old-Gen.init|Initial size of the G1-Old-Gen memory pool.|
|jvm.memory.pools.G1-Old-Gen.usage|Size of the G1-Old-Gen memory pool.|
|jvm.memory.pools.G1-Old-Gen.used|Used size of the G1-Old-Gen memory pool.|
|jvm.memory.pools.G1-Old-Gen.used-after-gc| G1-Old-Gen memory pool after garbage collection.|
|jvm.memory.pools.G1-Survivor-Space.*|Metrics for the G1-Survivor-Space memory pool.|
|jvm.memory.pools.G1-Survivor-Space.max|Maximum size of the G1-Survivor-Space memory pool.|
|jvm.memory.pools.G1-Survivor-Space.committed|Committed size of the G1-Survivor-Space memory pool.|
|jvm.memory.pools.G1-Survivor-Space.init|Initial size of the G1-Survivor-Space memory pool.|
|jvm.memory.pools.G1-Survivor-Space.usage|Size of the G1-Survivor-Space memory pool.|
|jvm.memory.pools.G1-Survivor-Space.used|Used size of the G1-Survivor-Space memory pool.|
|jvm.memory.pools.G1-Survivor-Space.used-after-gc|Used size of the G1-Survivor-Space memory pool after garbage collection.|
|jvm.memory.pools.Metaspace.*|Metrics for the JVM metaspace memory pool.|
|jvm.memory.pools.Metaspace.max|Maximum size of the JVM metaspace memory pool.|
|jvm.memory.pools.Metaspace.committed|Committed size of the JVM metaspace memory pool.|
|jvm.memory.pools.Metaspace.init|Initial size of the JVM metaspace memory pool.|
|jvm.memory.pools.Metaspace.usage|Size of the JVM metaspace memory pool.|
|jvm.memory.pools.Metaspace.used|Used size of the JVM metaspace memory pool.|
|jvm.memory.pools.PS-Eden-Space.*|Metrics for Metrics for the JVM PS-Eden-Space memory pool.|
|jvm.memory.pools.PS-Eden-Space.max|Maximum size of the JVM PS-Eden-Space memory pool.|
|jvm.memory.pools.PS-Eden-Space.committed|Committed size of the JVM PS-Eden-Space memory pool.|
|jvm.memory.pools.PS-Eden-Space.init|Initial size of the JVM PS-Eden-Space memory pool.|
|jvm.memory.pools.PS-Eden-Space.usage|Size of the JVM PS-Eden-Space memory pool.|
|jvm.memory.pools.PS-Eden-Space.used|Used size of the JVM PS-Eden-Space memory pool.|
|jvm.memory.pools.PS-Eden-Space.used-after-gc|Used size of the JVM PS-Eden-Space memory pool after garbage collection.|
|jvm.memory.pools.PS-Old-Gen.max|Metrics for the JVM PS-Old-Gen memory pool.|
|jvm.memory.pools.PS-Old-Gen.max|Maximum size of the JVM PS-Old-Gen memory pool.|
|jvm.memory.pools.PS-Old-Gen.committed|Committed size of the JVM PS-Old-Gen memory pool.|
|jvm.memory.pools.PS-Old-Gen.init|Initial size of the JVM PS-Old-Gen memory pool.|
|jvm.memory.pools.PS-Old-Gen.usage|Size of the JVM PS-Old-Gen memory pool.|
|jvm.memory.pools.PS-Old-Gen.used|Used size of the JVM PS-Old-Gen memory pool.|
|jvm.memory.pools.PS-Old-Gen.used-after-gc|Used size of the JVM PS-Old-Gen memory pool after garbage collection.|
|jvm.memory.pools.PS-Survivor-Space.*|Metrics for the JVM PS-Survivor-Space memory pool.|
|jvm.memory.pools.PS-Survivor-Space.max|Maximum size of the JVM PS-Survivor-Space memory pool.|
|jvm.memory.pools.PS-Survivor-Space.committed|Committed size of the JVM PS-Survivor-Space memory pool.|
|jvm.memory.pools.PS-Survivor-Space.init|Initial size of the JVM PS-Survivor-Space memory pool.|
|jvm.memory.pools.PS-Survivor-Space.usage|Size of the JVM PS-Survivor-Space memory pool.|
|jvm.memory.pools.PS-Survivor-Space.used|Used size of the JVM PS-Survivor-Space memory pool.|
|jvm.memory.pools.PS-Survivor-Space.used-after-gc|Used size of the JVM PS-Survivor-Space memory pool after garbage collection.|
|jvm.memory.total.*|Metrics for total JVM memory.|
|jvm.memory.total.max|Maximum total JVM memory.|
|jvm.memory.total.committed|Committed total JVM memory.|
|jvm.memory.total.init|Initial total JVM memory.|
|jvm.memory.total.used|Used total JVM memory.|
|jvm.memory.used|Metrics for used JVM memory.|
|jvm.memory.used|The amount of used memory.|
|jvm.memory.used.bytes|The amount of used memory in bytes.|
|jvm.memory.used.bytes.gauge|An estimate of the used memory.|
|jvm.thread-states.*|JVM thread state metrics.|
|jvm.thread-states.count|JVM thread state number.|
|jvm.thread-states.blocked.count|JVM blocked thread states number.|
|jvm.thread-states.daemon.count|JVM daemon thread states number.|
|jvm.thread-states.deadlock.count|JVM deadlock thread states number.|
|jvm.thread-states.new.count|JVM new thread states number.|
|jvm.thread-states.runnable.count|JVM runnable thread states number.|
|jvm.thread-states.terminated.count|JVM terminated thread states number.|
|jvm.thread-states.timed_waiting.count|JVM timed_waiting thread states number.|
|jvm.thread-states.waiting.count|JVM waiting thread states number.|
|jvm.threads.*|JVM thread metrics|
|jvm.threads.count|JVM threads number.|
|jvm.threads.blocked.count|JVM blocked threads number.|
|jvm.threads.daemon.count|JVM daemon threads number.|
|jvm.threads.daemon.threads|JVM daemon threads.|
|jvm.threads.daemon.threads.gauge|An estimate of the JVM daemon threads.|
|jvm.threads.deadlock.count|JVM deadlock threads number.|
|jvm.threads.live|JVM live threads number.|
|jvm.threads.live.threads|JVM live threads.|
|jvm.threads.live.threads.gauge|An estimate of the JVM live threads.|
|jvm.threads.new.count|JVM new threads number.|
|jvm.threads.peak|JVM peak threads.|
|jvm.threads.peak.threads|JVM peak threads number.|
|jvm.threads.peak.threads.gauge|An estimate of the JVM peak threads.|
|jvm.threads.runnable.count|JVM runnable threads number.|
|jvm.threads.states|JVM states threads.|
|jvm.threads.states.threads|JVM states threads number.|
|jvm.threads.states.threads.gauge|An estimate of the JVM states threads.|
|jvm.threads.terminated.count|JVM terminated threads number.|
|jvm.threads.timed_waiting.count|JVM timed_waiting threads number.|
|jvm.threads.waiting.count|JVM waiting threads number.|
|jvm.uptime|JVM uptime.|
|java.class.loading.LoadedClassCount|Count of loaded classes.|
|java.class.loading.TotalLoadedClassCount|Total number of loaded classes.|
|java.class.loading.UnloadedClassCount|Count of unloaded classes.|
|java.garbage.collector.CollectionCount|Collection count of Garbage Collector.|
|java.garbage.collector.CollectionTime|Collection time of Garbage Collector.|
|java.last.garbage.collection.LastGcInfo.LastGcInfo.GcThreadCount|Thread count of last garbage collection information.|
|java.last.garbage.collection.LastGcInfo.LastGcInfo.duration|Duration of the last garbage collection information.|
|java.last.garbage.collection.LastGcInfo.LastGcInfo.endTime|End time of last garbage collection information.|
|java.last.garbage.collection.LastGcInfo.LastGcInfo.id|ID of last garbage collection information.|
|java.last.garbage.collection.LastGcInfo.LastGcInfo.memoryUsageAfterGc.CodeHeap--non-nmethods-.*|Statistics: committed, init, max, used|
|java.last.garbage.collection.LastGcInfo.LastGcInfo.memoryUsageAfterGc.CodeHeap--non-profiled-nmethods-.*|Statistics: committed, init, max, used|
|java.last.garbage.collection.LastGcInfo.LastGcInfo.memoryUsageAfterGc.CodeHeap--profiled-nmethods-.*|Statistics: committed, init, max, used|
|java.last.garbage.collection.LastGcInfo.LastGcInfo.memoryUsageAfterGc.Compressed-Class-Space.*|Statistics: committed, init, max, used|
|java.last.garbage.collection.LastGcInfo.LastGcInfo.memoryUsageAfterGc.G1-Eden-Space.*|Statistics: committed, init, max, used|
|java.last.garbage.collection.LastGcInfo.LastGcInfo.memoryUsageAfterGc.G1-Old-Gen.*|Statistics: committed, init, max, used|
|java.last.garbage.collection.LastGcInfo.LastGcInfo.memoryUsageAfterGc.G1-Survivor-Space.*|Statistics: committed, init, max, used|
|java.last.garbage.collection.LastGcInfo.LastGcInfo.memoryUsageAfterGc.Metaspace.*|Statistics: committed, init, max, used|
|java.last.garbage.collection.LastGcInfo.LastGcInfo.memoryUsageBeforeGc.CodeHeap--non-nmethods-.*|Statistics: committed, init, max, used|
|java.last.garbage.collection.LastGcInfo.LastGcInfo.memoryUsageBeforeGc.CodeHeap--non-profiled-nmethods-.*|Statistics: committed, init, max, used|
|java.last.garbage.collection.LastGcInfo.LastGcInfo.memoryUsageBeforeGc.CodeHeap--profiled-nmethods-.*|Statistics: committed, init, max, used|
|java.last.garbage.collection.LastGcInfo.LastGcInfo.memoryUsageBeforeGc.Compressed-Class-Space.*|Statistics: committed, init, max, used|
|java.last.garbage.collection.LastGcInfo.LastGcInfo.memoryUsageBeforeGc.G1-Eden-Space.*|Statistics: committed, init, max, used|
|java.last.garbage.collection.LastGcInfo.LastGcInfo.memoryUsageBeforeGc.G1-Old-Gen.*|Statistics: committed, init, max, used|
|java.last.garbage.collection.LastGcInfo.LastGcInfo.memoryUsageBeforeGc.G1-Survivor-Space.*|Statistics: committed, init, max, used|
|java.last.garbage.collection.LastGcInfo.LastGcInfo.memoryUsageBeforeGc.Metaspace.*|Statistics: committed, init, max, used|
|java.last.garbage.collection.LastGcInfo.LastGcInfo.startTime|Start time of last garbage collection information.|
|java.memory.HeapMemoryUsage.*|Amount of JVM heap memory Usage.</br>Statistics: committed, init, max, used|
|java.memory.NonHeapMemoryUsage.*|Amount of JVM non-heap memory Usage.</br>Statistics: committed, init, max, used|
|java.memory.ObjectPendingFinalizationCount|Count of objects pending finalization.|
|java.memory.pool.CollectionUsage.*|Collection usage of memory pool.</br>Statistics: committed, init, max, used|
|java.memory.pool.PeakUsage.*|Peak usage of memory pool.</br>Statistics: committed, init, max, used|
|java.memory.pool.Usage.*|Usage of memory pool.</br>Statistics: committed, init, max, used|
|java.operating.system.CommittedVirtualMemorySize|Total amount of committed virtual memory size.|
|java.operating.system.FreePhysicalMemorySize|Total amount of free physical memory size.|
|java.operating.system.FreeSwapSpaceSize|Total amount of free swap space size.|
|java.operating.system.MaxFileDescriptorCount|Maximum count of file descriptors.|
|java.operating.system.OpenFileDescriptorCount|Count of open file descriptors.|
|java.operating.system.ProcessCpuLoad|Process CPU load.|
|java.operating.system.ProcessCpuTime|Process CPU time.|
|java.operating.system.SystemCpuLoad|System CPU load.|
|java.operating.system.SystemLoadAverage|System load average over a period of time.|
|java.operating.system.TotalPhysicalMemorySize|Total amount of physical memory size.|
|java.operating.system.TotalSwapSpaceSize|Total amount of swap space size.|
|java.operating.system.AvailableProcessors|Count of processors.|
|java.runtime.Uptime|JVM uptime.|
|java.threading.DaemonThreadCount|Count of daemon threads.|
|java.threading.PeakThreadCount|Count of peak threads.|
|java.threading.ThreadCount|Count of threads.|
|java.threading.TotalStartedThreadCount|Count of started threads.|

<h2>Alerts</h2>  <ul><li markdown="span"><b>Percentage of Heap Memory Used</b>:Alert is reporting when the heap memory usage is too high. If a prefix is configured in the application, update the metrics' prefix in the condition for the metrics.</li><li markdown="span"><b>Percentage of G1 Old Generation Memory Used</b>:Alert is reporting when the memory usage by G1 old generation is too high. If a prefix is configured in the application, update the metrics' prefix in the condition for the metrics.</li></ul>