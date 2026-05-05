---
title: "Deliver Operational Insights to Atlassian Opsgenie using DevOps Guru"
url: "https://aws.amazon.com/blogs/devops/deliver-operational-insights-to-atlassian-opsgenie-using-devops-guru/"
date: "Fri, 27 Jan 2023 19:44:56 +0000"
author: "Brendan Jenkins"
feed_url: "https://aws.amazon.com/blogs/devops/category/artificial-intelligence/amazon-devops-guru/feed/"
---
<p>As organizations continue to grow and scale their applications, the need for teams to be able to quickly and autonomously detect anomalous operational behaviors becomes increasingly important. <a href="https://aws.amazon.com/devops-guru/">Amazon DevOps Guru</a> offers a fully managed AIOps service that enables you to improve application availability and resolve operational issues quickly. DevOps Guru helps ease this process by leveraging machine learning (ML) powered recommendations to detect operational insights, identify the exhaustion of resources, and provide suggestions to remediate issues. Many organizations running business critical applications use different tools to be notified about anomalous events in real-time for the remediation of critical issues. <a href="https://partners.amazonaws.com/partners/001E000000UfZWPIA3/Atlassian">Atlassian</a> is a modern team collaboration and productivity software suite that&nbsp;helps teams organize, discuss, and complete shared work. You can deliver these insights in near-real time to DevOps teams by integrating DevOps Guru with Atlassian Opsgenie. Opsgenie is a modern incident management platform that receives alerts from your monitoring systems and custom applications and categorizes each alert based on importance and timing.</p> 
<p>This blog post walks you through how to integrate Amazon DevOps Guru with Atlassian Opsgenie to<br /> receive notifications for new operational insights detected by DevOps Guru with more flexibility and customization using&nbsp;<a href="https://aws.amazon.com/eventbridge/">Amazon EventBridge</a>&nbsp;and&nbsp;<a href="https://aws.amazon.com/lambda/">AWS Lambda</a>. The Lambda function will be used to demonstrate how to customize insights sent to Opsgenie.</p> 
<h2>Solution overview</h2> 
<div class="wp-caption aligncenter" id="attachment_14426" style="width: 710px;">
 <img alt="Figure 1: Amazon EventBridge Integration with Opsgenie using AWS Lambda" class="wp-image-14426" height="271" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/01/27/devops_2218_1.png" width="700" />
 <p class="wp-caption-text" id="caption-attachment-14426">Figure 1: Amazon EventBridge Integration with Opsgenie using AWS Lambda</p>
</div> 
<p>Amazon DevOps Guru directly integrates with <a href="https://aws.amazon.com/eventbridge/">Amazon EventBridge</a> to notify you of events relating to generated insights and updates to insights. To begin routing these notifications to Opsgenie, you can configure routing rules to determine where to send notifications. As outlined below, you can also use pre-defined DevOps Guru patterns to only send notifications or trigger actions that match that pattern. You can select any of the following pre-defined patterns to filter events to trigger actions in a supported AWS resource. Here are the following <a href="https://docs.aws.amazon.com/devops-guru/latest/userguide/working-with-eventbridge.html">predefined patterns supported by DevOps Guru</a>:</p> 
<ul> 
 <li>DevOps Guru New Insight Open</li> 
 <li>DevOps Guru New Anomaly Association</li> 
 <li>DevOps Guru Insight Severity Upgraded</li> 
 <li>DevOps Guru New Recommendation Created</li> 
 <li>DevOps Guru Insight Closed</li> 
</ul> 
<p>By default, the patterns referenced above are enabled so we will leave all patterns operational in this implementation.&nbsp; However, you do have flexibility to change which of these patterns to choose to send to Opsgenie. When EventBridge receives an event, the EventBridge rule&nbsp;matches incoming events and sends it to a target, such as AWS Lambda, to process and send the insight to Opsgenie.</p> 
<h2>Prerequisites</h2> 
<p>The following prerequisites are required for this walkthrough:</p> 
<ul> 
 <li>An <a href="https://aws.amazon.com/resources/create-account/">AWS Account</a></li> 
 <li>An <a href="https://www.atlassian.com/software/opsgenie">Opsgenie Account</a></li> 
 <li>Maven</li> 
 <li><a href="https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html%22 %5Ct %22_blank">AWS Command Line Interface (CLI)</a></li> 
 <li><a href="https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam.html%22 %5Ct %22_blank">AWS Serverless Application Model (SAM) CLI</a></li> 
 <li><a href="https://support.atlassian.com/opsgenie/docs/create-a-team-and-add-members/">Create a team and add members</a> within your Opsgenie Account</li> 
 <li><a href="https://aws.amazon.com/cloud9/">AWS Cloud9</a> is recommended to create an environment to get access to the <a href="https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam.html">AWS Serverless Application Model (SAM) CLI</a> or <a href="https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html">AWS Command Line Interface (CLI)</a> from a bash terminal.</li> 
</ul> 
<h2>Push Insights using Amazon EventBridge &amp; AWS Lambda</h2> 
<p>In this tutorial, you will perform the following steps:</p> 
<ol> 
 <li>Create an <a href="https://www.atlassian.com/software/opsgenie">Opsgenie</a> integration</li> 
 <li>Launch the SAM template to deploy the solution</li> 
 <li>Test the solution</li> 
</ol> 
<h2>Create an Opsgenie integration</h2> 
<p>In this step, you will navigate to <a href="https://www.atlassian.com/software/opsgenie">Opsgenie</a> to create the integration with DevOps Guru and to obtain the API key and team name within your account. These parameters will be used as inputs in a later section of this blog.</p> 
<ol> 
 <li>Navigate to <strong>Teams</strong>, and take note of the <strong>team name</strong> you have as shown below, as you will need this parameter in a later section.</li> 
</ol> 
<div class="wp-caption aligncenter" id="attachment_14427" style="width: 710px;">
 <img alt="Figure 2: Opsgenie team names" class="wp-image-14427" height="197" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/01/27/devops_2218_2.png" width="700" />
 <p class="wp-caption-text" id="caption-attachment-14427">Figure 2: Opsgenie team names</p>
</div> 
<ol start="2"> 
 <li>Click on the team to proceed and navigate to <strong>Integrations</strong> on the left-hand pane. Click on Add Integration and select the <strong>Amazon DevOps Guru</strong> option.</li> 
</ol> 
<div class="wp-caption aligncenter" id="attachment_14428" style="width: 710px;">
 <img alt="Figure 3: Integration option for DevOps Guru" class="wp-image-14428" height="272" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/01/27/devops_2218_3.png" width="700" />
 <p class="wp-caption-text" id="caption-attachment-14428">Figure 3: Integration option for DevOps Guru</p>
</div> 
<ol start="3"> 
 <li>Now, scroll down and take note of the <strong>API Key</strong> for this integration and copy it to your notes as it will be needed in a later section. Click <strong>Save Integration</strong> at the bottom of the page to proceed.</li> 
</ol> 
<p>­­­</p> 
<div class="wp-caption aligncenter" id="attachment_14429" style="width: 710px;">
 <img alt=" Figure 4: API Key for DevOps Guru Integration" class="wp-image-14429" height="105" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/01/27/devops_2218_4.png" width="700" />
 <p class="wp-caption-text" id="caption-attachment-14429">Figure 4: API Key for DevOps Guru Integration</p>
</div> 
<ol start="4"> 
 <li>Now, the Opsgenie integration has been created and we’ve obtained the API key and team name. The email of any team member will be used in the next section as well.</li> 
</ol> 
<h2>Review &amp; launch the AWS SAM template to deploy the solution</h2> 
<p>In this step, you will review &amp; launch the SAM template. The template will deploy an AWS Lambda function that is triggered by an Amazon EventBridge rule when Amazon DevOps Guru generates a new event. The Lambda function will retrieve the parameters obtained from the deployment and pushes the events to Opsgenie via an API.</p> 
<h2>Reviewing the template</h2> 
<p>Below is the SAM template that will be deployed in the next step. This template launches a few key components specified earlier in the blog. The Transform section of the template allows us takes an entire template written in the AWS Serverless Application Model (AWS SAM) syntax and transforms and expands it into a compliant CloudFormation template. Under the Resources section this solution will deploy an AWS Lamba function using the Java runtime as well as an Amazon EventBridge Rule/Pattern. Another key aspect of the template are the Parameters. As shown below, the ApiKey, Email, and TeamName are parameters we will use for this CloudFormation template which will then be used as environment variables for our Lambda function to pass to OpsGenie.</p> 
<div class="wp-caption aligncenter" id="attachment_14430" style="width: 710px;">
 <img alt="Figure 5: Review of SAM Template" class="wp-image-14430" height="574" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/01/27/devops_2218_5.png" width="700" />
 <p class="wp-caption-text" id="caption-attachment-14430">Figure 5: Review of SAM Template</p>
</div> 
<h2>Launching the Template</h2> 
<ol> 
 <li>Navigate to the directory of choice within a terminal and clone the GitHub repository with the following command:</li> 
</ol> 
<div class="hide-language"> 
 <pre class="unlimited-height-code" style="padding-left: 40px;"><code class="lang-bash">git clone <a href="https://github.com/aws-samples/amazon-devops-guru-connector-opsgenie.git">https://github.com/aws-samples/amazon-devops-guru-connector-opsgenie.git</a></code></pre> 
</div> 
<ol start="2"> 
 <li>Change directories with the command below to navigate to the directory of the SAM template.</li> 
</ol> 
<div class="hide-language"> 
 <pre class="unlimited-height-code" style="padding-left: 40px;"><code class="lang-bash">cd amazon-devops-guru-connector-opsgenie/OpsGenieServerlessTemplate</code></pre> 
</div> 
<ol start="3"> 
 <li>From the CLI, use the AWS SAM to build and process your AWS SAM template file, application code, and any applicable language-specific files and dependencies.</li> 
</ol> 
<div class="hide-language"> 
 <pre class="unlimited-height-code" style="padding-left: 40px;"><code class="lang-bash">sam build</code></pre> 
</div> 
<ol start="4"> 
 <li>From the CLI, use the AWS SAM to deploy the AWS resources for the pattern as specified in the template.yml file.</li> 
</ol> 
<div class="hide-language"> 
 <pre class="unlimited-height-code" style="padding-left: 40px;"><code class="lang-bash">sam deploy --guided</code></pre> 
</div> 
<ol start="5"> 
 <li>You will now be prompted to enter the following information below. Use the information obtained from the previous section to enter the Parameter ApiKey, Parameter Email, and Parameter TeamName fields.</li> 
</ol> 
<ul> 
 <li>&nbsp;Stack Name</li> 
 <li>AWS Region</li> 
 <li>Parameter ApiKey</li> 
 <li>Parameter Email</li> 
 <li>Parameter TeamName</li> 
 <li>Allow SAM CLI IAM Role Creation</li> 
</ul> 
<h2>Test the solution</h2> 
<ol> 
 <li>Follow <a href="https://aws.amazon.com/blogs/devops/gaining-operational-insights-with-aiops-using-amazon-devops-guru/">this blog</a> to enable DevOps Guru and generate an operational insight.</li> 
 <li>When DevOps Guru detects a new insight, it will generate an event in EventBridge. EventBridge then triggers Lambda and sends the event to Opsgenie as shown below.</li> 
</ol> 
<div class="wp-caption aligncenter" id="attachment_14431" style="width: 710px;">
 <img alt="Figure 6: Event Published to Opsgenie with details such as the source, alert type, insight type, and a URL to the insight in the AWS console." class="wp-image-14431" height="397" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/01/27/devops_2218_6.png" width="700" />
 <p class="wp-caption-text" id="caption-attachment-14431">Figure 6: Event Published to Opsgenie with details such as the source, alert type, insight type, and a URL to the insight in the AWS console.</p>
</div> 
<h2>Cleaning up</h2> 
<p>To avoid incurring future charges, delete the resources.</p> 
<ol> 
 <li>Delete resources deployed from <a href="https://aws.amazon.com/blogs/devops/gaining-operational-insights-with-aiops-using-amazon-devops-guru/">this blog</a>.</li> 
 <li>From the command line, use AWS SAM to delete the serverless application along with its dependencies.</li> 
</ol> 
<div class="hide-language"> 
 <pre class="unlimited-height-code" style="padding-left: 40px;"><code class="lang-bash">sam delete</code></pre> 
</div> 
<h2>Customizing Insights published using Amazon EventBridge &amp; AWS Lambda</h2> 
<p>The foundation of the DevOps Guru and Opsgenie integration is based on Amazon EventBridge and AWS Lambda which allows you the flexibility to implement several customizations. An example of this would be the ability to generate an Opsgenie alert when a DevOps Guru insight severity is high. Another example would be the ability to forward appropriate notifications to the AIOps team when there is a serverless-related resource issue or forwarding a database-related resource issue to your DBA team. This section will walk you through how these customizations can be done.</p> 
<h2>EventBridge customization</h2> 
<p>EventBridge rules can be used to select specific events by using event patterns. As detailed below, you can trigger the lambda function only if a new insight is opened and the severity is high. The advantage of this kind of customization is that the Lambda function will only be invoked when needed.</p> 
<div class="hide-language"> 
 <pre class="unlimited-height-code"><code class="lang-bash">{
  "source": [
    "aws.devops-guru"
  ],
  "detail-type": [
    "DevOps Guru New Insight Open"
  ],
  "detail": {
    "insightSeverity": [
         "high"
         ]
  }
}</code></pre> 
</div> 
<h2>Applying EventBridge customization</h2> 
<ol> 
 <li>Open the file template.yaml reviewed in the previous section and implement the changes as highlighted below under the Events section within resources (original file on the left, changes on the right hand side).</li> 
</ol> 
<div class="wp-caption aligncenter" id="attachment_14432" style="width: 710px;">
 <img alt="Figure 7: CloudFormation template file changed so that the EventBridge rule is only triggered when the alert type is &quot;DevOps Guru New Insight Open&quot; and insightSeverity is “high”." class="wp-image-14432" height="402" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/01/27/devops_2218_7.png" width="700" />
 <p class="wp-caption-text" id="caption-attachment-14432">Figure 7: CloudFormation template file changed so that the EventBridge rule is only triggered when the alert type is “DevOps Guru New Insight Open” and insightSeverity is “high”.</p>
</div> 
<ol start="2"> 
 <li>Save the changes and use the following command to apply the changes</li> 
</ol> 
<div class="hide-language"> 
 <pre class="unlimited-height-code" style="padding-left: 40px;"><code class="lang-bash">sam deploy --template-file template.yaml</code></pre> 
</div> 
<ol start="3"> 
 <li>Accept the changeset deployment</li> 
</ol> 
<h2>Determining the Ops team based on the resource type</h2> 
<p>Another customization would be to change the Lambda code to route and control how alerts will be managed. &nbsp;Let’s say you want to get your DBA team involved whenever DevOps Guru raises an insight related to an <a href="https://aws.amazon.com/rds/">Amazon RDS</a> resource. You can change the AlertType Java class as follows:</p> 
<ol> 
 <li>To begin this customization of the Lambda code, the following changes need to be made within the AlertType.java file:</li> 
</ol> 
<ul> 
 <li>At the beginning of the file, the standard java.util.List and java.util.ArrayList packages were imported</li> 
 <li>Line 60: created a list of CloudWatch metrics namespaces</li> 
 <li>Line 74: Assigned the dataIdentifiers JsonNode to the variable dataIdentifiersNode</li> 
 <li>Line 75: Assigned the namespace JsonNode to a variable namespaceNode</li> 
 <li>Line 77: Added the namespace to the list for each DevOps Insight which is always raised as an EventBridge event with the structure detail►anomalies►0►sourceDetails►0►dataIdentifiers►namespace</li> 
 <li>Line 88: Assigned the default responder team to the variable defaultResponderTeam</li> 
 <li>Line 89: Created the list of responders and assigned it to the variable respondersTeam</li> 
 <li>Line 92: Check if there is at least one AWS/RDS namespace</li> 
 <li>Line 93: Assigned the DBAOps_Team to the variable dbaopsTeam</li> 
 <li>Line 93: Included the DBAOps_Team team as part of the responders list</li> 
 <li>Line 97: Set the OpsGenie request teams to be the responders list</li> 
</ul> 
<div class="wp-caption aligncenter" id="attachment_14434" style="width: 710px;">
 <img alt="Figure 8: java.util.List and java.util.ArrayList packages were imported" class="wp-image-14434" height="257" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/01/27/devops_2218_8.png" width="700" />
 <p class="wp-caption-text" id="caption-attachment-14434">Figure 8: java.util.List and java.util.ArrayList packages were imported</p>
</div> 
<div class="wp-caption aligncenter" id="attachment_14436" style="width: 710px;">
 <img alt="Figure 9: AlertType Java class customized to include DBAOps_Team for RDS-related DevOps Guru insights." class="wp-image-14436" height="402" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/01/27/devops_2218_9.png" width="700" />
 <p class="wp-caption-text" id="caption-attachment-14436">Figure 9: AlertType Java class customized to include DBAOps_Team for RDS-related DevOps Guru insights.</p>
</div> 
<ol start="2"> 
 <li>You then need to generate the jar file by using the mvn clean package command.</li> 
</ol> 
<ul> 
 <li>The function needs to be updated with: 
  <ul> 
   <li>FUNCTION_NAME=$(aws lambda<br /> list-functions –query ‘Functions[?contains(FunctionName, `DevOps-Guru`) ==<br /> `true`].FunctionName’ –output text)</li> 
   <li>aws lambda update-function-code –region<br /> us-east-1 –function-name $FUNCTION_NAME –zip-file fileb://target/Functions-1.0.jar</li> 
  </ul> </li> 
</ul> 
<ol start="3"> 
 <li>As result, the DBAOps_Team will be assigned to the Opsgenie alert in the case a DevOps Guru Insight is related to RDS.</li> 
</ol> 
<div class="wp-caption aligncenter" id="attachment_14437" style="width: 710px;">
 <img alt="Figure 10: Opsgenie alert assigned to both DBAOps_Team and AIOps_Team." class="wp-image-14437" height="480" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/01/27/devops_2218_10.png" width="700" />
 <p class="wp-caption-text" id="caption-attachment-14437">Figure 10: Opsgenie alert assigned to both DBAOps_Team and AIOps_Team.</p>
</div> 
<h2>Conclusion</h2> 
<p>In this post, you learned how Amazon DevOps Guru integrates with Amazon EventBridge and publishes insights to Opsgenie using AWS Lambda. By creating an Opsgenie integration with DevOps Guru, you can now leverage Opsgenie strengths, incident management, team communication, and collaboration when responding to an insight. All of the insight data can be viewed and addressed in Opsgenie’s Incident Command Center (ICC). &nbsp;By customizing the data sent to Opsgenie via Lambda, you can empower your organization even more by fine tuning and displaying the most relevant data thus decreasing the MTTR (mean time to resolve) of the responding operations team.</p> 
<p><strong>About the authors:</strong></p> 
<footer> 
 <div class="blog-author-box"> 
  <div class="blog-author-image">
   <img alt="" class="aligncenter size-full wp-image-11636" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/01/27/Brendan-Jenkins.png" />
  </div> 
  <h3 class="lb-h4">Brendan Jenkins</h3> 
  <p>Brendan Jenkins is a solutions architect working with Enterprise AWS customers providing them with technical guidance and helping achieve their business goals. He has an area of interest around DevOps and Machine Learning technology. He enjoys building solutions for customers whenever he can in his spare time.</p> 
 </div> 
</footer> 
<footer> 
 <div class="blog-author-box"> 
  <div class="blog-author-image">
   <img alt="" class="aligncenter wp-image-11636" height="326" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/01/27/Pablo-Silva.png" width="236" />
  </div> 
  <h3 class="lb-h4">Pablo Silva</h3> 
  <p>Pablo Silva is a Sr. DevOps consultant that guide customers in their decisions on technology strategy, business model, operating model, technical architecture, and investments.</p> 
  <p>He holds a master’s degree in Artificial Intelligence and has more than 10 years of experience with telecommunication and financial companies.</p> 
 </div> 
</footer> 
<footer> 
 <div class="blog-author-box"> 
  <div class="blog-author-image">
   <img alt="" class="aligncenter wp-image-11636" height="326" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/01/27/joseph.png" width="236" />
  </div> 
  <h3 class="lb-h4">Joseph Simon</h3> 
  <p>Joseph Simon is a solutions architect working with mid to large Enterprise AWS customers. He has been in technology for 13 years with 5 of those centered around DevOps. He has a passion for Cloud, DevOps and Automation and in his spare time, likes to travel and spend time with his family.</p> 
 </div> 
</footer>
