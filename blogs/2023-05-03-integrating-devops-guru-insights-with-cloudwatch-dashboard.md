---
title: "Integrating DevOps Guru Insights with CloudWatch Dashboard"
url: "https://aws.amazon.com/blogs/devops/integrating-devops-guru-insights-with-cloudwatch-dashboard/"
date: "Wed, 03 May 2023 21:31:48 +0000"
author: "Suresh Babu"
feed_url: "https://aws.amazon.com/blogs/devops/category/artificial-intelligence/amazon-devops-guru/feed/"
---
<p>Many customers use <a href="https://aws.amazon.com/cloudwatch/">Amazon CloudWatch</a> dashboards to monitor applications and often ask how they can integrate <a href="https://aws.amazon.com/devops-guru/">Amazon DevOps Guru</a> Insights in order to have a unified dashboard for monitoring. &nbsp;This blog post showcases integrating DevOps Guru proactive and reactive insights to a CloudWatch dashboard by using Custom Widgets. It can help you to correlate trends over time and spot issues more efficiently by displaying related data from different sources side by side and to have a single pane of glass visualization in the CloudWatch dashboard.</p> 
<p><a href="https://aws.amazon.com/devops-guru/">Amazon DevOps Guru</a> is a machine learning (ML) powered service that helps developers and operators automatically detect anomalies and improve application availability. DevOps Guru’s anomaly detectors can proactively detect anomalous behavior even before it occurs, helping you address issues before they happen; detailed <a href="https://docs.aws.amazon.com/devops-guru/latest/userguide/working-with-insights.html">insights</a> provide recommendations to mitigate that behavior.</p> 
<p><a href="https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Dashboards.html">Amazon CloudWatch dashboard</a> is a customizable home page in the <a href="https://aws.amazon.com/blogs/aws/cloudwatch-dashboards-create-use-customized-metrics-views/">CloudWatch console</a> that monitors multiple resources in a single view. You can use CloudWatch dashboards to create customized views of the metrics and alarms for your AWS resources.</p> 
<h2>Solution overview</h2> 
<p>This post will help you to create a Custom Widget for Amazon CloudWatch dashboard that displays DevOps Guru Insights. A custom widget is part of your CloudWatch dashboard that calls an AWS Lambda function containing your custom code. The Lambda function accepts custom parameters, generates your dataset or visualization, and then returns HTML to the CloudWatch dashboard. The CloudWatch dashboard will display this HTML as a widget. In this post, we are providing sample code for the Lambda function that will call DevOps Guru APIs to retrieve the insights information and displays as a widget in the CloudWatch dashboard. The architecture diagram of the solution is below.</p> 
<p><img alt="Solution Architecture" class="wp-image-15198 size-full" height="814" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/04/27/figure1.1.1.jpg" width="2592" /></p> 
<p class="wp-caption-text" id="caption-attachment-15064">Figure 1: Reference architecture diagram</p> 
<h2>Prerequisites and Assumptions</h2> 
<ul> 
 <li>An AWS account. To sign up: 
  <ul> 
   <li>Create an AWS account. For instructions, see <a href="https://lakeformation.aworkshop.io/30-prerequisite/301-sign-up-aws.html">Sign Up For AWS</a>.</li> 
  </ul> </li> 
 <li>DevOps Guru should be enabled in the account. For enabling DevOps guru, see <a href="https://docs.aws.amazon.com/devops-guru/latest/userguide/setting-up.html">DevOps Guru Setup</a></li> 
 <li>Follow this <a href="https://catalog.us-east-1.prod.workshops.aws/workshops/f92df379-6add-4101-8b4b-38b788e1222b/en-US">Workshop</a> to deploy a sample application in your AWS Account which can help generate some DevOps Guru insights.</li> 
</ul> 
<h2>Solution Deployment</h2> 
<p>We are providing two options to deploy the solution – using the AWS console and <a href="https://aws.amazon.com/cloudformation/">AWS CloudFormation</a>. The first section has instructions to deploy using the AWS console followed by instructions for using CloudFormation. The key difference is that we will create one Widget while using the Console, but three Widgets are created when we use AWS CloudFormation.</p> 
<h2><strong>Using the AWS Console:</strong></h2> 
<p>We will first create a Lambda function that will retrieve the DevOps Guru insights. We will then modify the default IAM role associated with the Lambda function to add DevOps Guru permissions. Finally we will create a CloudWatch dashboard and add a custom widget to display the DevOps Guru insights.</p> 
<ol> 
 <li>Navigate to the Lambda Console after logging to your AWS Account and click on Create function. <p></p>
  <div class="wp-caption alignnone" id="attachment_15208" style="width: 2900px;">
   <img alt="Figure 2a: Create Lambda Function" class="size-full wp-image-15208" height="342" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/04/27/figure2a-1.jpg" width="2890" />
   <p class="wp-caption-text" id="caption-attachment-15208">Figure 2a: Create Lambda Function</p>
  </div></li> 
 <li>Choose Author from Scratch and use the runtime Node.js 16.x. Leave the rest of the settings at default and create the function. <p></p>
  <div class="wp-caption alignnone" id="attachment_15213" style="width: 3030px;">
   <img alt="Figure 2b: Create Lambda Function" class="size-full wp-image-15213" height="1536" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/04/27/figure2b-1.jpg" width="3020" />
   <p class="wp-caption-text" id="caption-attachment-15213">Figure 2b: Create Lambda Function</p>
  </div></li> 
 <li>After a few seconds, the Lambda function will be created and you will see a code source box. Copy the code from the text box below and replace the code present in code source as shown in screen print below. <pre><code class="lang-js">// SPDX-License-Identifier: MIT-0
// CloudWatch Custom Widget sample: displays count of Amazon DevOps Guru Insights
const aws = require('aws-sdk');

const DOCS = `## DevOps Guru Insights Count
Displays the total counts of Proactive and Reactive Insights in DevOps Guru.
`;

async function getProactiveInsightsCount(DevOpsGuru, StartTime, EndTime) {
    let NextToken = null;
    let proactivecount=0;

    do {
        const args = { StatusFilter: { Any : { StartTimeRange: { FromTime: StartTime, ToTime: EndTime }, Type: 'PROACTIVE'  }}}
        const result = await DevOpsGuru.listInsights(args).promise();
        console.log(result)
        NextToken = result.NextToken;
        result.ProactiveInsights.forEach(res =&amp;gt; {
        console.log(result.ProactiveInsights[0].Status)
        proactivecount++;
        });
        } while (NextToken);
    return proactivecount;
}

async function getReactiveInsightsCount(DevOpsGuru, StartTime, EndTime) {
    let NextToken = null;
    let reactivecount=0;

    do {
        const args = { StatusFilter: { Any : { StartTimeRange: { FromTime: StartTime, ToTime: EndTime }, Type: 'REACTIVE'  }}}
        const result = await DevOpsGuru.listInsights(args).promise();
        NextToken = result.NextToken;
        result.ReactiveInsights.forEach(res =&amp;gt; {
        reactivecount++;
        });
        } while (NextToken);
    return reactivecount;
}

function getHtmlOutput(proactivecount, reactivecount, region, event, context) {

    return `DevOps Guru Proactive Insights&amp;lt;br&amp;gt;&amp;lt;font size="+10" color="#FF9900"&amp;gt;${proactivecount}&amp;lt;/font&amp;gt;
    &amp;lt;p&amp;gt;DevOps Guru Reactive Insights&amp;lt;/p&amp;gt;&amp;lt;font size="+10" color="#FF9900"&amp;gt;${reactivecount}`;
}

exports.handler = async (event, context) =&amp;gt; {
    if (event.describe) {
        return DOCS;
    }
    const widgetContext = event.widgetContext;
    const timeRange = widgetContext.timeRange.zoom || widgetContext.timeRange;
    const StartTime = new Date(timeRange.start);
    const EndTime = new Date(timeRange.end);
    const region = event.region || process.env.AWS_REGION;
    const DevOpsGuru = new aws.DevOpsGuru({ region });

    const proactivecount = await getProactiveInsightsCount(DevOpsGuru, StartTime, EndTime);
    const reactivecount = await getReactiveInsightsCount(DevOpsGuru, StartTime, EndTime);

    return getHtmlOutput(proactivecount, reactivecount, region, event, context);
    
};</code></pre> <p></p>
  <div class="wp-caption alignnone" id="attachment_15228" style="width: 3220px;">
   <img alt="Figure 3: Lambda Function Source Code" class="size-full wp-image-15228" height="1306" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/04/27/figure3.jpg" width="3210" />
   <p class="wp-caption-text" id="caption-attachment-15228">Figure 3: Lambda Function Source Code</p>
  </div></li> 
 <li>Click on Deploy to save the function code</li> 
 <li>Since we used the default settings while creating the function, a default Execution role is created and associated with the function. We will need to modify the IAM role to grant DevOps Guru permissions to retrieve Proactive and Reactive insights.</li> 
 <li>Click on the Configuration tab and select Permissions from the left side option list. You can see the IAM execution role associated with the function as shown in figure 4. <p></p>
  <div class="wp-caption alignnone" id="attachment_15220" style="width: 2830px;">
   <img alt="Figure 4: Lambda function execution role" class="size-full wp-image-15220" height="824" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/04/27/figure4.jpg" width="2820" />
   <p class="wp-caption-text" id="caption-attachment-15220">Figure 4: Lambda function execution role</p>
  </div></li> 
 <li>Click on the IAM role name to open the role in the IAM console. Click on Add Permissions and select Attach policies. <p></p>
  <div class="wp-caption alignnone" id="attachment_15221" style="width: 2740px;">
   <img alt="Figure 5: IAM Role Update" class="size-full wp-image-15221" height="634" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/04/27/Figure5-1.jpg" width="2730" />
   <p class="wp-caption-text" id="caption-attachment-15221">Figure 5: IAM Role Update</p>
  </div></li> 
 <li>Search for DevOps and select the AmazonDevOpsGuruReadOnlyAccess. Click on Add permissions to update the IAM role. <p></p>
  <div class="wp-caption alignnone" id="attachment_15222" style="width: 2754px;">
   <img alt="Figure 6: IAM Role Policy Update" class="size-full wp-image-15222" height="824" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/04/27/figure6.jpg" width="2744" />
   <p class="wp-caption-text" id="caption-attachment-15222">Figure 6: IAM Role Policy Update</p>
  </div></li> 
 <li>Now that we have created the Lambda function for our custom widget and assigned appropriate permissions, we can navigate to CloudWatch to create a Dashboard.</li> 
 <li>Navigate to CloudWatch and click on dashboards from the left side list. You can choose to create a new dashboard or add the widget in an existing dashboard.</li> 
 <li>We will choose to create a new dashboard <p></p>
  <div class="wp-caption alignnone" id="attachment_15236" style="width: 475px;">
   <img alt="Figure 7: Create New CloudWatch dashboard" class=" wp-image-15236" height="162" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/04/27/figure7-1.jpg" width="465" />
   <p class="wp-caption-text" id="caption-attachment-15236">Figure 7: Create New CloudWatch dashboard</p>
  </div></li> 
 <li>Choose Custom Widget in the Add widget page <p></p>
  <div class="wp-caption alignnone" id="attachment_15224" style="width: 793px;">
   <img alt="Figure 8: Add widget" class=" wp-image-15224" height="705" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/04/27/figure8.jpg" width="783" />
   <p class="wp-caption-text" id="caption-attachment-15224">Figure 8: Add widget</p>
  </div></li> 
 <li>Click Next in the custom widge page without choosing a sample <p></p>
  <div class="wp-caption alignnone" id="attachment_15225" style="width: 792px;">
   <img alt="Figure 9: Custom Widget Selection" class=" wp-image-15225" height="553" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/04/27/figure9.jpg" width="782" />
   <p class="wp-caption-text" id="caption-attachment-15225">Figure 9: Custom Widget Selection</p>
  </div></li> 
 <li>Choose the region where devops guru is enabled. Select the Lambda function that we created earlier. In the preview pane, click on preview to view DevOps Guru metrics. Once the preview is successful, create the Widget. <p></p>
  <div class="wp-caption alignnone" id="attachment_15226" style="width: 2996px;">
   <img alt="Figure 10: Create Custom Widget" class="size-full wp-image-15226" height="904" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/04/27/figure10.jpg" width="2986" />
   <p class="wp-caption-text" id="caption-attachment-15226">Figure 10: Create Custom Widget</p>
  </div></li> 
 <li>Congratulations, you have now successfully created a CloudWatch dashboard with a custom widget to get insights from DevOps Guru. The sample code that we provided can be customized to suit your needs.</li> 
</ol> 
<h2>Using AWS CloudFormation</h2> 
<p>You may skip this step and move to future scope section if you have already created the resources using AWS Console.</p> 
<p>In this step we will show you how to &nbsp;deploy the solution using AWS CloudFormation. AWS CloudFormation lets you model, provision, and manage AWS and third-party resources by treating infrastructure as code. Customers define an initial template and then revise it as their requirements change. For more information on CloudFormation stack creation refer to &nbsp;<a href="https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-console-create-stack.html">this</a> blog post.</p> 
<p>The following resources are created.</p> 
<ul> 
 <li>Three Lambda functions that will support CloudWatch Dashboard custom widgets</li> 
 <li>An&nbsp;<a href="http://aws.amazon.com/iam">AWS Identity and Access Management (IAM)</a> role to that allows the Lambda function to access DevOps Guru Insights and to publish logs to CloudWatch</li> 
 <li>Three Log Groups under CloudWatch</li> 
 <li>A CloudWatch dashboard with widgets to pull data from the Lambda Functions</li> 
</ul> 
<p>To deploy the solution by using the CloudFormation template</p> 
<ol> 
 <li>You can use this downloadable <a href="https://d2908q01vomqb2.cloudfront.net/artifacts/DevOpsBlog/devops-2165/DevOpsGuru-Cloudwatch.yaml">template&nbsp;</a> to set up the resources. To launch directly through the console, choose Launch Stack button, which creates the stack in the us-east-1 AWS Region.</li> 
 <li>Choose Next to go to the Specify stack details page.</li> 
 <li>(Optional) On the Configure Stack Options page, enter any tags, and then choose Next.</li> 
 <li>On the Review page, select I acknowledge that AWS CloudFormation might create IAM resources.</li> 
 <li>Choose Create stack.</li> 
</ol> 
<p>It takes approximately 2-3 minutes for the provisioning to complete. After the status is “Complete”, proceed to validate the resources as listed below.</p> 
<h2>Validate the resources</h2> 
<p>Now that the stack creation has completed successfully, you should validate the resources that were created.</p> 
<ul> 
 <li>On AWS Console, head to CloudWatch, under Dashboards – there will be a dashboard created with name &lt;StackName-Region&gt;.</li> 
 <li>On AWS Console, head to CloudWatch, under LogGroups there will be 3 new log-groups created with the name as: 
  <ul> 
   <li>lambdaProactiveLogGroup</li> 
   <li>lambdaReactiveLogGroup</li> 
   <li>lambdaSummaryLogGroup</li> 
  </ul> </li> 
 <li>On AWS Console, head to Lambda, there will be lambda function(s) under the name: 
  <ul> 
   <li>lambdaFunctionDGProactive</li> 
   <li>lambdaFunctionDGReactive</li> 
   <li>lambdaFunctionDGSummary</li> 
  </ul> </li> 
 <li>On AWS Console, head to IAM, under Roles there will be a new role created with name “lambdaIAMRole”</li> 
</ul> 
<h2>To View Results/Outcome</h2> 
<p>With the appropriate time-range setup on CloudWatch Dashboard, you will be able to navigate through the insights that have been generated from DevOps Guru on the CloudWatch Dashboard.</p> 
<div class="wp-caption alignnone" id="attachment_15227" style="width: 3004px;">
 <img alt="Figure 11: DevOpsGuru Insights in Cloudwatch Dashboard" class="size-full wp-image-15227" height="666" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/04/27/figure11.jpg" width="2994" />
 <p class="wp-caption-text" id="caption-attachment-15227">Figure 11: DevOpsGuru Insights in Cloudwatch Dashboard</p>
</div> 
<h2>Cleanup</h2> 
<p>For cost optimization, after you complete and test this solution, clean up the resources. You can delete them manually if you used the AWS Console or by deleting the AWS CloudFormation stack called devopsguru-cloudwatch-dashboard if you used AWS CloudFormation.</p> 
<p>For more information on deleting the stacks, see <a href="https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-console-delete-stack.html">Deleting a stack on the AWS CloudFormation console</a>.</p> 
<h2>Conclusion</h2> 
<p>This blog post outlined how you can integrate DevOps Guru insights into a CloudWatch Dashboard. As a customer, you can start leveraging CloudWatch Custom Widgets to include DevOps Guru Insights in an existing Operational dashboard.</p> 
<p>AWS Customers are now using Amazon DevOps Guru to monitor and improve application performance. You can start monitoring your applications by following the instructions in the product documentation. Head over to the Amazon DevOps Guru console to get started today.</p> 
<p>To learn more about AIOps for Serverless using Amazon DevOps Guru check out this <a href="https://www.youtube.com/watch?v=xR2UZ8DxyqM">video</a>.</p> 
<footer> 
 <div class="blog-author-box"> 
  <div class="blog-author-image">
   <img alt="" class="wp-image-11636 alignleft" height="160" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/04/29/blog-photo.jpg" width="120" />
  </div> 
  <h3 class="lb-h4">Suresh Babu</h3> 
  <p style="text-align: left;">Suresh Babu is a Senior DevOps Consultant at Amazon Web Services (AWS) with 21 years of experience in designing and implementing software solutions from various industries. He helps customers in Application Modernization and DevOps adoption. Suresh is a passionate public speaker and often speaks about DevOps and Artificial Intelligence (AI)</p> 
 </div> 
 <div class="blog-author-box"> 
  <div class="blog-author-image">
   <img alt="" class="wp-image-11636 alignleft" height="160" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/04/30/venkat-devarajan.jpeg" width="120" />
  </div> 
  <h3 class="lb-h4">Venkat Devarajan</h3> 
  <p style="text-align: left;">Venkat Devarajan is a Senior Solutions Architect at Amazon Webservices (AWS) supporting enterprise automotive customers. He has over 18 years of industry experience in helping customers design, build, implement and operate enterprise applications.</p> 
 </div> 
 <div class="blog-author-box"> 
  <div class="blog-author-image">
   <img alt="" class="wp-image-11636 alignleft" height="160" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/04/29/Ashwin-Bhargava-Author.jpg" width="120" />
  </div> 
  <h3 class="lb-h4">Ashwin Bhargava</h3> 
  <p style="text-align: left;">Ashwin is a DevOps Consultant at AWS working in Professional Services Canada. He is a DevOps expert and a security enthusiast with more than 15 years of development and consulting experience.</p> 
 </div> 
 <div class="blog-author-box"> 
  <div class="blog-author-image">
   <img alt="" class="wp-image-11636 alignleft" height="160" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/04/29/murty-blog.jpg" width="120" />
  </div> 
  <h3 class="lb-h4">Murty Chappidi</h3> 
  <p style="text-align: left;">Murty is an APJ Partner Solutions Architecture Lead at Amazon Web Services with a focus on helping customers with accelerated and seamless journey to AWS by providing solutions through our GSI partners. He has more than 25 years’ experience in software and technology and has worked in multiple industry verticals. He is the APJ SME for AI for DevOps Focus Area. In his free time, he enjoys gardening and cooking.</p> 
 </div> 
 <div class="blog-author-box"> 
  <div class="blog-author-image">
   <img alt="" class="wp-image-11636 alignleft" height="160" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/11/16/akanksha-blog-photo.jpg" width="120" />
  </div> 
  <h3 class="lb-h4">Akanksha Bansal</h3> 
  <p style="text-align: left;">Akanksha Bansal is a Senior User Experience Designer at AWS and is based out in Seattle, WA. She has around 15 years of experience of designing customer-centric products. Currently, she is focused on driving user experience design for DevOps Guru. Prior to joining AWS, she led product design for data and governance products at Azure. Outside of work, Akanksha loves to spend time cooking new cuisines, Bollywood dancing and doing art projects.</p> 
 </div> 
</footer>
