---
title: "Fine-tuning Operations at Slice using AWS DevOps Guru"
url: "https://aws.amazon.com/blogs/devops/fine-tuning-operations-at-slice-using-aws-devops-guru/"
date: "Wed, 12 Oct 2022 13:46:33 +0000"
author: "Adnan Bilwani"
feed_url: "https://aws.amazon.com/blogs/devops/category/artificial-intelligence/amazon-devops-guru/feed/"
---
<p><em>This guest post was authored by Sapan Jain,&nbsp;DevOps Engineer at Slice, and edited by Sobhan Archakam and Adnan Bilwani, at AWS.</em></p> 
<p><a href="https://slicelife.com/">Slice</a> empowers over 18,000 independent pizzerias with the modern tools that have grown the major restaurant chains. By uniting these small businesses with specialized technology, marketing, data insights, and shared services, Slice enables them to serve their digitally-minded customers and move away from third-party apps. Using Amazon DevOps Guru, Slice is able to fine-tune their operations to better support these customers.</p> 
<p>Serial tech entrepreneur Ilir Sela started Slice to modernize and support his family’s New York City pizzerias. Today, the company partners with restaurants in 3,000 cities and all 50 states, forming the nation’s largest pizza network. For more information, visit <a href="http://slicelife.com/">slicelife.com</a>.</p> 
<h2>Slice’s challenge</h2> 
<p>At Slice, we manage a wide variety of systems, services, and platforms, all with varying levels of complexity. Observability, monitoring, and log aggregation are things we excel at, and they’re always critical for our platform engineering team. However, deriving insights from this data still requires some manual investigation, particularly when dealing with operational anomalies and/or misconfigurations.</p> 
<p>To gain automated insights into our services and resources, Slice conducted a proof-of-concept utilizing <a href="https://aws.amazon.com/devops-guru/">Amazon DevOps Guru</a> to analyze a small selection of AWS resources. Amazon DevOps Guru identified potential issues in our environment, resulting in actionable insights (ultimately leading to remediation). As a result of this analysis, we enabled Amazon DevOps Guru account-wide, thereby leading to numerous insights into our production environment.</p> 
<h2>Insights with Amazon DevOps Guru</h2> 
<p>After we configured Amazon DevOps Guru to begin its account-wide analysis, we left the tool alone to begin the process of collecting and analyzing data. We immediately began seeing some actionable insights for various production AWS resources, some of which are highlighted in the following section:</p> 
<h2>Amazon DynamoDB Point-in-time recovery</h2> 
<p><a href="https://aws.amazon.com/dynamodb">Amazon DynamoDB</a> offers a point-in-time recovery (PITR) feature that provides continuous backups of your DynamoDB data for 35 days to help you protect against accidental write or deletes. If enabled, this lets you restore your respective table to a previous state. Amazon DevOps Guru identified several tables in our environment that had PITR disabled, along with a corresponding Recommendation.</p> 
<p><img alt="The graphic shows proactive insights for the last 1 month. The one insight shown is 'Dynamo Table Point in Time Recovery not enabled' with a status of OnGoing and a severity of low." class="aligncenter wp-image-13558" height="158" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2022/10/05/deveops_2154_1.png" width="700" /></p> 
<div class="wp-caption aligncenter" id="attachment_13559" style="width: 710px;">
 <img alt="The graphic shows proactive insights for the last 1 month. The one insight shown is 'Dynamo Table Point in Time Recovery not enabled' with a status of OnGoing and a severity of low." class="wp-image-13559" height="88" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2022/10/05/deveops_2154_2.png" width="700" />
 <p class="wp-caption-text" id="caption-attachment-13559">Figure 1. The graphic shows proactive insights for the last 1 month. The one insight shown is ‘Dynamo Table Point in Time Recovery not enabled’ with a status of OnGoing and a severity of low.</p>
</div> 
<h2>Elasticache anomalous evictions</h2> 
<p><a href="https://aws.amazon.com/elasticache/redis/">Amazon Elasticache for Redis</a> is used by a handful of our services to cache any relevant application data. Amazon DevOps Guru identified that one of our instances was exhibiting anomalous behavior regarding its cache eviction rate. Essentially, due to the memory pressure of the instance, the eviction rate of cache entries began to increase. DevOps Guru recommended revisiting the sizing of this instance and scaling it vertically or horizontally, where appropriate.</p> 
<p><img alt="The graph shows the metric: count of ElastiCache evictions plotted for the time period Jul 3, 20:35 to Jul 3, 21:35 UTC. A highlighted section shows that the evictions increased to a peak of 2500 between 21:00 and 21:08. Outside of this interval the evictions are below 500. " class="aligncenter wp-image-13560" height="153" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2022/10/05/deveops_2154_4.png" width="700" /></p> 
<div class="wp-caption aligncenter" id="attachment_13561" style="width: 710px;">
 <img alt="The graph shows the metric: count of ElastiCache evictions plotted for the time period Jul 3, 20:35 to Jul 3, 21:35 UTC. A highlighted section shows that the evictions increased to a peak of 2500 between 21:00 and 21:08. Outside of this interval the evictions are below 500. " class="wp-image-13561" height="444" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2022/10/05/deveops_2154_5.png" width="700" />
 <p class="wp-caption-text" id="caption-attachment-13561">Figure 2. The graph shows the metric: count of ElastiCache evictions plotted for the time period Jul 3, 20:35 to Jul 3, 21:35 UTC. A highlighted section shows that the evictions increased to a peak of 2500 between 21:00 and 21:08. Outside of this interval the evictions are below 500</p>
</div> 
<h2>AWS Lambda anomalous errors</h2> 
<p>We manage a few <a href="https://aws.amazon.com/lambda/">AWS Lambda</a> functions that all serve different purposes. During the beginning of normal work day, we began to see increased error rates for a particular function resulting in an exception being thrown. DevOps Guru was able to detect the increase in error rates and flag them as anomalous. Although retries in this case wouldn’t have solved the problem, it did increase our visibility into the issue (which was also corroborated by our APM platform).</p> 
<p><img alt="The graph shows the metric: count of AWS/Lambda errors plotted between 11:00 and 13:30 on Jul 6. The sections between the times 11:23 and 12:15 and at 12:37 and 13:13 UTC are highlighted to show the anomalies." class="wp-image-13562 aligncenter" height="364" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2022/10/05/deveops_2154_7.png" width="700" /></p> 
<div class="wp-caption aligncenter" id="attachment_13563" style="width: 1523px;">
 <img alt="Figure 3. The graph shows the metric: count of AWS/Lambda errors plotted between 11:00 and 13:30 on Jul 6. The sections between the times 11:23 and 12:15 and at 12:37 and 13:13 UTC are highlighted to show the anomalies" class="size-full wp-image-13563" height="106" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2022/10/05/deveops_2154_8.png" width="1513" />
 <p class="wp-caption-text" id="caption-attachment-13563">Figure 3. The graph shows the metric: count of AWS/Lambda errors plotted between 11:00 and 13:30 on Jul 6. The sections between the times 11:23 and 12:15 UTC are highlighted to show the anomalies</p>
</div> 
<h2>Conclusion</h2> 
<p>Amazon DevOps Guru integrated into our environment quickly, with no more additional configuration or setup aside from a few button clicks to enable the service. After reviewing several of the proactive insights that DevOps Guru provided, we could formulate plans of action regarding remediation. One specific case example of this is where DevOps Guru flagged several of our Lambda functions for not containing enough subnets. After triaging the finding, we discovered that we were lacking multi-AZ redundancy for several of those functions. As a result, we could implement a change that maximized our availability of those resources.</p> 
<p>With the continuous analysis that DevOps Guru performs, we continue to gain new insights into the resources that we utilize and deploy in our environment. This lets us improve operationally while simultaneously maintaining production stability.</p> 
<p><strong>About the author:</strong></p> 
<footer> 
 <div class="blog-author-box"> 
  <div class="blog-author-image">
   <img alt="" class="aligncenter size-full wp-image-11636" height="160" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2022/10/05/abilwani.jpg" width="120" />
  </div> 
  <h3 class="lb-h4">Adnan Bilwani</h3> 
  <p>Adnan Bilwani is a Sr. Specialist Builders Experience at AWS and part of the AI for DevOps portfolio of services providing fully managed ML-based solutions to enhance your DevOps workflows.</p> 
 </div> 
</footer> 
<footer> 
 <div class="blog-author-box"> 
  <div class="blog-author-image">
   <img alt="" class="aligncenter wp-image-11636" height="160" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2022/10/05/Archakam.jpg" width="120" />
  </div> 
  <h3 class="lb-h4">Sobhan Archakam</h3> 
  <p>Sobhan Archakam is a Senior Technical Account Manager at Amazon Web Services. He provides advocacy and guidance to Enterprise Customers to plan, build, deploy and operate solutions at scale using best practices.</p> 
 </div> 
</footer> 
<footer> 
 <div class="blog-author-box"> 
  <div class="blog-author-image">
   <img alt="" class="aligncenter wp-image-11636" height="459" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2022/10/10/Sapan-Jain.jpg" width="438" />
  </div> 
  <h3 class="lb-h4">Sapan Jain</h3> 
  <p>Sapan Jain is a DevOps Engineer at Slice. He provides support in all facets of DevOps, and has an interest in performance, observability, automation, and troubleshooting.</p> 
 </div> 
</footer>
