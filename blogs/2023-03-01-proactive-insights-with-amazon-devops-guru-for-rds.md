---
title: "Proactive Insights with Amazon DevOps Guru for RDS"
url: "https://aws.amazon.com/blogs/devops/proactive-insights-with-amazon-devops-guru-for-rds/"
date: "Wed, 01 Mar 2023 18:33:26 +0000"
author: "Kishore Dhamodaran"
feed_url: "https://aws.amazon.com/blogs/devops/category/artificial-intelligence/amazon-devops-guru/feed/"
---
<p>Today, we are pleased to announce a new <a href="https://aws.amazon.com/devops-guru/features/devops-guru-for-rds/" rel="noopener" target="_blank">Amazon DevOps Guru for RDS</a> capability: <a href="https://docs.aws.amazon.com/devops-guru/latest/userguide/working-with-insights.html" rel="noopener" target="_blank">Proactive Insights</a>. DevOps Guru for RDS is a fully-managed service powered by machine learning (ML), that uses the data collected by <a href="https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/USER_PerfInsights.Overview.html" rel="noopener" target="_blank">RDS Performance Insights</a> to detect and alert customers of anomalous behaviors within Amazon Aurora databases. Since its release, DevOps Guru for RDS has empowered customers with information to quickly react to performance problems and to take corrective actions. Now, Proactive Insights adds recommendations related to operational issues that may prevent potential issues in the future.</p> 
<p>Proactive Insights requires no additional set up for customers already using DevOps Guru for RDS, for both Amazon Aurora MySQL-Compatible Edition and Amazon Aurora PostgreSQL-Compatible Edition.</p> 
<p>The following are example use cases of operational issues available for Proactive Insights today, with more insights coming over time:</p> 
<ul> 
 <li><strong>Long InnoDB History for Aurora MySQL-Compatible engines</strong> – Triggered when the InnoDB history list length becomes very large.</li> 
 <li><strong>Temporary tables created on disk for Aurora MySQL-Compatible engines</strong> – Triggered when the ratio of temporary tables created versus all temporary tables breaches a threshold.</li> 
 <li><strong>Idle In Transaction for&nbsp;Aurora PostgreSQL-Compatible engines </strong>–&nbsp;Triggered when sessions connected to the database are not performing active work, but can keep database resources blocked.</li> 
</ul> 
<p>To get started, navigate to the Amazon DevOps Guru Dashboard where you can see a summary of your system’s overall health, including ongoing proactive insights. In the following screen capture, the number three indicates that there are three ongoing proactive insights. Click on that number to see the listing of the corresponding Proactive Insights, which may include RDS or other Proactive Insights supported by Amazon DevOps Guru.</p> 
<div class="wp-caption aligncenter" id="attachment_14701" style="width: 710px;">
 <a href="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/02/25/DGR-Dashboard.png"><img alt="Amazon DevOps Guru Dashboard where you can see a summary of your system’s overall health, including ongoing proactive insights" class="wp-image-14701" height="254" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/02/25/DGR-Dashboard-1024x371.png" width="700" /></a>
 <p class="wp-caption-text" id="caption-attachment-14701">Figure 1. Amazon DevOps Guru Dashboard where you can see a summary of your system’s overall health, including ongoing proactive insights.</p>
</div> 
<p>Ongoing problems (including reactive and proactive insights) are also highlighted against your database instance on the Database list page in the Amazon RDS console.</p> 
<div class="wp-caption aligncenter" id="attachment_14695" style="width: 710px;">
 <a href="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/02/25/DGR-RDS.png"><img alt="Proactive and Reactive Insights are highlighted against your database instance on the Database list page in the Amazon RDS console" class="wp-image-14695" height="274" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/02/25/DGR-RDS-1024x401.png" width="700" /></a>
 <p class="wp-caption-text" id="caption-attachment-14695">Figure 2. Proactive and Reactive Insights are highlighted against your database instance on the Database list page in the Amazon RDS console.</p>
</div> 
<p>In the following sections, we will dive deep on these use cases of DevOps Guru for RDS Proactive Insights.</p> 
<h3>Long InnoDB History for Aurora MySQL-Compatible engines</h3> 
<p>The<strong> InnoDB history list</strong> is a global list of the undo logs for committed transactions. MySQL uses the history list to purge records and log pages when transactions no longer require the history. &nbsp;If the <strong>InnoDB history list</strong> length grows too large, indicating a large number of old row versions, queries and even the database shutdown process can become slower.</p> 
<p>DevOps Guru for RDS now detects when the history list length exceeds 1 million records and alerts users to close (either by commit or by rollback) any unnecessary long-running transactions before triggering database changes that involve a shutdown (this includes reboots and database version upgrades).</p> 
<p>From the <a href="https://us-east-1.console.aws.amazon.com/devops-guru/home?region=us-east-1" rel="noopener" target="_blank">DevOps Guru console</a>, navigate to <strong>Insights</strong>, choose <strong>Proactive</strong>, then choose “<strong>RDS InnoDB History List Length Anomalous” </strong>Proactive Insight with an ongoing status<strong>. </strong>You will notice that Proactive Insights provides an “<strong>Insight overview</strong>”, “<strong>Metrics</strong>” and “<strong>Recommendations</strong>”.</p> 
<p><strong>Insight overview</strong> provides you basic information on this insight. In our case, the history list for row changes increased significantly, which affects query and shutdown performance.</p> 
<div class="wp-caption aligncenter" id="attachment_14697" style="width: 710px;">
 <a href="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/02/25/Long-InnoDB-History-Insights-overview.jpg"><img alt="Long InnoDB History for Aurora MySQL-Compatible engines Insight overview" class="wp-image-14697" height="272" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/02/25/Long-InnoDB-History-Insights-overview.jpg" width="700" /></a>
 <p class="wp-caption-text" id="caption-attachment-14697">Figure 3. Long InnoDB History for Aurora MySQL-Compatible engines Insight overview.</p>
</div> 
<p>The <strong>Metrics</strong> panel gives you a graphical representation of the history list length and the timeline, allowing you to correlate it with any anomalous application activity that may have occurred during this window.</p> 
<div class="wp-caption aligncenter" id="attachment_14698" style="width: 710px;">
 <a href="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/02/25/Long-InnoDB-History-Metrics.png"><img alt="Long InnoDB History for Aurora MySQL-Compatible engines Metrics panel" class="wp-image-14698" height="256" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/02/25/Long-InnoDB-History-Metrics-1024x374.png" width="700" /></a>
 <p class="wp-caption-text" id="caption-attachment-14698">Figure 4. Long InnoDB History for Aurora MySQL-Compatible engines Metrics panel.</p>
</div> 
<p>The <strong>Recommendations</strong> section suggests actions that you can take to mitigate this issue before it leads to a bigger problem. You will also notice the rationale behind the recommendation under the “Why is DevOps Guru recommending this?” column.</p> 
<div class="wp-caption aligncenter" id="attachment_14703" style="width: 710px;">
 <a href="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/02/25/Long-InnoDB-History-Recommendations.jpg"><img alt="The Recommendations section suggests actions that you can take to mitigate this issue before it leads to a bigger problem" class="wp-image-14703" height="250" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/02/25/Long-InnoDB-History-Recommendations.jpg" width="700" /></a>
 <p class="wp-caption-text" id="caption-attachment-14703">Figure 5. The Recommendations section suggests actions that you can take to mitigate this issue before it leads to a bigger problem.</p>
</div> 
<h3>Temporary tables created on disk&nbsp;for Aurora MySQL-Compatible engines</h3> 
<p>Sometimes it is necessary for the MySQL database to create an <a href="https://dev.mysql.com/doc/refman/8.0/en/internal-temporary-tables.html" rel="noopener" target="_blank">internal temporary table</a> while processing a query. An internal temporary table can be held in memory and processed by the TempTable or MEMORY storage engine, or stored on disk by the InnoDB storage engine. An increase of temporary tables created on disk instead of in memory can impact the database performance.</p> 
<p>DevOps Guru for RDS now monitors the rate at which the database creates temporary tables and the percentage of those temporary tables that use disk. When these values cross recommended levels over a given period of time, DevOps Guru for RDS creates an insight exposing this situation before it becomes critical.</p> 
<p>From the DevOps Guru console, navigate to Insights, choose Proactive, then choose “RDS Temporary Tables On Disk Anomalous<strong>” </strong>Proactive Insight with an ongoing status<strong>. </strong>You will notice this Proactive Insight provides an “Insight overview”, “Metrics” and “Recommendations”.</p> 
<p><strong>Insight overview</strong> provides you basic information on this insight. In our case, more than 58% of the total temporary tables created per second were using disk, with a sustained rate of two temporary tables on disk created every second, which indicates that query performance is degrading.</p> 
<div class="wp-caption aligncenter" id="attachment_14706" style="width: 710px;">
 <a href="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/02/26/Temporary-tables-created-on-disk-Insight-overview.jpg"><img alt="Temporary tables created on disk insight overview" class="wp-image-14706" height="320" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/02/26/Temporary-tables-created-on-disk-Insight-overview.jpg" width="700" /></a>
 <p class="wp-caption-text" id="caption-attachment-14706">Figure 6. Temporary tables created on disk insight overview.</p>
</div> 
<p>The <strong>Metrics</strong> panel shows you a graphical representation of the information specific for this insight. You will be presented with the evolution of the amount of temporary tables created on disk per second, the percentage of temporary tables on disk (out of the total number of database-created temporary tables), and of the overall rate at which the temporary tables are created (per second).</p> 
<div class="wp-caption aligncenter" id="attachment_14707" style="width: 710px;">
 <a href="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/02/26/RDS-Temporary-Tables-On-Disk-Anomalous-1.png"><img alt="Temporary tables created on disk evolution of the amount of temporary tables created on disk per second" class="wp-image-14707" height="367" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/02/26/RDS-Temporary-Tables-On-Disk-Anomalous-1-1024x536.png" width="700" /></a>
 <p class="wp-caption-text" id="caption-attachment-14707">Figure 7. Temporary tables created on disk – evolution of the amount of temporary tables created on disk per second.</p>
</div> 
<div class="wp-caption aligncenter" id="attachment_14708" style="width: 710px;">
 <a href="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/02/26/RDS-Temporary-Tables-On-Disk-Anomalous-2.png"><img alt="Temporary tables created on disk the percentage of temporary tables on disk (out of the total number of database-created temporary tables)" class="wp-image-14708" height="308" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/02/26/RDS-Temporary-Tables-On-Disk-Anomalous-2-1024x451.png" width="700" /></a>
 <p class="wp-caption-text" id="caption-attachment-14708">Figure 8. Temporary tables created on disk – the percentage of temporary tables on disk (out of the total number of database-created temporary tables).</p>
</div> 
<div class="wp-caption aligncenter" id="attachment_14709" style="width: 710px;">
 <a href="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/02/26/RDS-Temporary-Tables-On-Disk-Anomalous-3.png"><img alt="Temporary tables created on disk overall rate at which the temporary tables are created (per second)" class="wp-image-14709" height="296" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/02/26/RDS-Temporary-Tables-On-Disk-Anomalous-3-1024x433.png" width="700" /></a>
 <p class="wp-caption-text" id="caption-attachment-14709">Figure 9. Temporary tables created on disk – overall rate at which the temporary tables are created (per second).</p>
</div> 
<p>The <strong>Recommendations</strong> section suggests actions to avoid this situation when possible, such as not using BLOB and TEXT data types, tuning <strong>tmp_table_size</strong> and <strong>max_heap_table_size</strong> database parameters, data set reduction, columns indexing and more.</p> 
<div class="wp-caption aligncenter" id="attachment_14711" style="width: 710px;">
 <a href="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/02/26/Temporary-tables-created-on-disk-recommendations.jpg"><img alt="Temporary tables created on disk actions to avoid this situation when possible, such as not using BLOB and TEXT data types, tuning tmp_table_size and max_heap_table_size database parameters, data set reduction, columns indexing and more" class="wp-image-14711" height="250" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/02/26/Temporary-tables-created-on-disk-recommendations.jpg" width="700" /></a>
 <p class="wp-caption-text" id="caption-attachment-14711">Figure 10. Temporary tables created on disk – actions to avoid this situation when possible, such as not using BLOB and TEXT data types, tuning tmp_table_size and max_heap_table_size database parameters, data set reduction, columns indexing and more.</p>
</div> 
<p>Additional explanations on this use case can be found by clicking on the “View troubleshooting doc” link.</p> 
<h3>Idle In Transaction for&nbsp;Aurora PostgreSQL-Compatible engines</h3> 
<p>A connection that has been&nbsp;<strong>idle in transaction</strong>&nbsp; for too long can impact performance by holding locks, blocking other queries, or by preventing <strong>VACUUM</strong> (including <strong>autovacuum</strong>) from cleaning up dead rows.<br /> PostgreSQL database requires periodic maintenance, which is known as vacuuming. <strong>Autovacuum</strong> in PostgreSQL automates the execution of <strong>VACUUM</strong> and <strong>ANALYZE</strong> commands. This process gathers the table statistics and deletes the dead rows. When vacuuming does not occur, this negatively impacts the database performance. It leads to an increase in table and index bloat (the disk space that was used by a table or index and is available for reuse by the database but has not been reclaimed), leads to stale statistics and can even end in transaction wraparound (when the number of unique transaction ids reaches its maximum of about two billion).</p> 
<p>DevOps Guru for RDS monitors the time spent by sessions in an Aurora PostgreSQL database in <strong>idle in transaction</strong> state and raises initially a warning notification, followed by an alarm notification if the <strong>idle in transaction</strong> state continues (the current thresholds are 1800 seconds for the warning and 3600 seconds for the alarm).</p> 
<p>From the DevOps Guru console, navigate to Insights, choose Proactive, then choose “RDS Idle In Transaction Max Time Anomalous<strong>” </strong>Proactive Insight with an ongoing status<strong>. </strong>You will notice this Proactive Insights provides an “Insight overview”, “Metrics” and “Recommendations”.</p> 
<p>In our case, a connection has been in “idle in transaction” state for more than 1800 seconds, which could impact the database performance.</p> 
<div class="wp-caption aligncenter" id="attachment_14716" style="width: 710px;">
 <a href="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/02/27/Idle-In-Transaction-insights-overview.jpg"><img alt="A connection has been in “idle in transaction” state for more than 1800 seconds, which could impact the database performance" class="wp-image-14716" height="250" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/02/27/Idle-In-Transaction-insights-overview.jpg" width="700" /></a>
 <p class="wp-caption-text" id="caption-attachment-14716">Figure 11. A connection has been in “idle in transaction” state for more than 1800 seconds, which could impact the database performance.</p>
</div> 
<p>The <strong>Metrics</strong> panel shows you a graphical representation of when the long-running “idle in transaction” connections started.</p> 
<div class="wp-caption aligncenter" id="attachment_14717" style="width: 710px;">
 <a href="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/02/27/Idle-In-Transaction-metrics.png"><img alt="The Metrics panel shows you a graphical representation of when the long-running “idle in transaction” connections started" class="wp-image-14717" height="431" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/02/27/Idle-In-Transaction-metrics.png" width="700" /></a>
 <p class="wp-caption-text" id="caption-attachment-14717">Figure 12. The Metrics panel shows you a graphical representation of when the long-running “idle in transaction” connections started.</p>
</div> 
<p>As with the other insights, recommended actions are listed and a troubleshooting doc is linked for even more details on this use case.</p> 
<div class="wp-caption aligncenter" id="attachment_14718" style="width: 710px;">
 <a href="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/02/27/Idle-In-Transaction-recommendations.jpg"><img alt="Recommended actions are listed and a troubleshooting doc is linked for even more details on this use case" class="wp-image-14718" height="247" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/02/27/Idle-In-Transaction-recommendations.jpg" width="700" /></a>
 <p class="wp-caption-text" id="caption-attachment-14718">Figure 13. Recommended actions are listed and a troubleshooting doc is linked for even more details on this use case.</p>
</div> 
<h2>Conclusion</h2> 
<p>With Proactive Insights, DevOpsGuru for RDS enhances its abilities to help you monitor your databases by notifying you about potential operational issues, before they become bigger problems down the road. To get started, you need to ensure that you have <a href="https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_PerfInsights.Enabling.html" rel="noopener" target="_blank">enabled Performance Insights</a> on the database instance(s) you want monitored, as well as ensure and confirm that DevOps Guru is <a href="https://docs.aws.amazon.com/devops-guru/latest/userguide/working-with-rds.enabling.html" rel="noopener" target="_blank">enabled to monitor those instances</a> (for example by enabling it at account level, by monitoring specific CloudFormation stacks or by using AWS tags for specific Aurora resources). Proactive Insights is available in all regions where DevOps Guru for RDS is <a href="https://aws.amazon.com/devops-guru/faqs/#:~:text=Q%3A%20In%20which%20AWS%20regions%20is%20Amazon%20DevOps%20Guru%20available%3F" rel="noopener" target="_blank">supported</a>. To learn more about Proactive Insights, join us for a free hands-on Immersion Day (available in three time zones) on <a href="https://aiopsonawsamer.splashthat.com/">March 15th</a> or <a href="https://boostyourapplicationsamer.splashthat.com/">April 12th</a>.</p> 
<p><strong>About the authors:</strong></p> 
<footer> 
 <div class="blog-author-box"> 
  <div class="blog-author-image">
   <img alt="" class="aligncenter size-full wp-image-11636" height="160" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/02/27/kkdaws.jpg" width="120" />
  </div> 
  <h3 class="lb-h4">Kishore Dhamodaran</h3> 
  <p>Kishore Dhamodaran is a Senior Solutions Architect at AWS.</p> 
 </div> 
</footer> 
<footer> 
 <div class="blog-author-box"> 
  <div class="blog-author-image">
   <img alt="" class="aligncenter wp-image-11636" height="160" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/02/27/ralconst.jpg" width="120" />
  </div> 
  <h3 class="lb-h4">Raluca Constantin</h3> 
  <p>Raluca Constantin is a Senior Database Engineer with the Relational Database Services (RDS) team at Amazon Web Services. She has 16 years of experience in the databases world. She enjoys travels, hikes, arts and is a proud mother of a 12y old daughter and a 7y old son.</p> 
 </div> 
</footer> 
<footer> 
 <div class="blog-author-box"> 
  <div class="blog-author-image">
   <img alt="" class="aligncenter wp-image-11636" height="160" src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2023/02/27/Jonathan-Vogel.jpg" width="120" />
  </div> 
  <h3 class="lb-h4">Jonathan Vogel</h3> 
  <p>Jonathan is a Developer Advocate at AWS. He was a DevOps Specialist Solutions Architect at AWS for two years prior to taking on the Developer Advocate role. Prior to AWS, he practiced professional software development for over a decade. Jonathan enjoys music, birding and climbing rocks.</p> 
 </div> 
</footer>
