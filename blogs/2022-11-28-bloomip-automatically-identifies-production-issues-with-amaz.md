---
title: "BloomIP Automatically Identifies production issues with Amazon DevOps Guru"
url: "https://aws.amazon.com/blogs/devops/bloomip-automatically-identifies-production-issues-with-amazon-devops-guru/"
date: "Mon, 28 Nov 2022 18:41:50 +0000"
author: "David Ernst"
feed_url: "https://aws.amazon.com/blogs/devops/category/artificial-intelligence/amazon-devops-guru/feed/"
---
<p>Operational excellence is critical for <a href="https://partners.amazonaws.com/partners/001E000000pbTJFIA2/Bloomip,%20Inc.">BloomIP’s</a> customers. In this post, you will see how we built a solution to automate the detection of trends and issues in production workloads by implementing <a href="https://aws.amazon.com/devops-guru/">Amazon DevOps Guru</a> for our clients.</p> 
<p><a href="https://partners.amazonaws.com/partners/001E000000pbTJFIA2/Bloomip, Inc.">BloomIP</a> ensures your business is ready for what’s ahead, with security, scalability, performance, and cost control. We are cloud solutions partner that gets to know both the people and processes in your business.</p> 
<h2>The Challenge</h2> 
<p>Identifying operational issues within applications and services is time-consuming. This requires developers and cloud engineers to spend valuable time manually debugging using multiple tools. We needed to quickly identify any operational issues related to our clients applications, including any load balancer errors or user delays in accessing their application. Ensuring the application is up and running during certain times of the day is crucial to the success of our client’s business. We needed to identify any downtime or performance patterns and quickly address any related issues.</p> 
<p>Analyzing an AWS environment after any incident requires a combination of tools such as <a href="https://aws.amazon.com/cloudwatch/">Amazon CloudWatch</a>, <a href="https://aws.amazon.com/config/">AWS Config</a>, <a href="https://aws.amazon.com/cloudtrail/">AWS CloudTrail</a>, <a href="https://aws.amazon.com/cloudformation/">AWS CloudFormation</a>, and <a href="https://aws.amazon.com/xray/">AWS X-Ray</a>. We spend hours pouring over the information in each tool to try to identify patterns and troubleshooting steps. Still, identifying issues that correlate between those tools is a manual process.</p> 
<h2>Automating Identification of Operational Issues</h2> 
<p>To address the challenges of tedious and manual processes of analyzing different tools to identify patterns, we implemented Amazon DevOps Guru&nbsp; for many of our clients. <a href="https://aws.amazon.com/devops-guru/">Amazon DevOps Guru</a> helps us automatically ingests all related data from the services mentioned above and applies Machine Learning techniques to analyze and recommend fixes for abnormal behaviors. Amazon DevOps Guru organizes its findings into reactive and proactive insights.</p> 
<p>We capture Amazon DevOps Guru Insights as events using Amazon EventBridg, and send them to an &nbsp;Amazon SNS Topic, which then notifies us via email and Slack.</p> 
<div class="wp-caption aligncenter" id="attachment_13681" style="width: 710px;">
 <img alt="Architecture diagram showing a typical 3 tier web app using AWS services and integrating the application with Amazon DevOps Guru, Amazon Eventbridge and Amazon SNS Topic to send send notifications via Email and Slack" class="wp-image-13681" height="277" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2022/10/28/devops-2121_1.png" width="700" />
 <p class="wp-caption-text" id="caption-attachment-13681">Figure 1. Architecture diagram</p>
</div> 
<h2>Results</h2> 
<p>BloomIP is leveraging DevOps Guru to scale its operations across multiple customers. Amazon DevOps Guru was easy to enable; it provides us with a single console experience to search and visualize operational data. In addition to detecting anomalies, we can see graphs and timelines related to the numerous anomalous metrics and more contextual information such as relevant events and log snippets. This helps us quickly understand the anomaly scope. Because it integrates data across multiple sources such as <a href="https://aws.amazon.com/cloudwatch/">Amazon CloudWatch</a>, <a href="https://aws.amazon.com/config/">AWS Config</a>,<a href="https://aws.amazon.com/cloudtrail/"> AWS CloudTrail</a>, <a href="https://aws.amazon.com/cloudformation/">AWS CloudFormation</a>, and <a href="https://aws.amazon.com/xray/">AWS X-Ray</a>, Amazon DevOps Guru reduces the need for us to use numerous tools.</p> 
<blockquote>
 <p>“We were looking at a way to effortlessly scale our observability needs across multiple clients while ensuring we had the proper coverage. DevOps Guru gives us additional insight and assurance by quickly pointing out anomalies in our client’s environments. With ML-powered recommendations, DevOps Guru has allowed us to remediate repeated production issues automatically. ” – <strong>Joshua Haynes, Director of Engineering, BloomIP</strong></p>
</blockquote> 
<h2>Conclusion</h2> 
<p>Amazon DevOps Guru provides <em><strong>BloomIP</strong> </em>with a streamlined approach to visualize operational data by integrating data across multiple sources supporting <a href="https://aws.amazon.com/cloudwatch/">Amazon CloudWatch</a>, <a href="https://aws.amazon.com/config/">AWS Config</a>, <a href="https://aws.amazon.com/cloudtrail/">AWS CloudTrail</a>, <a href="https://aws.amazon.com/cloudformation/">AWS CloudFormation</a>, and <a href="https://aws.amazon.com/xray/">AWS X-Ray</a> and reduces the need to use multiple tools. DevOps Guru gives you a single-console dashboard to look for and visualize anomalies in your operational data.</p> 
<p>Start monitoring your AWS applications with AWS DevOps Guru today using this&nbsp;<a href="https://aws.amazon.com/devops-guru/">link</a></p> 
<p><strong>About the authors:</strong></p> 
<footer> 
 <div class="blog-author-box"> 
  <div class="blog-author-image">
   <img alt="" class="aligncenter size-full wp-image-11636" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2022/10/28/david.png" />
  </div> 
  <h3 class="lb-h4">David Ernst</h3> 
  <p>David is a Sr. Specialist Solution Architect – DevOps, with 20+ years of experience in designing and implementing software solutions for various industries. David is an automation enthusiast and works with AWS customers to design, deploy, and manage their AWS workloads/architectures.</p> 
 </div> 
</footer> 
<footer> 
 <div class="blog-author-box"> 
  <div class="blog-author-image">
   <img alt="" class="aligncenter wp-image-11636" height="160" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2022/10/28/Abdullahi-Olaoye.jpg" width="120" />
  </div> 
  <h3 class="lb-h4">Abdullahi Olaoye</h3> 
  <p>Abdullahi is a Senior Cloud Architect at AWS Professional Services where he works with customers of different scales to design and build IT solutions that solve business challenges. When he’s not working, he enjoys spending time with his family, traveling and learning history of different varieties through documentaries and podcasts.</p> 
 </div> 
</footer>
