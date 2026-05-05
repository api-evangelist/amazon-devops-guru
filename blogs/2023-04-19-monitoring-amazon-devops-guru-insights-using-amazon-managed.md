---
title: "Monitoring Amazon DevOps Guru insights using Amazon Managed Grafana"
url: "https://aws.amazon.com/blogs/devops/monitoring-amazon-devops-guru-insights-using-amazon-managed-grafana/"
date: "Wed, 19 Apr 2023 19:27:40 +0000"
author: "MJ Kubba"
feed_url: "https://aws.amazon.com/blogs/devops/category/artificial-intelligence/amazon-devops-guru/feed/"
---
<p>As organizations operate day-to-day, having insights into their cloud infrastructure state can be crucial for the durability and availability of their systems. Industry research estimates<a href="#_ftn1" name="_ftnref1">[1]</a> that downtime costs small businesses around $427 per minute of downtime, and medium to large businesses an average of $9,000 per minute of downtime. <a href="https://aws.amazon.com/devops-guru/">Amazon DevOps Guru</a> customers want to monitor and generate alerts using a single dashboard. This allows them to reduce context switching between applications, providing them an opportunity to respond to operational issues faster.</p> 
<p>DevOps Guru can integrate with <a href="https://aws.amazon.com/grafana/">Amazon Managed Grafana</a> to create and display operational insights. Alerts can be created and communicated for any critical events captured by DevOps Guru and notifications can be sent to operation teams to respond to these events. The key telemetry data types of logs and metrics are parsed and filtered to provide the necessary insights into observability.</p> 
<p>Furthermore, it&nbsp;provides plug-ins to popular open-source databases, third-party ISV monitoring tools, and other cloud services. With Amazon Managed Grafana, you can easily visualize information from multiple AWS services, AWS accounts, and Regions in a single Grafana dashboard.</p> 
<p>In this post, we will walk you through integrating the insights generated from DevOps Guru with Amazon Managed Grafana.</p> 
<p><strong>Solution Overview:</strong></p> 
<p><img alt="This architecture diagram shows the flow of the logs and metrics that will be utilized by Amazon Managed Grafana. Insights originate from DevOps Guru, each insight generating an event. These events are captured by Amazon EventBridge, and then saved as logs to Amazon CloudWatch Log Group DevOps Guru service metrics, and then parsed by Amazon Managed Grafana to create new dashboards." class="aligncenter wp-image-15093 size-full" height="270" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/04/19/overview.png" width="941" /></p> 
<p>This architecture diagram shows the flow of the logs and metrics that will be utilized by Amazon Managed Grafana, starting with DevOps Guru and then using <a href="https://aws.amazon.com/eventbridge/">Amazon EventBridge</a> to save the insight event logs to <a href="https://aws.amazon.com/cloudwatch/">Amazon CloudWatch</a> Log Group DevOps Guru <a href="https://docs.aws.amazon.com/devops-guru/latest/userguide/monitoring-cloudwatch.html">service metrics</a> to be parsed by Amazon Managed Grafana and create new dashboards in Grafana from these logs and Metrics.</p> 
<p>Now we will walk you through how to do this and set up notifications to your operations team.</p> 
<h3></h3> 
<h3>Prerequisites:</h3> 
<p>The following prerequisites are required for this walkthrough:</p> 
<ul> 
 <li>An <a href="https://aws.amazon.com/resources/create-account/">AWS Account</a></li> 
 <li><a href="https://docs.aws.amazon.com/devops-guru/latest/userguide/getting-started-enable-service.html">Enabled</a> DevOps Guru on your account with CloudFormation stack, or tagged resources monitored.</li> 
</ul> 
<h2>Using Amazon CloudWatch Metrics</h2> 
<p><strong>&nbsp;</strong></p> 
<p>DevOps Guru sends <a href="https://docs.aws.amazon.com/devops-guru/latest/userguide/monitoring-cloudwatch.html">service metrics</a> to CloudWatch Metrics. We will use these to&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;track metrics for insights and metrics for your DevOps Guru usage; the DevOps Guru service reports the metrics to the <strong>AWS/DevOps-Guru</strong> namespace in CloudWatch by default.</p> 
<p>First, we will provision an Amazon Managed Grafana workspace and then create a Dashboard in the workspace that uses Amazon CloudWatch as a data source.</p> 
<h3>Setting up Amazon CloudWatch Metrics</h3> 
<ol> 
 <li>Create Grafana Workspace<br /> Navigate to <strong>Amazon Managed Grafana</strong> from AWS console, then click Create <strong>workspace</strong></li> 
</ol> 
<p style="padding-left: 40px;">a. Select the Authentication mechanism</p> 
<p style="padding-left: 80px;">i. <a href="https://aws.amazon.com/iam/identity-center/">AWS IAM Identity Center (AWS SSO)</a> or SAML v2 based Identity Providers</p> 
<p style="padding-left: 80px;">ii. Service Managed Permission or Customer Managed</p> 
<p style="padding-left: 80px;">iii. Choose Next</p> 
<p style="padding-left: 40px;">b. Under “Data sources and notification channels”, choose Amazon CloudWatch</p> 
<p style="padding-left: 40px;">c. Create the Service.</p> 
<p>You can use <a href="https://aws.amazon.com/blogs/mt/amazon-managed-grafana-supports-direct-saml-integration-with-identity-providers/">this post</a> for more information on how to create and configure the Grafana workspace with SAML based authentication.</p> 
<p>Next, we will show you how to create a dashboard and parse the Logs and Metrics to display the DevOps Guru insights and recommendations.</p> 
<p style="padding-left: 40px;">2. Configure Amazon Managed Grafana</p> 
<p style="padding-left: 80px;">a. Add <strong>CloudWatch</strong> as a data source:<br /> From the left bar navigation menu, hover over AWS and select <strong>Data sources</strong><strong>.</strong></p> 
<p style="padding-left: 80px;">b. From the Services dropdown select and configure <strong>CloudWatch</strong>.</p> 
<p style="padding-left: 40px;">3. Create a <strong>Dashboard</strong></p> 
<p style="padding-left: 80px;">a. From the left navigation bar, click on add a <strong>new Panel.</strong></p> 
<p style="padding-left: 80px;">b. You will see a demo panel.</p> 
<p style="padding-left: 80px;">c. In the demo panel – Click on <strong>Data source</strong> and select <strong>Amazon CloudWatch.<br /> </strong></p> 
<p><img alt="The Amazon Grafana Workspace dashboard with the Grafana data source dropdown menu open. The drop down has 'Amazon CloudWatch (region name)' highlighted, other options include 'Mixed, 'Dashboard', and 'Grafana'." class="aligncenter wp-image-15094 size-full" height="543" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/04/19/dash1.png" width="766" /></p> 
<p style="padding-left: 80px;">d. For this panel we will use CloudWatch metrics to display the number of insights.</p> 
<p style="padding-left: 80px;">e. From Namespace select the <strong>AWS/DevOps-Guru</strong> name space, <strong>Insights</strong> as Metric name and <strong>Average</strong> for Statistics.</p> 
<p><img alt="In the Amazon Grafana Workspace dashboard the user has entered values in three fields. &quot;Grafana Query with Namespace&quot; has the chosen value: AWS/DevOps-Guru. &quot;Metric name&quot; has the chosen value: Insights. &quot;Statistic&quot; has the chosen value: Average." class="aligncenter wp-image-15095 size-full" height="420" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/04/19/dash2.png" width="941" /></p> 
<p style="padding-left: 80px;">click <strong>apply</strong></p> 
<p><img alt="Time series graph contains a single new data point, indicting a recent event." class="aligncenter wp-image-15096 size-full" height="402" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/04/19/panel1.png" width="941" /></p> 
<p style="padding-left: 80px;">f. This is our first panel. We can change the panel name from the right-side bar under <strong>Title</strong>. We will name this panel “<strong>Insights</strong>”</p> 
<p style="padding-left: 80px;">g. From the top right menu, click <strong>save dashboard</strong> and give your new dashboard a name</p> 
<h2></h2> 
<h2>Using Amazon CloudWatch Logs via <a href="https://aws.amazon.com/eventbridge/">Amazon EventBridge</a></h2> 
<p>For other insights outside of the service metrics, such as a number of insights per specific service or the average for a region or for a specific AWS account, we will need to parse the event logs. These logs first need to be sent to Amazon CloudWatch Logs. We will go over the details on how to set this up and how we can parse these logs in Amazon Managed Grafana using CloudWatch Logs Query Syntax. In this post, we will show a couple of examples. For more details, please check out this <a href="https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/CWL_QuerySyntax.html">User Guide documentation</a>. This is not done by default and we will need to use Amazon EventBridge to pass these logs to CloudWatch.</p> 
<p>DevOps Guru logs include other details that can be helpful when building Dashboards, such as region, Insight Severity (High, Medium, or Low), associated resources, and DevOps guru dashboard URL, among other things.&nbsp; For more information, please check out this <a href="https://docs.aws.amazon.com/devops-guru/latest/userguide/working-with-eventbridge.html">User Guide documentation</a>.</p> 
<p>EventBridge offers a serverless event bus that helps you receive, filter, transform, route, and deliver events. It provides one to many messaging solutions to support decoupled architectures, and it is easy to integrate with AWS Services and 3rd-party tools. Using Amazon EventBridge with DevOps Guru provides a solution that is easy to extend to create a ticketing system through integrations with <a href="https://aws.amazon.com/eventbridge/integrations/">ServiceNow, Jira, and other tools</a>. It also makes it easy to set up alert systems through integrations with PagerDuty, Slack, and more.</p> 
<p><strong>&nbsp;</strong></p> 
<h3>Setting up Amazon CloudWatch Logs</h3> 
<ol start="4"> 
 <li>Let’s dive in to creating the EventBridge rule and enhance our Grafana dashboard:</li> 
</ol> 
<p style="padding-left: 80px;">a. First head to <strong>Amazon EventBridge</strong> in the AWS console.</p> 
<p style="padding-left: 80px;">b. Click <strong>Create</strong> <strong>rule.</strong></p> 
<p style="padding-left: 80px;">&nbsp; &nbsp; &nbsp;Type in rule <strong>Name</strong> and <strong>Description</strong>. You can leave the Event bus to default and Rule type to Rule with an event pattern.</p> 
<p style="padding-left: 80px;">c. Select AWS events or EventBridge partner events.</p> 
<p style="padding-left: 80px;">&nbsp; &nbsp; For event Pattern change to Customer patterns (JSON editor) and use:</p> 
<pre style="padding-left: 80px;"><code class="lang-json">{"source": ["aws.devops-guru"]}</code></pre> 
<p style="padding-left: 80px;">This filters for all events generated from DevOps Guru. You can use the same mechanism to filter out specific messages such as new insights, or insights closed to a different channel. For this demonstration, let’s consider extracting all events.</p> 
<p><img alt="As the user configures their EventBridge Rule, for the Creation method they have chosen &quot;Custom pattern (JSON editor) write an event pattern in JSON.&quot; For the Event pattern editor just below they have entered {&quot;source&quot;:[&quot;aws.devops-guru&quot;]}" class="aligncenter wp-image-15097 size-full" height="956" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/04/19/eventbridge.png" width="941" /></p> 
<p style="padding-left: 80px;">d. Next, for Target, select <strong>AWS servic</strong><strong>e.</strong></p> 
<p style="padding-left: 80px;">&nbsp; &nbsp; Then use CloudWatch log Group.</p> 
<p style="padding-left: 80px;">&nbsp; &nbsp; For the Log Group, give your group a name, such as “<strong>devops-guru</strong>”.</p> 
<p><img alt="In the prompt for the new Target's configurations, the user has chosen AWS service as the Target type. For the Select a target drop down, they chose CloudWatch log Group. For the log group, they selected the /aws/events radio option, and then filled in the following input text box with the kebab case group name devops-guru." class="aligncenter wp-image-15098 size-full" height="566" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/04/19/target.png" width="941" /></p> 
<p style="padding-left: 80px;">e. Click <strong>Create</strong> <strong>rule.</strong></p> 
<p style="padding-left: 80px;">f. Navigate back to Amazon Managed Grafana.<br /> It’s time to add a couple more additional Panels to our dashboard.&nbsp; Click <strong>Add panel.<br /> </strong>&nbsp; &nbsp; Then Select <strong>Amazon CloudWatch</strong>, and change from metrics to CloudWatch Logs and select the Log Group we created previously.</p> 
<p><img alt="In the Grafana Workspace, the user has &quot;Data source&quot; selected as Amazon CloudWatch us-east-1. Underneath that they have chosen to use the default region and CloudWatch Logs. Below that, for the Log Groups they have entered /aws/events/DevOpsGuru" class="aligncenter wp-image-15099 size-full" height="568" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/04/19/insight.png" width="941" /></p> 
<p style="padding-left: 80px;">g. For the query use the following to get the number of closed insights:</p> 
<div class="hide-language"> 
 <pre style="padding-left: 80px;"><code class="lang-sql">fields @detail.messageType
| filter detail.messageType="CLOSED_INSIGHT"
| count(detail.messageType)</code></pre> 
</div> 
<p style="padding-left: 80px;">You’ll see the new dashboard get updated with “Data is missing a time field”.</p> 
<p><img alt="New panel suggestion with switch to table or open visualization suggestions" class="aligncenter wp-image-15100 size-full" height="387" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/04/19/paneldata.png" width="941" /></p> 
<p style="padding-left: 80px;">You can either open the suggestions and select a <strong>gauge</strong> that makes sense;</p> 
<p><img alt="New Suggestions display a dial graph, a bar graph, and a count numerical tracker " class="aligncenter wp-image-15101 size-large" height="1024" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/04/19/suggestions-773x1024.png" width="773" /></p> 
<p style="padding-left: 80px;">Or choose from multiple visualization options.</p> 
<p style="padding-left: 80px;">Now we have 2 panels:</p> 
<p><img alt="Two panels are shown, one is the new dial graph, and the other is the time series graph that was created earlier." class="aligncenter wp-image-15102 size-full" height="727" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/04/19/panel3.png" width="941" /></p> 
<p style="padding-left: 80px;">h. You can repeat the same process. To create 3<sup>rd</sup> panel for the new insights using this query:</p> 
<div class="hide-language"> 
 <pre style="padding-left: 80px;"><code class="lang-sql">fields @detail.messageType 
| filter detail.messageType="NEW_INSIGHT" 
| count(detail.messageType)</code></pre> 
</div> 
<p style="padding-left: 80px;">Now we have 3 panels:</p> 
<p><img alt="Grafana now shows three 3 panels. Two dial graphs, and the time series graph." class="aligncenter wp-image-15103 size-full" height="366" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/04/19/panel4.png" width="941" /></p> 
<p style="padding-left: 80px;">Next, depending on the visualizations, you can work with the Logs and metrics data types to parse and filter the data.</p> 
<p><img alt="Setting up a 4th panel as table. Under the Query tab, in the query editor, the user has entered the text: fields detail.messageType, detail.insightSeverity, detail.insightUrlfilter | filter detail.messageType=&quot;CLOSED_INSIGHT&quot; or detail.messageType=&quot;NEW_INSIGHT&quot;" class="aligncenter wp-image-15104 size-full" height="520" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/04/19/panel5.png" width="941" /></p> 
<p style="padding-left: 80px;">i. For our fourth panel, we will add DevOps Guru dashboard direct link to the AWS Console.</p> 
<p style="padding-left: 80px;">Repeat the same process as demonstrated previously one more time with this query:</p> 
<div class="hide-language"> 
 <pre style="padding-left: 80px;"><code class="lang-sql">fields detail.messageType, detail.insightSeverity, detail.insightUrlfilter 
| filter detail.messageType="CLOSED_INSIGHT" or detail.messageType="NEW_INSIGHT"</code><strong style="font-family: Georgia, 'Times New Roman', 'Bitstream Charter', Times, serif;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</strong></pre> 
</div> 
<p style="padding-left: 80px;"><strong>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; </strong>Switch to table when prompted on the panel.</p> 
<p><img alt="Grafana now shows 4 panels. The new panel displays a data table that contains information about the most recent DevOps Guru insights. There are also the two dial graphs, and the time series graph from before." class="aligncenter wp-image-15105 size-full" height="512" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/04/19/panel6.png" width="941" /></p> 
<p style="padding-left: 80px;">This will give us a direct link to the DevOps Guru dashboard and help us get to the insight details and Recommendations.</p> 
<p><img alt="Grafana now shows 4 panels. The new panel displays a data table that contains information about the most recent DevOps Guru insights. There are also the two dial graphs, and the time series graph from before." class="aligncenter wp-image-15106 size-full" height="577" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/04/19/devopsguru.png" width="941" /></p> 
<p style="padding-left: 80px;">Save your dashboard.</p> 
<ol start="5"> 
 <li>You can extend observability by sending notifications through alerts on dashboards of panels providing metrics. The alerts will be triggered when a condition is met. The Alerts are communicated with <a href="https://aws.amazon.com/sns/">Amazon SNS</a> notification mechanism. This is our SNS notification channel setup.</li> 
</ol> 
<p><img alt="Screenshot: notification settings show Name: DevopsGuruAlertsFromGrafana and Type: SNS" class="aligncenter wp-image-15107 size-full" height="189" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/04/19/alert1.png" width="941" /></p> 
<p style="padding-left: 40px;">A previously created notification is used next to communicate any alerts when the condition is met across the metrics being observed.</p> 
<p><img alt="Screenshot: notification setting with condition when count of query is above 5, a notification is sent to DevopsGuruAlertsFromGrafana with message, &quot;More than 5 insights in the past 1 hour&quot;" class="aligncenter wp-image-15108 size-full" height="550" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/04/19/alert2.png" width="941" /></p> 
<p><strong>Cleanup</strong></p> 
<p>To avoid incurring future charges, delete the resources.</p> 
<ul> 
 <li>Navigate to <strong>EventBridge</strong> in AWS console and delete the rule created in step 4 (a-e) “devops-guru”.</li> 
 <li>Navigate to <strong>CloudWatch</strong> logs in AWS console and delete the log group created as results of step 4 (a-e) named “devops-guru”.</li> 
 <li>Amazon Managed Grafana: Navigate to Amazon <strong>Managed Grafana service</strong> and delete the Grafana services you created in step 1.</li> 
</ul> 
<p><strong>Conclusion</strong></p> 
<p>In this post, we have demonstrated how to successfully incorporate Amazon DevOps Guru insights into Amazon Managed Grafana and use Grafana as the observability tool. This will allow Operations team to successfully observe the state of their AWS resources and notify them through Alarms on any preset thresholds on DevOps Guru metrics and logs. You can expand on this to create other panels and dashboards specific to your needs. If you don’t have DevOps Guru, you can start monitoring your AWS applications with AWS DevOps Guru today using this <a href="https://aws.amazon.com/devops-guru/">link</a>.</p> 
<p><a href="#_ftnref1" name="_ftn1">[1]</a> https://www.atlassian.com/incident-management/kpis/cost-of-downtime</p> 
<p><strong>About the authors:</strong></p> 
<footer> 
 <div class="blog-author-box"> 
  <div class="blog-author-image">
   <img alt="" class="aligncenter size-full wp-image-11636" height="160" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2022/12/26/MJ-Kubba.jpg" width="120" />
  </div> 
  <h3 class="lb-h4">MJ Kubba</h3> 
  <p>MJ Kubba is a Solutions Architect who enjoys working with public sector customers to build solutions that meet their business needs. MJ has over 15 years of experience designing and implementing software solutions. He has a keen passion for DevOps and cultural transformation.</p> 
 </div> 
 <div class="blog-author-box"> 
  <div class="blog-author-image">
   <img alt="" class="aligncenter size-full wp-image-11636" height="160" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2022/10/28/david.png" width="120" />
  </div> 
  <h3 class="lb-h4">David Ernst</h3> 
  <p>David is a Sr. Specialist Solution Architect – DevOps, with 20+ years of experience in designing and implementing software solutions for various industries. David is an automation enthusiast and works with AWS customers to design, deploy, and manage their AWS workloads/architectures.</p> 
 </div> 
 <div class="blog-author-box"> 
  <div class="blog-author-image">
   <img alt="" class="aligncenter size-full wp-image-11636" height="160" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/04/19/sofiaslackpic.jpg" width="120" />
  </div> 
  <h3 class="lb-h4">Sofia Kendall</h3> 
  <p>Sofia Kendall is a Solutions Architect who helps small and medium businesses achieve their goals as they utilize the cloud. Sofia has a background in Software Engineering and enjoys working to make systems reliable, efficient, and scalable.</p> 
 </div> 
</footer>
