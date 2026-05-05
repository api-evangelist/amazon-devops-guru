---
title: "Reducing Your Organization’s Carbon Footprint with Amazon CodeGuru Profiler"
url: "https://aws.amazon.com/blogs/devops/reducing-your-organizations-carbon-footprint-with-codeguru-profiler/"
date: "Tue, 15 Nov 2022 19:52:19 +0000"
author: "Isha Dua"
feed_url: "https://aws.amazon.com/blogs/devops/category/artificial-intelligence/amazon-devops-guru/feed/"
---
<p>It is crucial to examine every functional area when firms reorient their operations toward sustainable practices. Making informed decisions is necessary to reduce the environmental effect of an IT stack when creating, deploying, and maintaining it. To build a sustainable business for our customers and for the world we all share, we have deployed data centers that provide the efficient, resilient service our customers expect while minimizing our environmental footprint—and theirs. While we work to improve the energy efficiency of our datacenters, we also work to help our customers improve their operations on the AWS cloud. This two-pronged approach is based on the concept of the shared responsibility between AWS and AWS’ customers. As shown in the diagram below, AWS focuses on optimizing the sustainability of the cloud, while customers are responsible for sustainability in the cloud, meaning that AWS customers must optimize the workloads they have on the AWS cloud.</p> 
<div class="wp-caption aligncenter" id="attachment_13762" style="width: 710px;">
 <img alt="Figure 1. Shared responsibility model for sustainability" class="wp-image-13762" height="295" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2022/11/07/image001-2.jpg" width="700" />
 <p class="wp-caption-text" id="caption-attachment-13762">Figure 1. Shared responsibility model for sustainability</p>
</div> 
<p>Just by migrating to the cloud, AWS customers become significantly more sustainable in their technology operations. On average, AWS customers use 77% fewer servers, 84% less power, and a 28% cleaner power mix, ultimately <a href="https://d1.awsstatic.com/institute/The%20carbon%20opportunity%20of%20moving%20to%20the%20cloud%20for%20APAC.pdf">reducing their carbon emissions</a> by 88% compared to when they ran workloads in their own data centers. These improvements are attributable to the technological advancements and economies of scale that AWS datacenters bring. However, there are still significant opportunities for AWS customers to make their cloud operations more sustainable. To uncover this, we must first understand how emissions are categorized.</p> 
<p>The <a href="https://ghgprotocol.org/">Greenhouse Gas Protocol</a> organizes carbon emissions into the following scopes, along with relevant emission examples within each scope for a cloud provider such as AWS:</p> 
<ul> 
 <li><strong>Scope 1</strong>: All direct emissions from the activities of an organization or under its control. For example, fuel combustion by data center backup generators.</li> 
 <li><strong>Scope 2</strong>: Indirect emissions from electricity purchased and used to power data centers and other facilities. For example, emissions from commercial power generation.</li> 
 <li><strong>Scope 3</strong>: All other indirect emissions from activities of an organization from sources it doesn’t control. AWS examples include emissions related to data center construction, and the manufacture and transportation of IT hardware deployed in data centers.</li> 
</ul> 
<p>From an AWS customer perspective, emissions from customer workloads running on AWS are accounted for as indirect emissions, and part of the customer’s Scope 3 emissions. Each workload deployed generates a fraction of the total AWS emissions from each of the previous scopes. The actual amount varies per workload and depends on several factors including the AWS services used, the energy consumed by those services, the carbon intensity of the electric grids serving the AWS data centers where they run, and the AWS procurement of renewable energy.</p> 
<p>At a high level, AWS customers approach optimization initiatives at three levels:</p> 
<ul> 
 <li><strong>Application (Architecture and Design)</strong>: Using efficient software designs and architectures to minimize the average resources required per unit of work.</li> 
 <li><strong>Resource (Provisioning and Utilization)</strong>: Monitoring workload activity and modifying the capacity of individual resources to prevent idling due to over-provisioning or under-utilization.</li> 
 <li><strong>Code (Code Optimization)</strong>: Using code profilers and other tools to identify the areas of code that use up the most time or resources as targets for optimization.</li> 
</ul> 
<p>In this blogpost, we will concentrate on code-level sustainability improvements and how they can be realized using <a href="https://docs.aws.amazon.com/codeguru/latest/profiler-ug/what-is-codeguru-profiler.html">Amazon CodeGuru Profiler</a>.</p> 
<h2><strong>How CodeGuru Profiler improves code sustainability</strong></h2> 
<p><a href="https://aws.amazon.com/codeguru/">Amazon CodeGuru</a> Profiler collects runtime performance data from your live applications and provides recommendations that can help you fine-tune your application performance. Using machine learning algorithms, CodeGuru Profiler can help you find your most CPU-intensive lines of code, which contribute the most to your scope 3 emissions. CodeGuru Profiler then suggests ways to improve the code to make it less CPU demanding. CodeGuru Profiler provides different visualizations of profiling data to help you identify what code is running on the CPU, see how much time is consumed, and suggest ways to reduce CPU utilization. Optimizing your code with CodeGuru profiler leads to the following:</p> 
<ul> 
 <li>Improvements in application performance</li> 
 <li>Reduction in cloud cost, and</li> 
 <li>Reduction in the carbon emissions attributable to your cloud workload.</li> 
</ul> 
<p>When your code performs the same task with less CPU, your applications run faster, customer experience improves, and your cost reduces alongside your cloud emission. CodeGuru Profiler generates the recommendations that help you make your code faster by using an agent that continuously samples stack traces from your application. The stack traces indicate how much time the CPU spends on each function or method in your code—information that is then transformed into CPU and latency data that is used to detect anomalies. When anomalies are detected, CodeGuru Profiler generates recommendations that clearly outline you should do to remediate the situation. Although CodeGuru Profiler has several visualizations that help you visualize your code, in many cases, customers can implement these recommendations without reviewing the visualizations. Let’s demonstrate this with a simple example.</p> 
<h2><strong>Demonstration: Using CodeGuru Profiler to optimize a Lambda function</strong></h2> 
<p>In this demonstration, the inefficiencies in a <a href="https://aws.amazon.com/lambda/">AWS Lambda</a> function will be identified by CodeGuru Profiler.</p> 
<h3><em>Building our Lambda Function (10mins)</em></h3> 
<p>To keep this demonstration quick and simple, let’s create a simple lambda function that display’s ‘Hello World’. Before writing the code for this function, let’s review two important concepts. First, when writing Python code that runs on AWS and calls AWS services, two critical steps are required:</p> 
<ul> 
 <li>Importing the <a href="https://docs.aws.amazon.com/pythonsdk">AWS SDK for Python (Boto3)</a>, and</li> 
 <li>Creating the AWS SDK service client.</li> 
</ul> 
<p>The Python code lines (that will be part of our function) that execute these steps listed above are shown below:</p> 
<p><code class="lang-python">import boto3 #this will import AWS SDK library for Python<br /> VariableName = boto3.client('dynamodb’) #this will create the AWS SDK service client </code></p> 
<p>Secondly, functionally, AWS Lambda functions comprise of two sections:</p> 
<ul> 
 <li><strong>Initialization</strong> code</li> 
 <li><strong>Handler</strong> code</li> 
</ul> 
<p>The first time a function is invoked (i.e., a cold start), Lambda downloads the function code, creates the required runtime environment, runs the initialization code, and then runs the handler code. During subsequent invocations (warm starts), to keep execution time low, Lambda bypasses the initialization code and goes straight to the handler code. AWS Lambda is designed such that the SDK service client created during initialization persists into the handler code execution. For this reason, AWS SDK service clients should be created in the initialization code. If the code lines for creating the AWS SDK service client are placed in the handler code, the AWS SDK service client will be recreated every time the Lambda function is invoked, needlessly increasing the duration of the Lambda function during cold and warm starts. This inadvertently increases CPU demand (and cost), which in turn increases the carbon emissions attributable to the customer’s code. Below, you can see the green and brown versions of the same Lambda function.</p> 
<p><img alt="" class="alignnone size-full wp-image-13771" height="598" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2022/11/08/Screen-Shot-2022-11-07-at-4.13.18-PM-1.png" width="1401" /></p> 
<p>Now that we understand the importance of structuring our Lambda function code for efficient execution, let’s create a Lambda function that recreates the SDK service client. We will then watch CodeGuru Profiler flag this issue and generate a recommendation.</p> 
<ol> 
 <li>Open AWS Lambda from the AWS Console and click on <strong>Create function</strong>.</li> 
 <li>Select Author from scratch, name the function ‘<strong>demo-function</strong>’, select <strong>Python 3.9</strong> under runtime, select <strong>x86_64</strong> under Architecture.</li> 
 <li>Expand <strong>Permissions</strong>, then choose whether to create a new execution role or use an existing one.</li> 
 <li>Expand <strong>Advanced settings</strong>, and then select <strong>Function URL</strong>.</li> 
 <li>For Auth type, choose <strong>AWS_IAM</strong> or <strong>NONE</strong>.</li> 
 <li>Select <strong>Configure cross-origin resource sharing (CORS)</strong>. By selecting this option during function creation, your function URL allows requests from all origins by default. You can edit the CORS settings for your function URL after creating the function.</li> 
 <li>Choose<strong> Create function</strong>.</li> 
 <li>In the code editor tab of the code source window, copy and paste the code below:</li> 
</ol> 
<pre><code class="lang-python">#invocation code
import json
import boto3

#handler code
def lambda_handler(event, context):
  client = boto3.client('dynamodb') #create AWS SDK Service client’
  #simple codeblock for demonstration purposes  
  output = ‘Hello World’
  print(output)
  #handler function return

  return output
</code></pre> 
<p>Ensure that the handler code is properly indented.</p> 
<ol start="9"> 
 <li>Save the code, Deploy, and then Test.</li> 
 <li>For the first execution of this Lambda function, a test event configuration dialog will appear. On the Configure test event dialog window, leave the selection as the default (Create new event), enter ‘demo-event’ as the Event name, and leave the hello-world template as the Event template.</li> 
 <li>When you run the code by clicking on Test, the console should return ‘Hello World’.</li> 
 <li>To simulate actual traffic, let’s run a curl script that will invoke the Lambda function every 0.2 seconds. On a bash terminal, run the following command:</li> 
</ol> 
<pre><code class="lang-python">while true; do curl {Lambda Function URL]; sleep 0.06; done</code></pre> 
<p>If you do not have git bash installed, you can use <a href="https://aws.amazon.com/cloud9/">AWS Cloud 9</a> which supports curl commands.</p> 
<h2>Enabling CodeGuru Profiler for our Lambda function</h2> 
<p>We will now set up CodeGuru Profiler to monitor our Lambda function. For Lambda functions running on Java 8 (Amazon Corretto), Java 11, and Python 3.8 or 3.9 runtimes, CodeGuru Profiler can be enabled through a single click in the configuration tab in the AWS Lambda console.&nbsp; Other runtimes can be enabled following a series of steps that can be found in the CodeGuru Profiler documentation for <a href="https://docs.aws.amazon.com/codeguru/latest/profiler-ug/integrating-with-java.html">Java</a> and the <a href="https://docs.aws.amazon.com/codeguru/latest/profiler-ug/integrating-with-python.html">Python</a>.</p> 
<p>Our demo code is written in Python 3.9, so we will enable Profiler from the configuration tab in the AWS Lambda console.</p> 
<ol> 
 <li>On the AWS Lambda console, select the <strong>demo-function</strong> that we created.</li> 
 <li>Navigate to <strong>Configuration &gt; Monitoring and operations tools</strong>, and click <strong>Edit</strong> on the right side of the page.</li> 
</ol> 
<p><img alt="" class="wp-image-13772 aligncenter" height="485" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2022/11/08/Screen-Shot-2022-11-07-at-4.19.46-PM.png" width="700" /></p> 
<ol start="3"> 
 <li>&nbsp;Scroll down to <strong>Amazon CodeGuru Profiler</strong> and click the button next to <strong>Code profiling</strong> to turn it on. After enabling Code profiling, click <strong>Save</strong>.</li> 
</ol> 
<p><img alt="" class="aligncenter wp-image-13773" height="636" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2022/11/08/Screen-Shot-2022-11-07-at-4.20.32-PM.png" width="700" /></p> 
<p><strong>Note</strong>: CodeGuru Profiler requires 5 minutes of Lambda runtime data to generate results. After your Lambda function provides this runtime data, which may need multiple runs if your lambda has a short runtime, it will display within the Profiling group page in the CodeGuru Profiler console. The <strong>profiling group</strong> will be given a default name (i.e., aws-lambda-&lt;lambda-function-name&gt;), and it will take approximately 15 minutes after CodeGuru Profiler receives the runtime data for this profiling group to appear. Be patient. Although our function duration is ~33ms, our curl script invokes the application once every 0.06 seconds. This should give profiler sufficient information to profile our function in a couple of hours. After 5 minutes, our profiling group should appear in the list of active profiling groups as shown below.</p> 
<p><img alt="" class="aligncenter wp-image-13774" height="171" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2022/11/08/Screen-Shot-2022-11-07-at-4.21.29-PM.png" style="border: 1px solid #000000;" width="700" /></p> 
<p>Depending on how frequently your Lambda function is invoked, it can take up to 15 minutes to aggregate profiles, after which you can see your first visualization in the CodeGuru Profiler console. The granularity of the first visualization depends on how active your function was during those first 5 minutes of profiling—an application that is idle most of the time doesn’t have many data points to plot in the default visualization. However, you can remedy this by looking at a wider time period of profiled data, for example, a day or even up to a week, if your application has very low CPU utilization. For our demo function, a recommendation should appear after about an hour. By this time, the profiling groups list should show that our profiling group now has one recommendation.<img alt="" class="aligncenter wp-image-13775" height="171" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2022/11/08/Screen-Shot-2022-11-07-at-4.21.42-PM.png" width="700" /></p> 
<p>Profiler has now flagged the repeated creation of the SDK service client with every invocation.<img alt="" class="wp-image-13776 aligncenter" height="417" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2022/11/08/Screen-Shot-2022-11-07-at-4.21.59-PM.png" width="700" /></p> 
<p>From the information provided, we can see that our CPU is spending 5x more computing time than expected on the recreation of the SDK service client. The estimated cost impact of this inefficiency is also provided. In production environments, the cost impact of seemingly minor inefficiencies can scale very quickly to several kilograms of CO2 and hundreds of dollars as invocation frequency, and the number of Lambda functions increase.</p> 
<p>CodeGuru Profiler integrates with Amazon DevOps Guru, a fully managed service that makes it easy for developers and operators to improve the performance and availability of their applications. Amazon DevOps Guru analyzes operational data and application metrics to identify behaviors that deviate from normal operating patterns. Once these operational anomalies are detected, DevOps Guru presents intelligent recommendations that address current and predicted future operational issues. By integrating with CodeGuru Profiler, customers can now view operational anomalies and code optimization recommendations on the DevOps Guru console. The integration, which is enabled by default, is only applicable to Lambda resources that are supported by CodeGuru Profiler and monitored by both DevOps Guru and CodeGuru.</p> 
<p>We can now stop the curl loop (Control+C) so that the Lambda function stops running. Next, we <a href="https://docs.aws.amazon.com/codeguru/latest/profiler-ug/working-with-profiling-groups-delete.html">delete the profiling group</a> that was created when we enabled profiling in Lambda, and then delete the Lambda function or repurpose as needed.</p> 
<h2><strong>Conclusion</strong></h2> 
<p>Cloud sustainability is a shared responsibility between AWS and our customers. While we work to make our datacenter more sustainable, customers also have to work to make their code, resources, and applications more sustainable, and CodeGuru Profiler can help you improve code sustainability, as demonstrated above. To start Profiling your code today, visit the CodeGuru Profiler <a href="https://docs.aws.amazon.com/codeguru/latest/profiler-ug/what-is-codeguru-profiler.html">documentation page</a>. To start monitoring your applications, head over to the Amazon DevOps Guru <a href="https://docs.aws.amazon.com/devops-guru/latest/userguide/welcome.html">documentation page</a>.</p> 
<p><strong>About the authors:</strong></p> 
<footer> 
 <div class="blog-author-box"> 
  <div class="blog-author-image">
   <img alt="" class="aligncenter size-full wp-image-11636" height="160" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2022/11/14/Isha-Dua.jpg" width="120" />
  </div> 
  <h3 class="lb-h4">Isha Dua</h3> 
  <p>Isha Dua is a Senior Solutions Architect based in San Francisco Bay Area. She helps AWS Enterprise customers grow by understanding their goals and challenges, and guiding them on how they can architect their applications in a cloud native manner while making sure they are resilient and scalable. She’s passionate about machine learning technologies and Environmental Sustainability.</p> 
 </div> 
</footer> 
<footer> 
 <div class="blog-author-box"> 
  <div class="blog-author-image">
   <img alt="" class="aligncenter wp-image-11636" height="160" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2022/11/14/tomelc.jpg" width="120" />
  </div> 
  <h3 class="lb-h4">Christian Tomeldan</h3> 
  <p>Christian Tomeldan is a DevOps Engineer turned Solutions Architect. Operating out of San Francisco, he is passionate about technology and conveys that passion to customers ensuring they grow with the right support and best practices. He focuses his technical depth mostly around Containers, Security, and Environmental Sustainability.</p> 
 </div> 
</footer> 
<footer> 
 <div class="blog-author-box"> 
  <div class="blog-author-image">
   <img alt="" class="aligncenter wp-image-11636" height="160" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2022/11/14/ifokafor.jpg" width="120" />
  </div> 
  <h3 class="lb-h4">Ifeanyi Okafor</h3> 
  <p>Ifeanyi Okafor is a Product Manager with AWS. He enjoys building products that solve customer problems at scale.</p> 
 </div> 
</footer>
