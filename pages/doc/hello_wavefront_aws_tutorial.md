---
title: Hello Wavefront!
keywords:
tags: [tutorials]
sidebar: doc_sidebar
permalink: hello_wavefront_aws_tutorial.html
summary: Get data from a Windows host or Amazon Web Services.
---

In this tutorial, you'll learn how to:
1. Log in to Tanzu Observability by Wavefront.
2. Send data from applications and services that run on your AWS account.
3. Visualize data with preconfigured dashboards and charts.

It's an easy setup. You don't have to install anything or make changes to your application code.

{% include tip.html content="Don't have an AWS account? You can [send data from a Windows host](#video-set-up-data-ingestion-from-a-windows-host). Or you can explore dashboards with sample data. [Log in](#task-1-log-in-to-your-wavefront-instance), click **Integrations** and search for **Tutorial** or **Tour Pro**." %}

<table style="width: 100%;">
<tbody>
<tr>
<td width="50%" markdown="span">![screenshot of tutorial dashboards list](images/tutorial_screenshot.png)
</td>
<td width="50%" markdown="span">![screenshot tour pro dashboards list](images/tour_screenshot.png) </td></tr>
</tbody>
</table>


## Task 1: Log In to Your Wavefront Instance

If you've never logged in to your company's Wavefront instance, follow these steps. Go to Task 2 otherwise.

1. Check your email! When your administrator adds you to the Wavefront instance, you receive an email with the subject that includes `You have been invited by <email> to Wavefront!`.
2. Click the link in the email.
  * In some environments, you set up your password and you're done.
  * If your administrator has configured Single Sign-On (SSO), you're redirected to your company's SSO environment. When you complete the authentication steps, you're redirected to the Wavefront instance.
3. Going forward, you can log in using the `https://<my_instance>.wavefront.com` URL. The Wavefront instance name is in the invitation email.

{% include tip.html content="If your company isn't currently a Tanzu Observability customer, [sign up for a free trial](https://tanzu.vmware.com/observability)." %}

## Task 2: Set Up the Integration

In this task, we'll set up a data ingestion pipeline with AWS.

### Step 1: Start Integration Setup

1. Click **Integrations** on the toolbar.
   An integration tile usually has:
   * A **Setup** tab which provides step-by-step instructions on setting up the integration.
   * A **Dashboards** tab to access the out-of-the-box dashboards.
   Popular integrations also have an **Alerts** tab with preconfigured alerts.
2. Click the **Amazon Web Services (AWS)** tile.
    <!--![Highlight the AWS integration on the Wavefront Integrations page.](images/hello_tutorial_aws_integration_tile.png)-->
3. Click the **Setup** tab and click **Add Integration**.
    ![Highlights the Add Integration button on the AWS integration's Setup tab.](images/hello_tutorial_aws_add_integration.png)
4. Click the **How to get Role ARN** link.

You'll see **Account ID** and **External ID** under **How to get Role ARN**. You'll need them to set up the integration.

### Step 2: Create a Wavefront Read-Only Role in Your AWS Account

{{site.data.alerts.note}}
<p>For this step, you must log in to your AWS account. Create a new AWS account if you don’t have one.</p>
{{site.data.alerts.end}}

Follow these steps:

1. On a web browser tab, log in to your AWS account.
1. Search for the **IAM** (AWS Identity and Access Management) service and click it to open the service.
1. In the left panel, click **Roles**, and click **Create role**.
1. Create a trusted entity:
    1. Click the **AWS Account** tile and select the **Another AWS account** radio button.
    1. Enter the **Account ID**: Copy the **Account ID** value shown in the AWS integration setup instructions and paste it here.
        ![A diagram that shows where the account ID is on the Wavefront integration and an arrow pointing how to copy and paste on the AWS account.](images/hello_tutorial_aws_account_ID.png)
    1. Select the **Require external ID** check box.
        ![A screenshot of the external ID option selected.](images/hello_tutorial_aws_external_ID_selected.png)
    1. Enter the **External ID**. Copy the **External ID** value shown in the AWS integration setup instructions and paste it here.
        ![A diagram that shows where the external ID is on the Wavefront integration and an arrow pointing how to copy and paste on the AWS account.](images/hello_tutorial_aws_external_ID.png)
    1. Click **Next**.
1. Set Permissions:
    1. Search for the **ReadOnlyAccess** permission and select it.
        {% include note.html content="You get many results when you search for ReadOnlyAccess. Scroll down and browse through the pages until you find ReadOnlyAccess, as shown in the screenshot below."%}
        ![A screenshot that shows the ReadOnlyAccess permission selected.](images/hello_tutorial_readonly_permission.png)
    1. Click **Next**.

1. Set the **Role name** as `wavefront`.
1. Click **Create role**.
1. Once the list of roles appears, click `wavefront` (the role you just created), and copy the **ARN** value.

{% include note.html content="See [Giving Wavefront Limited Access](integrations_aws_overview.html#giving-limited-access) if you want to specify a more restrictive IAM policy for Wavefront." %}

### Step 3: Configure the AWS Integration

Go back to the Wavefront instance where you opened the AWS integration tile, and follow these steps:

<table style="width: 100%;">
<tbody>
<tr>
<td width="50%">
<ol><li>Paste the <strong>Role ARN</strong> value you copied in the previous step as the value for <strong>“Role ARN” from Amazon IAM</strong>. </li>
<li>Click <strong>Register</strong>. </li>
</ol>
</td>
<td width="50%" markdown="span">![Screenshot of the AWS integration's configure section. The Register button is highlighted in red.](images/hell_tutorial_configure_aws_integration.png) </td></tr>
</tbody>
</table>

Tanzu Observability by Wavefront can now connect to your AWS account and get data. Once the data starts flowing, you can visualize them. It will take a few minutes for the data to show.

### Step 4: (Optional) Launch an EC2 Instance

Don't have an application running on your AWS account? Follow the steps given below.
If you already have an application running on the AWS account, move to the next task and see how you can visualize your data.

1. Go back to your AWS account, search for the **EC2** service, and click it to open the service.
1. Follow the AWS documentation on [Launching an Amazon EC2 Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EC2_GetStarted.html#ec2-launch-instance).

<!--
1. Select **Launch Instance** and click **Launch Instance**.
    ![Screenshot showing the launch instance.](images/hello_tutorial_launch_instance.png)
1. Select **Free tier only** on the left panel and click **Select** on the image you want to run.
      {% include important.html content="You may still be charged for the use of some AWS products unless your infrastructure and service choices remain within the free usage tier. Therefore, make sure it is free to use." %}
1. Follow the steps to launch the instance.
1. You can select **proceed without a key pair** when prompted to select or create a new key pair.
      {% include important.html content="When you select **proceed without a key pair**, you are not able to SSH into the EC2 instance you deploy. Only use it for this tutorial, as it is not a recommended approach." %}
-->
Once the instance is launched, you'll see the data after a few minutes.

### Learn More About Data Ingestion and the AWS Integration

* [Set Up Data Ingestion](wavefront_data_ingestion.html) has information on data ingestion, including a video.
* [Amazon Web Services Integration](integrations_aws_overview.html) has more information on the AWS integration.
* [Set up and manage the AWS Integration by Using the API](integrations_aws_overview_API.html).
* See the [List of Tanzu Observability by Wavefront Integrations](label_integrations%20list.html).

## Task 3: Explore Data with Out-of-the-Box Dashboards

With data flowing, you can start exploring dashboards and charts:

<p><span style="font-size: large; font-weight: 500">View Metrics</span></p>
1. In your Wavefront instance, go to the AWS integration.
1. Click the **Metrics** tab.

You see charts with the metrics collected from your AWS account.

Example:
![Screenshot of the AWS metrics once the data starts to flow to Wavefront.](images/hello_tutorial_aws_metrics.png)
{% include note.html content="You see **No Data** if Tanzu Observability can't find any metrics to match the queries in the chart." %}

<br/>
<p><span style="font-size: large; font-weight: 500">View Data on Dashboards</span></p>
Tanzu Observability includes system dashboards for the AWS integration that help you analyze and gather data.
1. To see the list of the system dashboards, click the **Dashboards** tab.
    ![Screenshot of all the predefined dashboards available for Wavefront.](images/hello_tutorial_aws_dahsboards.png)
1. Click **AWS: Summary**. From the Summary dashboard, you can easily navigate to all other AWS dashboards.
    {% include note.html content="You need to configure your AWS account preferences to send billing metrics. See [Configuring CloudWatch Billing Metrics](integrations_aws_metrics.html#configuring-cloudwatch-billing-metrics)." %}
    ![Screenshot of the predefined AWS summary dashboard](images/hello_tutorial_aws_summary_dashboard.png)


### Learn More About Dashboards and Charts

This [90-second video](https://vmwaretv.vmware.com/embed/secure/iframe/entryId/1_gunwcmwm/uiConfId/49694343/pbc/252649793/st/0) gives a great overview of how to interact with dashboards and charts.

## Task 4: Set Up and Use an Out-of-the-Box Alert

Many integrations have preconfigured alerts for common use cases. All you have to do is:
* Clone the alert.
* Edit the thresholds (for most alerts).
* Specify who should receive the alert notification, i.e. the recipient of the alert.

<table style="width: 100%;">
<tbody>
<tr>
<td width="50%" markdown="span">
1. Navigate to the **Integrations** page.
<br/>
<br/>
2. Click the integration that you want to use.
   A configured integration has a green tick in the top right.
</td>
<td width="50%" markdown="span">![Screenshot of several integrations, icon with green tick in top right](images/featured_integrations.png) </td></tr>
<tr>
<td width="50%">
3. On the <strong>Alerts</strong> tab, click <strong>Install All</strong>. Here's an example screenshot from the AWS integration. Not all integrations have preconfigured alerts.
<br/><br/>
You can now edit the alert directly, but we recommend that you clone the alert so you don't lose your changes in case you reinstall the alerts.
</td>
<td width="50%" markdown="span">![Screenshot that shows the Alerts tab of the AWS integration](images/aws_alerts_install.png) </td></tr>
<tr>
<td width="50%">
4. From the toolbar, select <strong>Alerting > All Alerts</strong> and search for the alert by name.
   <br/><br/>In this example, we'll clone the <strong>EC2 Instance CPU Usage Too High</strong> alert. When you clone the alert, the new alert opens in Edit mode.
</td>
<td width="50%" markdown="span">![Screenshot of the Alerts Browser, where we've searched for the ECS instance CPU usage too high alert](images/clone_aws_alert.png) </td></tr>
<tr>
<td width="50%" >
5. Customize the thresholds. For example, you can set up the alert to be SEVERE when 97% of CPU utilization is reached.
6. Scroll down to the <strong>Recipients</strong> section. For the lowest severity level that you want notification for:<br/>
&nbsp;&nbsp; a. Click the plus (+) icon.<br/>
&nbsp;&nbsp; b. Enter your email address.<br/>
&nbsp;&nbsp; c. Press Enter.<br/>
<br/>
</td>
<td width="50%" markdown="span">![Screenshot of the recipients section of the alert where we've entered the email address](images/aws_specify_recipient.png) </td></tr>
<tr>
<td width="50%" >
When the threshold is exceeded, you'll receive an email that includes a link to the alert in the Alert Viewer. <br/><br/>The annotated screenshot on the right can help you get started with the Alert Viewer. <br/><br/>This <a href="https://vmwaretv.vmware.com/embed/secure/iframe/entryId/1_qdr0dtwr/uiConfId/49694343/pbc/252649793/st/0#">short video</a> shows what you can do.
</td>
<td width="50%" markdown="span">![Annotated screenshot of the alert viewer](images/alert_viewer.png) </td></tr>
</tbody>
</table>

### Learn More About Alerts

* [How Alerts Work & Tutorials for Alert Viewer and Alerts Browser](alerts.html)
* [Create and Manage Alerts](alerts_manage.html)
* [Alerts FAQs](alerts_faq.html)


## Video: Set Up Data Ingestion from a Windows Host

Watch the following video to learn how to ingest Windows host metrics.
<p>
<iframe id="kmsembed-1_0bbze8os" width="608" height="402" src="https://vmwaretv.vmware.com/embed/secure/iframe/entryId/1_0bbze8os/uiConfId/49694343/pbc/252649793/st/0" class="kmsembed" allowfullscreen webkitallowfullscreen mozAllowFullScreen allow="autoplay *; fullscreen *; encrypted-media *" referrerPolicy="no-referrer-when-downgrade" frameborder="0" alt="Setting up a Windows integration"></iframe>
</p>
You can also watch the video <a href="https://vmwaretv.vmware.com/media/t/1_0bbze8os" target="_blank">here <img src="/images/video_camera.png" alt="video camera icon"/>

## Frequently Asked Questions

Our Success Value Engineering team shared some frequently asked questions -- and where you can find the answers.

* How do I manage my account (permissions, preferences, etc.)? See [Customize Your Account](users_account_managing.html).
* How can admins add other users? See [Manage User Accounts](user-accounts.html).
* How do admins set up SSO? See [Single-Tenant Authentication and Self-Service SAML SSO](auth_self_service_sso.html)
* How do I track usage? See [Monitor Your Wavefront Service with the Wavefront Usage Integration](wavefront_monitoring.html).
* Why did my alert (not) fire? See [Why Did My Alert Not Fire?](alerts_faq.html#why-did-my-alert-not-fire) and [Why Did My Alert Misfire](alerts_faq.html#why-did-my-alert-misfire).
* How do I contact support? Start with [the gear icon menu](wavefront_support_feedback.html#support). See [How Do I Engage with Technical Support](https://help.wavefront.com/hc/en-us/articles/360057219171-How-to-Engage-Technical-Support) for details on severity levels, SLAs, and so on.
* Do you have videos? Yes! We have a [Tanzu Observability channel on VMware TV](https://vmwaretv.vmware.com/channel/Tanzu%2BObservability/252649793)!
