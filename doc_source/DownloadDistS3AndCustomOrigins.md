# Using various origins with CloudFront distributions<a name="DownloadDistS3AndCustomOrigins"></a>

When you create a distribution, you specify the *origin* where CloudFront sends requests for the files\. You can use several different kinds of origins with CloudFront\. For example, you can use an Amazon S3 bucket, a MediaStore container, a MediaPackage channel, an Application Load Balancer, or an AWS Lambda function URL\.

**Topics**
+ [Using an Amazon S3 bucket](#using-s3-as-origin)
+ [Using a MediaStore container or a MediaPackage channel](#concept_AWS_Media)
+ [Using an Application Load Balancer](#concept_elb_origin)
+ [Using a Lambda function URL](#concept_lambda_function_url)
+ [Using Amazon EC2 \(or another custom origin\)](#concept_CustomOrigin)
+ [Using CloudFront origin groups](#concept_origin_groups)

## Using an Amazon S3 bucket<a name="using-s3-as-origin"></a>

The following topics describe the different ways that you can use an Amazon S3 bucket as the origin for a CloudFront distribution\.

**Topics**
+ [Using a standard Amazon S3 bucket](#concept_S3Origin)
+ [Using an Amazon S3 bucket that's configured as a website endpoint](#concept_S3Origin_website)
+ [Adding CloudFront to an existing Amazon S3 bucket](#adding-cloudfront-to-s3)
+ [Moving an Amazon S3 bucket to a different AWS Region](#move-s3-bucket-different-region)

### Using a standard Amazon S3 bucket<a name="concept_S3Origin"></a>

When you use Amazon S3 as an origin for your distribution, you place the objects that you want CloudFront to deliver in an Amazon S3 bucket\. You can use any method that is supported by Amazon S3 to get your objects into Amazon S3\. For example, you can use the Amazon S3 console or API, or a third\-party tool\. You can create a hierarchy in your bucket to store the objects, just as you would with any other standard Amazon S3 bucket\.

Using an existing Amazon S3 bucket as your CloudFront origin server doesn't change the bucket in any way; you can still use it as you normally would to store and access Amazon S3 objects at the standard Amazon S3 price\. You incur regular Amazon S3 charges for storing the objects in the bucket\. For more information about the charges to use CloudFront, see [Amazon CloudFront Pricing](https://aws.amazon.com/cloudfront/pricing/)\. For more information about using CloudFront with an existing S3 bucket, see [Adding CloudFront to an existing Amazon S3 bucket](#adding-cloudfront-to-s3)\.

**Important**  
For your bucket to work with CloudFront, the name must conform to DNS naming requirements\. For more information, go to [Bucket naming rules](https://docs.aws.amazon.com/AmazonS3/latest/userguide/bucketnamingrules.html) in the *Amazon Simple Storage Service User Guide*\.

When you specify an Amazon S3 bucket as an origin for CloudFront, we recommend that you use the following format:

`bucket-name.s3.region.amazonaws.com`

When you specify the bucket name in this format, you can use the following CloudFront features:
+ Configure CloudFront to communicate with your Amazon S3 bucket using SSL/TLS\. For more information, see [Using HTTPS with CloudFront](using-https.md)\.
+ Use an origin access control to require that viewers access your content using CloudFront URLs, not by using Amazon S3 URLs\. For more information, see [Restricting access to an Amazon S3 origin](private-content-restricting-access-to-s3.md)\.
+ Update the content of your bucket by submitting `POST` and `PUT` requests to CloudFront\. For more information, see [HTTP methods](RequestAndResponseBehaviorS3Origin.md#RequestS3HTTPMethods) in the topic [How CloudFront processes and forwards requests to your Amazon S3 origin](RequestAndResponseBehaviorS3Origin.md#RequestBehaviorS3Origin)\.

Don't specify the bucket using the following formats:
+ The Amazon S3 path style: `s3.amazonaws.com/bucket-name`
+ The Amazon S3 CNAME

### Using an Amazon S3 bucket that's configured as a website endpoint<a name="concept_S3Origin_website"></a>

You can use an Amazon S3 bucket that's configured as a website endpoint as a custom origin with CloudFront\. When you configure your CloudFront distribution, for the origin, enter the Amazon S3 static website hosting endpoint for your bucket\. This value appears in the [Amazon S3 console](https://console.aws.amazon.com/s3/), on the **Properties** tab, in the **Static website hosting** pane\. For example:

`http://bucket-name.s3-website-region.amazonaws.com`

For more information about specifying Amazon S3 static website endpoints, see [Website endpoints](https://docs.aws.amazon.com/AmazonS3/latest/userguide/WebsiteEndpoints.html) in the *Amazon Simple Storage Service User Guide*\.

When you specify the bucket name in this format as your origin, you can use Amazon S3 redirects and Amazon S3 custom error documents\. For more information, see [Configuring a custom error document](https://docs.aws.amazon.com/AmazonS3/latest/userguide/CustomErrorDocSupport.html) and [Configuring a redirect](https://docs.aws.amazon.com/AmazonS3/latest/userguide/how-to-page-redirect.html) in the *Amazon Simple Storage Service User Guide*\. \(CloudFront also provides custom error pages\. For more information, see [Creating a custom error page for specific HTTP status codes](GeneratingCustomErrorResponses.md#creating-custom-error-pages)\.\)

Using an Amazon S3 bucket as your CloudFront origin server doesn't change the bucket in any way\. You can still use it as you normally would and you incur regular Amazon S3 charges\. For more information about the charges to use CloudFront, see [Amazon CloudFront Pricing](https://aws.amazon.com/cloudfront/pricing/)\.

**Note**  
If you use the CloudFront API to create your distribution with an Amazon S3 bucket that is configured as a website endpoint, you must configure it by using `CustomOriginConfig`, even though the website is hosted in an Amazon S3 bucket\. For more information about creating distributions by using the CloudFront API, see [CreateDistribution](https://docs.aws.amazon.com/cloudfront/latest/APIReference/API_CreateDistribution.html) in the *Amazon CloudFront API Reference*\.

### Adding CloudFront to an existing Amazon S3 bucket<a name="adding-cloudfront-to-s3"></a>

If you store your objects in an Amazon S3 bucket, you can either have users get your objects directly from S3, or you can configure CloudFront to get your objects from S3 and then distribute them to your users\. Using CloudFront can be more cost effective if your users access your objects frequently because, at higher usage, the price for CloudFront data transfer is lower than the price for Amazon S3 data transfer\. In addition, downloads are faster with CloudFront than with Amazon S3 alone because your objects are stored closer to your users\.

**Note**  
If you want CloudFront to respect Amazon S3 cross\-origin resource sharing settings, configure CloudFront to forward the `Origin` header to Amazon S3\. For more information, see [Caching content based on request headers](header-caching.md)\.

If you currently distribute content directly from your Amazon S3 bucket using your own domain name \(such as example\.com\) instead of the domain name of your Amazon S3 bucket \(such as DOC\-EXAMPLE\-BUCKET\.s3\.us\-west\-2\.amazonaws\.com\), you can add CloudFront with no disruption by using the following procedure\.<a name="migrate-s3-to-cloudfront-process"></a>

**To add CloudFront when you're already distributing your content from Amazon S3**

1. Create a CloudFront distribution\. For more information, see [Steps for creating a distribution \(overview\)](distribution-web-creating.md)\.

   When you create the distribution, specify the name of your Amazon S3 bucket as the origin server\.
**Important**  
For your bucket to work with CloudFront, the name must conform to DNS naming requirements\. For more information, go to [Bucket naming rules](https://docs.aws.amazon.com/AmazonS3/latest/userguide/bucketnamingrules.html) in the *Amazon Simple Storage Service User Guide*\.

   If you're using a CNAME with Amazon S3, specify the CNAME for your distribution, too\.

1. Create a test webpage that contains links to publicly readable objects in your Amazon S3 bucket, and test the links\. For this initial test, use the CloudFront domain name of your distribution in the object URLs, for example, `https://d111111abcdef8.cloudfront.net/images/image.jpg`\.

   For more information about the format of CloudFront URLs, see [Customizing the URL format for files in CloudFront](LinkFormat.md)\.

1. If you're using Amazon S3 CNAMEs, your application uses your domain name \(for example, example\.com\) to reference the objects in your Amazon S3 bucket instead of using the name of your bucket \(for example, DOC\-EXAMPLE\-BUCKET\.s3\.amazonaws\.com\)\. To continue using your domain name to reference objects instead of using the CloudFront domain name for your distribution \(for example, d111111abcdef8\.cloudfront\.net\), you need to update your settings with your DNS service provider\.

   For Amazon S3 CNAMEs to work, your DNS service provider must have a CNAME resource record set for your domain that currently routes queries for the domain to your Amazon S3 bucket\. For example, if a user requests this object:

   `https://example.com/images/image.jpg`

   The request is automatically rerouted, and the user sees this object:

   `https://DOC-EXAMPLE-BUCKET.s3.amazonaws.com/images/image.jpg`

   To route queries to your CloudFront distribution instead of your Amazon S3 bucket, you need to use the method provided by your DNS service provider to update the CNAME resource record set for your domain\. This updated CNAME record redirects DNS queries from your domain to the CloudFront domain name for your distribution\. For more information, see the documentation provided by your DNS service provider\.
**Note**  
If you're using Route 53 as your DNS service, you can use either a CNAME resource record set or an alias resource record set\. For information about editing resource record sets, see [Editing records](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resource-record-sets-editing.html)\. For information about alias resource record sets, see [Choosing between alias and non\-alias records](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resource-record-sets-choosing-alias-non-alias.html)\. Both topics are in the *Amazon Route 53 Developer Guide*\.

   For more information about using CNAMEs with CloudFront, see [Using custom URLs by adding alternate domain names \(CNAMEs\)](CNAMEs.md)\.

   After you update the CNAME resource record set, it can take up to 72 hours for the change to propagate throughout the DNS system, although it usually happens faster\. During this time, some requests for your content will continue to be routed to your Amazon S3 bucket, and others will be routed to CloudFront\.

### Moving an Amazon S3 bucket to a different AWS Region<a name="move-s3-bucket-different-region"></a>

If you're using Amazon S3 as the origin for a CloudFront distribution and you move the bucket to a different AWS Region, CloudFront can take up to an hour to update its records to use the new Region when both of the following are true:
+ You're using a CloudFront origin access identity \(OAI\) to restrict access to the bucket\.
+ You move the bucket to an Amazon S3 Region that requires Signature Version 4 for authentication\.

When you're using OAIs, CloudFront uses the Region \(among other values\) to calculate the signature that it uses to request objects from your bucket\. For more information about OAIs, see [Using an origin access identity \(legacy, not recommended\)](private-content-restricting-access-to-s3.md#private-content-restricting-access-to-s3-oai)\. For a list of AWS Regions that support Signature Version 2, see [Signature Version 2 signing process](https://docs.aws.amazon.com/general/latest/gr/signature-version-2.html) in the *Amazon Web Services General Reference*\.

To force a faster update to CloudFront's records, you can update your CloudFront distribution, for example, by updating the **Description** field on the **General** tab in the CloudFront console\. When you update a distribution, CloudFront immediately checks the Region that your bucket is in\. Propagation of the change to all edge locations should take only a few minutes\.

## Using a MediaStore container or a MediaPackage channel<a name="concept_AWS_Media"></a>

To stream video using CloudFront, you can set up an Amazon S3 bucket that is configured as a MediaStore container, or create a channel and endpoints with MediaPackage\. Then you create and configure a distribution in CloudFront to stream the video\.

For more information and step\-by\-step instructions, see the following topics:
+ [Serving video using AWS Elemental MediaStore as the origin](live-streaming.md#video-streaming-mediastore)
+ [Serving live video formatted with AWS Elemental MediaPackage](live-streaming.md#live-streaming-with-mediapackage)

## Using an Application Load Balancer<a name="concept_elb_origin"></a>

If your origin is one or more HTTP servers \(web servers\) hosted on one or more Amazon EC2 instances, you can use an Application Load Balancer to distribute traffic to the instances\. For more information about using an Application Load Balancer as your origin for CloudFront, including how to make sure that viewers can only access your web servers through CloudFront and not by accessing the load balancer directly, see [Restricting access to Application Load Balancers](restrict-access-to-load-balancer.md)\.

## Using a Lambda function URL<a name="concept_lambda_function_url"></a>

A [Lambda function URL](https://docs.aws.amazon.com/lambda/latest/dg/lambda-urls.html) is a dedicated HTTPS endpoint for an AWS Lambda function\. You can use a Lambda function URL to build a serverless web application entirely within AWS Lambda\. You can invoke the Lambda web application directly through the function URL, with no need to integrate with API Gateway or an Application Load Balancer\.

If you build a serverless web application using Lambda functions with function URLs, you can add CloudFront to get the following benefits:
+ Speed up your application by caching content closer to viewers
+ Use a custom domain name for your web application
+ Route different URL paths to different Lambda functions using CloudFront cache behaviors
+ Block specific requests using CloudFront geographic restrictions or AWS WAF \(or both\)
+ Use AWS WAF with CloudFront to help protect your application from malicious bots, help prevent common application exploits, and enhance protection from DDoS attacks

To use a Lambda function URL as the origin for a CloudFront distribution, specify the full domain name of the Lambda function URL as the origin domain\. A Lambda function URL domain name uses the following format:

`function-URL-ID.lambda-url.AWS-Region.on.aws`

When you use a Lambda function URL as the origin for a CloudFront distribution, you must make sure that the function URL is publicly accessible\. To do this, you set the `AuthType` parameter of the function URL to `NONE` and allow the `lambda:InvokeFunctionUrl` permission in a resource\-based policy\. For more information, see [Using the NONE AuthType](https://docs.aws.amazon.com/lambda/latest/dg/urls-auth.html#urls-auth-none) in the *AWS Lambda Developer Guide*\. However, you can also [add a custom origin header](add-origin-custom-headers.md) to the requests that CloudFront sends to the origin, and write function code to return an error response if the header is not present in the request\. This helps to make sure that users can only access your web application through CloudFront, not directly using the Lambda function URL\.

For more information about Lambda function URLs, see the following topics in the *AWS Lambda Developer Guide*:
+ [Lambda function URLs](https://docs.aws.amazon.com/lambda/latest/dg/lambda-urls.html) – A general overview of the Lambda function URLs feature
+ [Invoking Lambda function URLs](https://docs.aws.amazon.com/lambda/latest/dg/urls-invocation.html) – Includes details about the request and response payloads to use for coding your serverless web application

## Using Amazon EC2 \(or another custom origin\)<a name="concept_CustomOrigin"></a>

A custom origin is an HTTP server, for example, a web server\. The HTTP server can be an Amazon EC2 instance or an HTTP server that you host somewhere else\. An Amazon S3 origin configured as a website endpoint is also considered a custom origin\.

When you use your own HTTP server as a custom origin, you specify the DNS name of the server, along with the HTTP and HTTPS ports and the protocol that you want CloudFront to use when fetching objects from your origin\.

Most CloudFront features are supported when you use a custom origin with the exception of private content\. Although you can use a signed URL to distribute content from a custom origin, for CloudFront to access the custom origin, the origin must remain publicly accessible\. For more information, see [Serving private content with signed URLs and signed cookies](PrivateContent.md)\.

Follow these guidelines for using Amazon EC2 instances and other custom origins with CloudFront\.
+ Host and serve the same content on all servers that are serving content for the same CloudFront origin\. For more information, see [Origin settings](distribution-web-values-specify.md#DownloadDistValuesOrigin) in the [Values that you specify when you create or update a distribution](distribution-web-values-specify.md) topic\.
+ Log the `X-Amz-Cf-Id` header entries on all servers in case you need AWS Support or CloudFront to use this value for debugging\.
+ Restrict requests to the HTTP and HTTPS ports that your custom origin listens on\.
+ Synchronize the clocks of all servers in your implementation\. Note that CloudFront uses Coordinated Universal Time \(UTC\) for signed URLs and signed cookies, for logs, and reports\. In addition, if you monitor CloudFront activity using CloudWatch metrics, note that CloudWatch also uses UTC\.
+ Use redundant servers to handle failures\.
+ For information about using a custom origin to serve private content, see [ Restricting access to files on custom origins](private-content-overview.md#forward-custom-headers-restrict-access)\.
+ For information about request and response behavior and about supported HTTP status codes, see [Request and response behavior](RequestAndResponseBehavior.md)\.

If you use Amazon EC2 for a custom origin, we recommend that you do the following:
+ Use an Amazon Machine Image that automatically installs the software for a web server\. For more information, see the [Amazon EC2 documentation](https://docs.aws.amazon.com/ec2/index.html)\.
+ Use an Elastic Load Balancing load balancer to handle traffic across multiple Amazon EC2 instances and to isolate your application from changes to Amazon EC2 instances\. For example, if you use a load balancer, you can add and delete Amazon EC2 instances without changing your application\. For more information, see the [Elastic Load Balancing documentation](https://docs.aws.amazon.com/elasticloadbalancing/index.html)\.
+ When you create your CloudFront distribution, specify the URL of the load balancer for the domain name of your origin server\. For more information, see [Creating a distribution](distribution-web-creating-console.md)\.

## Using CloudFront origin groups<a name="concept_origin_groups"></a>

You can specify an origin group for your CloudFront origin if, for example, you want to configure origin failover for scenarios when you need high availability\. Use origin failover to designate a primary origin for CloudFront plus a second origin that CloudFront automatically switches to when the primary origin returns specific HTTP status code failure responses\.

For more information, including the steps for setting up an origin group, see [Optimizing high availability with CloudFront origin failover](high_availability_origin_failover.md)\.